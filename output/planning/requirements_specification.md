# Requirements Specification: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 2.0
- **Date:** 2026-10-20

## Changelog
| Version | Date | Change Description |
|---|---|---|
| 1.0 | 2026-10-17 | Initial requirements baseline linking functional and non-functional specifications to WBS and Product Backlog |
| 2.0 | 2026-10-20 | Baseline planning re-issue. Requirements REQ-001 to REQ-016 are retained with their IDs, statements, and priorities. Added the Requirements Traceability Matrix to WBS 1.1 work packages and backlog stories, traced each requirement to its Milestone, and added requirements for the charter's in-scope items and non-functional items not covered in v1.0 (REQ-017 to REQ-024). Added Assumptions and Open Gaps. Version bumped from 1.0 to 2.0 because the baseline now includes charter requirements that were not previously specified (scope of the requirements baseline changed). |

## Assumptions
- Source materials are the Project Charter v1.0 (2026-10-17), Team Charter v1.0, WBS v1.1, Product Backlog v1.1, and Schedule Model v1.1 (all 2026-10-20 unless noted).
- The charter's stated requirement range "REQ-001 through REQ-038" is not fully reproduced in the source materials. Only REQ-001 to REQ-016 are defined in v1.0. Requirements REQ-017 to REQ-024 below are derived from charter in-scope items and constraints. Charter items REQ-021 to REQ-038 are not available as text and are recorded as an open gap.
- Numeric thresholds not stated in the source (for example, the maximum allowed error payload size, the target startup window for the container health check, and the audit log retention period) are not invented here. They are recorded as <UNKNOWN> and listed as open gaps.
- Story points are relative estimates and are not converted to hours.
- Owner roles are taken from the WBS. No named individuals are assigned.
- No scope change request was received during baseline planning.
- Requirements with the verification method "Analysis" or "Demonstration" are verified by the stated method and are not covered by unit tests alone.

## 1. Functional Requirements

### REQ-001: Hierarchical Location Management
- **Statement:** The system shall provide RESTful CRUD and lifecycle management for hierarchical warehouse locations spanning `warehouse` > `zone` > `aisle` > `rack` > `shelf` > `bin`.
- **Priority:** Must Have
- **Source:** US-101; Charter In Scope (Hierarchical Warehouse Locations)
- **Milestone:** 2
- **Acceptance Criteria:**
  - **Given** an authenticated user with `admin` role,
  - **When** a POST request is submitted to create a location node with valid parent hierarchy references,
  - **Then** the system shall persist the node, assign a unique location ID, and return HTTP 201 Created with resource representation.
- **Verification Method:** Test

### REQ-002: Location Capacity Validation
- **Statement:** The system shall track and enforce dimension and weight capacity constraints on warehouse locations.
- **Priority:** Should Have
- **Source:** US-102
- **Milestone:** 2
- **Acceptance Criteria:**
  - **Given** a location with defined maximum weight and volume constraints,
  - **When** an operation attempts to assign containers or stock exceeding these capacities,
  - **Then** the system shall reject the transaction and return HTTP 422 Unprocessable Entity with an RFC 7807 problem detail response.
- **Verification Method:** Test

### REQ-003: Container Registration & SSCC Tracking
- **Statement:** The system shall allow operators to register containers with SSCC/barcodes, weight, and dimension constraints.
- **Priority:** Must Have
- **Source:** US-103; Charter In Scope (Container Management)
- **Milestone:** 2
- **Acceptance Criteria:**
  - **Given** an operator with valid credentials,
  - **When** a container is registered with a unique SSCC barcode and physical limits,
  - **Then** the container record is successfully created and queryable by its barcode identifier.
- **Verification Method:** Test

### REQ-004: Container Nesting Operations
- **Statement:** The system shall support hierarchical container nesting (e.g., boxes inside pallets) while maintaining structural integrity and validating physical constraints.
- **Priority:** Must Have
- **Source:** US-104; Charter In Scope (Container Management, nesting)
- **Milestone:** 2
- **Acceptance Criteria:**
  - **Given** multiple active child containers and a parent container,
  - **When** a nested assignment request is processed,
  - **Then** child containers are securely linked to the parent container, and aggregate weights/dimensions are verified against parent limits.
- **Verification Method:** Test

### REQ-005: Stock SKU Registration and Status Handling
- **Statement:** The system shall register incoming stock SKUs with status handling (e.g., available, quarantined, damaged) and batch/expiry tracking.
- **Priority:** Must Have
- **Source:** US-201; Charter In Scope (Stock & Inventory Operations)
- **Milestone:** 3
- **Acceptance Criteria:**
  - **Given** validated external SKU references and batch details,
  - **When** stock is registered into a location or container,
  - **Then** inventory records are established with the correct status, batch identifier, and expiry timestamp.
- **Verification Method:** Test

### REQ-006: Stock Movement & Reason Codes
- **Statement:** The system shall execute full and partial stock movements between locations and containers using mandatory reason codes.
- **Priority:** Must Have
- **Source:** US-202; Charter In Scope (full/partial movements, reason codes)
- **Milestone:** 3
- **Acceptance Criteria:**
  - **Given** existing stock units in a source container or location,
  - **When** a movement request is executed with a valid reason code (e.g., `CYCLE_COUNT`, `PUTAWAY`),
  - **Then** stock quantities are updated atomically and movement events are recorded.
- **Verification Method:** Test

### REQ-007: Advanced Stock Transformations (Split, Merge, Group, Ungroup)
- **Statement:** The system shall support splitting, merging, grouping, and ungrouping stock items via atomic transactions without data discrepancy.
- **Priority:** Should Have
- **Source:** US-203; Charter In Scope (split, merge, group, ungroup)
- **Milestone:** 3
- **Acceptance Criteria:**
  - **Given** active stock inventory batches,
  - **When** a split or merge transformation API is invoked,
  - **Then** inventory balances reconcile precisely to zero net discrepancy across source and destination records.
- **Verification Method:** Test

### REQ-008: Immutable Audit Trail & Movement Log
- **Statement:** The system shall record every inventory movement, transfer, and adjustment into an immutable audit log.
- **Priority:** Must Have
- **Source:** US-204; Charter In Scope (Audit Trail & Traceability)
- **Milestone:** 3
- **Acceptance Criteria:**
  - **Given** any successful mutating inventory operation,
  - **When** the transaction commits,
  - **Then** an immutable audit entry capturing timestamp, user, action type, source, target, and delta is written to the audit log table.
- **Verification Method:** Inspection & Test

### REQ-017: Location Lifecycle Deactivation
- **Statement:** The system shall deactivate warehouse locations without physically deleting them, and shall reject deactivation of a location that still holds stock or containers.
- **Priority:** Must Have
- **Source:** US-101 (deactivate); Charter In Scope (lifecycle management)
- **Milestone:** 2
- **Acceptance Criteria:**
  - **Given** a location that contains one or more stock units or containers,
  - **When** a deactivation request is submitted for that location,
  - **Then** the system shall reject the request with HTTP 409 Conflict (or HTTP 422, to be confirmed) and an RFC 7807 problem detail response, and the location record shall remain unchanged.
  - **Given** a location that holds no stock or containers,
  - **When** a deactivation request is submitted,
  - **Then** the location status is set to inactive and the record is retained.
- **Verification Method:** Test

### REQ-018: Container Nesting Cycle Prevention
- **Statement:** The system shall reject any container nesting assignment that would create a cycle or make a container a child of its own descendant.
- **Priority:** Must Have
- **Source:** US-104; Charter Success Criteria (zero orphaned containers)
- **Milestone:** 2
- **Acceptance Criteria:**
  - **Given** container A contains container B,
  - **When** a request is submitted to nest container A inside container B,
  - **Then** the system shall reject the request with HTTP 422 and an RFC 7807 problem detail response, and no container relationship shall change.
- **Verification Method:** Test

### REQ-019: Stock Non-Negative Quantity Constraint
- **Statement:** The system shall prevent any stock quantity from becoming negative as the result of any movement, split, merge, or adjustment.
- **Priority:** Must Have
- **Source:** Charter Success Criteria (zero negative inventory); WBS 1.1.1 (schema constraints)
- **Milestone:** 3
- **Acceptance Criteria:**
  - **Given** a stock record with quantity Q,
  - **When** a movement or transformation requests a removal of quantity greater than Q,
  - **Then** the system shall reject the operation with HTTP 422 and an RFC 7807 problem detail response, and the quantity shall remain Q.
- **Verification Method:** Test

### REQ-020: Orphan Prevention for Containers and Stock
- **Statement:** The system shall prevent the creation of containers or stock records that reference a non-existent location, container, or stock parent, and shall prevent deletion of such parents while dependent records exist.
- **Priority:** Must Have
- **Source:** Charter Success Criteria (zero orphaned containers); WBS 1.1.1 (foreign keys)
- **Milestone:** 2
- **Acceptance Criteria:**
  - **Given** a request that references a non-existent parent location or container ID,
  - **When** the create request is processed,
  - **Then** the system shall reject it with HTTP 422 and an RFC 7807 problem detail response, and no record is written.
- **Verification Method:** Test

### REQ-021: Immutable Audit Log Write Protection
- **Statement:** The system shall not expose any API operation or database privilege that updates or deletes audit log entries.
- **Priority:** Must Have
- **Source:** US-204; Charter In Scope (immutable movement log)
- **Milestone:** 3
- **Acceptance Criteria:**
  - **Given** any authenticated user of any role,
  - **When** a PUT, PATCH, or DELETE request targets an audit log resource,
  - **Then** the system shall return HTTP 405 Method Not Allowed (or HTTP 403 Forbidden) with an RFC 7807 problem detail response.
- **Verification Method:** Inspection & Test

### REQ-022: Audit Trail Query Access
- **Statement:** The system shall allow users with the `supervisor` or `admin` role to query audit log entries filtered by stock identifier, location identifier, or time range.
- **Priority:** Should Have
- **Source:** US-204; Charter In Scope (Audit Trail & Traceability)
- **Milestone:** 3
- **Acceptance Criteria:**
  - **Given** an authenticated user with `supervisor` role,
  - **When** a GET request is submitted with a stock identifier filter,
  - **Then** the system shall return HTTP 200 OK with only the audit entries matching that identifier.
- **Verification Method:** Test

## 2. Non-Functional Requirements

### 2.1 Performance

### REQ-009: ACID Transaction Integrity
- **Statement:** All inventory, container, and location mutating operations shall execute within strict ACID database transactions to prevent orphaned records or negative inventory.
- **Priority:** Must Have
- **Source:** Milestone 1 / WBS 1.1; Charter Section 2 (ACID transactions)
- **Milestone:** 1 (schema), 2, 3
- **Acceptance Criteria:**
  - **Given** a multi-step inventory operation encountering a failure mid-transaction,
  - **When** the exception occurs,
  - **Then** the database performs a complete rollback leaving state entirely unmodified.
- **Verification Method:** Test

### REQ-010: Optimistic Concurrency Control
- **Statement:** The system shall enforce optimistic locking via ETag and version headers on all mutating resources to eliminate race conditions.
- **Priority:** Must Have
- **Source:** US-301; Charter Section 2 (optimistic locking, ETag)
- **Milestone:** 4
- **Acceptance Criteria:**
  - **Given** two concurrent update requests modifying the same resource with identical initial ETag values,
  - **When** the second request arrives after the first has committed,
  - **Then** the second request is rejected with HTTP 409 Conflict.
- **Verification Method:** Test

### REQ-013: Performance Latency Targets
- **Statement:** The API shall achieve p95 latency of < 300ms for single-resource operations and < 1s for list queries over 100,000 records.
- **Priority:** Must Have
- **Source:** US-302; Charter Section 2 and REQ-021 to REQ-038 range (performance targets)
- **Milestone:** 4
- **Acceptance Criteria:**
  - **Given** a database seeded with 100,000 inventory records,
  - **When** load testing executes list queries concurrently at target throughput,
  - **Then** the p95 response time remains below 1,000 milliseconds.
- **Verification Method:** Analysis & Test

### REQ-023: Single-Resource Latency
- **Statement:** The API shall achieve p95 latency of < 300 ms for single-resource read and write operations.
- **Priority:** Must Have
- **Source:** US-302; Charter Section 3 (p95 latency targets)
- **Milestone:** 4
- **Acceptance Criteria:**
  - **Given** a database seeded with 100,000 inventory records,
  - **When** load testing executes single-resource GET, POST, PUT, and PATCH requests at target throughput,
  - **Then** the p95 response time for each operation type remains below 300 milliseconds.
- **Verification Method:** Analysis & Test

### 2.2 Security

### REQ-011: Role-Based Access Control (RBAC)
- **Statement:** The API shall secure all endpoints using JWT/OAuth2 bearer tokens and enforce role-based permissions (`admin`, `supervisor`, `operator`).
- **Priority:** Must Have
- **Source:** WBS 1.2.1, WBS 1.2.2 / Milestone 1; Charter Section 2 (JWT/OAuth2 RBAC)
- **Milestone:** 1
- **Acceptance Criteria:**
  - **Given** a user authenticated with `operator` role,
  - **When** attempting to access an administrative endpoint restricted to `admin`,
  - **Then** the system shall deny access with HTTP 403 Forbidden.
- **Verification Method:** Test

### REQ-024: Unauthenticated Request Rejection
- **Statement:** The system shall reject any request to a protected endpoint that lacks a valid, unexpired JWT/OAuth2 bearer token.
- **Priority:** Must Have
- **Source:** WBS 1.2.1; Charter Section 2 (JWT/OAuth2)
- **Milestone:** 1
- **Acceptance Criteria:**
  - **Given** a request without an Authorization header, or with an expired or malformed token,
  - **When** the request reaches a protected endpoint,
  - **Then** the system shall return HTTP 401 Unauthorized with an RFC 7807 problem detail response.
- **Verification Method:** Test

### 2.3 Reliability

### REQ-012: RFC 7807 Standard Error Handling
- **Statement:** All error responses shall adhere strictly to the RFC 7807 `application/problem+json` specification without exposing internal stack traces or database schemas.
- **Priority:** Must Have
- **Source:** WBS 1.3.1 / Milestone 1; Charter Section 5 (Constraints)
- **Milestone:** 1
- **Acceptance Criteria:**
  - **Given** any erroneous API request resulting in a 4xx or 5xx status,
  - **When** the error response is returned,
  - **Then** the body conforms to RFC 7807 containing `type`, `title`, `status`, `detail`, and `instance` keys, and contains no stack trace or database schema text.
- **Verification Method:** Inspection & Test

### REQ-025: Service Availability Health Check
- **Statement:** The system shall expose a health check endpoint that reports the status of the application and its database connection.
- **Priority:** Must Have
- **Source:** WBS 1.4.1; Charter Section 4 (Milestone 4 production readiness)
- **Milestone:** 1
- **Acceptance Criteria:**
  - **Given** the application is running with a reachable database,
  - **When** a GET request is submitted to the health endpoint,
  - **Then** the system shall return HTTP 200 OK with a status of healthy.
  - **Given** the database is unreachable,
  - **When** a GET request is submitted to the health endpoint,
  - **Then** the system shall return HTTP 503 Service Unavailable.
- **Verification Method:** Test

### 2.4 Usability

### REQ-014: Localization Support
- **Statement:** Error messages and metadata shall support multi-language output in English (EN), Spanish (ES), and Catalan (CA) via standard `Accept-Language` headers.
- **Priority:** Could Have
- **Source:** US-303; Charter Section 2 (EN/ES/CA localization)
- **Milestone:** 4
- **Acceptance Criteria:**
  - **Given** an error condition triggered with `Accept-Language: es` header,
  - **When** the error response is rendered,
  - **Then** the detail message is returned in Spanish.
- **Verification Method:** Test

### REQ-026: API Documentation Availability
- **Statement:** The system shall publish an OpenAPI specification describing all endpoints, request and response schemas, and error responses.
- **Priority:** Must Have
- **Source:** WBS 1.3.2; Team Charter Section 7 (DoD, OpenAPI documentation)
- **Milestone:** 1
- **Acceptance Criteria:**
  - **Given** the running application,
  - **When** a GET request is submitted to the OpenAPI document endpoint,
  - **Then** the system shall return a valid OpenAPI document that lists every implemented endpoint.
- **Verification Method:** Inspection & Test

### 2.5 Maintainability

### REQ-015: Automated Quality & Test Coverage Gates
- **Statement:** The automated CI pipeline shall enforce a minimum unit and integration test coverage threshold of >= 80%.
- **Priority:** Must Have
- **Source:** US-304 / Team Charter Section 2; WBS 1.4.2
- **Milestone:** 1 (gate established), 4 (threshold met)
- **Acceptance Criteria:**
  - **Given** a pull request containing code changes,
  - **When** the CI pipeline runs the test suite,
  - **Then** builds with test coverage below 80% are automatically failed and blocked from merging.
- **Verification Method:** Inspection & Test

### REQ-027: Database Migration Rollback
- **Statement:** The system shall provide versioned database migration scripts with a verified rollback path for every migration.
- **Priority:** Must Have
- **Source:** WBS 1.1.2; Charter Section 2 (Milestone 1 scope)
- **Milestone:** 1
- **Acceptance Criteria:**
  - **Given** a applied migration,
  - **When** the rollback procedure is executed against a test database,
  - **Then** the schema returns to the prior version and the rollback completes without error.
- **Verification Method:** Test

### 2.6 Compliance

### REQ-028: Audit Retention
- **Statement:** The system shall retain audit log entries for a retention period of <UNKNOWN> and shall not purge them before that period expires.
- **Priority:** Must Have
- **Source:** Charter Success Criteria (full inventory traceability); no retention period stated in source
- **Milestone:** 3
- **Acceptance Criteria:**
  - **Given** an audit entry older than <UNKNOWN> but within retention policy,
  - **When** a purge job runs,
  - **Then** the entry is not removed.
- **Verification Method:** Inspection

### REQ-029: Data Protection in Transit
- **Statement:** The API shall accept requests and send responses only over TLS-encrypted connections.
- **Priority:** Must Have
- **Source:** Charter Section 2 (Security); assumed standard practice, not stated explicitly in source
- **Milestone:** 4
- **Acceptance Criteria:**
  - **Given** a plain HTTP request to the API,
  - **When** the request is received,
  - **Then** the system shall not process the request and shall redirect or reject it.
- **Verification Method:** Inspection & Test

### 2.7 Deployment & Operability

### REQ-016: Containerized Deployment
- **Statement:** The application shall be fully containerized using multi-stage Docker builds and orchestrated via docker-compose with verified health checks.
- **Priority:** Must Have
- **Source:** WBS 1.4.1 / US-304; Charter Section 2 (Docker containerization)
- **Milestone:** 1 (artifacts), 4 (production image)
- **Acceptance Criteria:**
  - **Given** the production Dockerfile configuration,
  - **When** the container is built and launched with health check probes enabled,
  - **Then** the container reports a healthy status within the designated startup window of <UNKNOWN> seconds.
- **Verification Method:** Demonstration & Test

### REQ-030: Localized Error Metadata Fallback
- **Statement:** When a requested language is not supported, the system shall return error messages in English (EN).
- **Priority:** Could Have
- **Source:** US-303
- **Milestone:** 4
- **Acceptance Criteria:**
  - **Given** an error condition with `Accept-Language: fr`,
  - **When** the error response is rendered,
  - **Then** the detail message is returned in English.
- **Verification Method:** Test

## 3. Open Gaps
- Charter requirements REQ-021 to REQ-038 are referenced but not defined in the source materials. Only the ranges stated in the charter (ACID, ETag, RBAC, RFC 7807, p95 targets, CI coverage, Dockerized deployment) are reflected here. Sponsor or product owner confirmation is required.
- Charter ID collisions: REQ-021 in the charter is a charter-level requirement ID, while this specification uses REQ-021 for audit log write protection. Reconciliation with the charter numbering is required.
- Audit retention period (REQ-028) is <UNKNOWN>.
- Container health check startup window (REQ-016) is <UNKNOWN>.
- Deactivation rejection status code (REQ-017) is to be confirmed between 409 and 422.
- Feature 1.2.2 (Release 1), 2.1.2 (Release 2), and 3.1.2 (Release 3) are referenced in the backlog release plan but not defined. Requirements for them are not created.
- Story points are not converted to hours. Velocity is not stated.
- Charter in-scope item "Hierarchical Locations lifecycle" and "Stock Operations" are covered by REQ-001 to REQ-008 and REQ-017 to REQ-022. Compliance requirements beyond audit retention and TLS are not stated in the source.

## 4. Requirements Traceability Matrix

| Requirement ID | Requirement Description | Priority | Milestone | Source (Backlog / WBS / Charter) | Verification Method |
|---|---|---|---|---|---|
| REQ-001 | Location CRUD & Lifecycle Hierarchy | Must Have | 2 | US-101; Charter In Scope (Locations) | Test |
| REQ-002 | Location Capacity Validation | Should Have | 2 | US-102 | Test |
| REQ-003 | Container Registration & SSCC Tracking | Must Have | 2 | US-103; Charter In Scope (Containers) | Test |
| REQ-004 | Container Nesting & Constraints | Must Have | 2 | US-104; Charter In Scope (Containers) | Test |
| REQ-005 | SKU Registration & Status Handling | Must Have | 3 | US-201; Charter In Scope (Stock) | Test |
| REQ-006 | Stock Movement & Reason Codes | Must Have | 3 | US-202; Charter In Scope (Stock) | Test |
| REQ-007 | Advanced Stock Transformations | Should Have | 3 | US-203; Charter In Scope (Stock) | Test |
| REQ-008 | Immutable Audit Trail & Log | Must Have | 3 | US-204; Charter In Scope (Audit) | Inspection & Test |
| REQ-009 | ACID Transaction Integrity | Must Have | 1, 2, 3 | WBS 1.1.1, 1.1.2; Charter Sec. 2 | Test |
| REQ-010 | Optimistic Concurrency via ETag | Must Have | 4 | US-301; Charter Sec. 2 | Test |
| REQ-011 | RBAC Authorization | Must Have | 1 | WBS 1.2.1, 1.2.2; Charter Sec. 2 | Test |
| REQ-012 | RFC 7807 Error Handling | Must Have | 1 | WBS 1.3.1; Charter Sec. 5 | Inspection & Test |
| REQ-013 | Performance Latency (< 1s list, 100k) | Must Have | 4 | US-302; Charter Sec. 3 | Analysis & Test |
| REQ-014 | Localization EN/ES/CA | Could Have | 4 | US-303; Charter Sec. 2 | Test |
| REQ-015 | Automated CI Coverage (>= 80%) | Must Have | 1, 4 | US-304; WBS 1.4.2; Team Charter Sec. 2 | Inspection & Test |
| REQ-016 | Docker Containerization & Health Checks | Must Have | 1, 4 | WBS 1.4.1; US-304 | Demonstration & Test |
| REQ-017 | Location Deactivation Guard | Must Have | 2 | US-101 | Test |
| REQ-018 | Container Nesting Cycle Prevention | Must Have | 2 | US-104 | Test |
| REQ-019 | Stock Non-Negative Quantity | Must Have | 3 | WBS 1.1.1; Charter Success Criteria | Test |
| REQ-020 | Orphan Prevention | Must Have | 2 | WBS 1.1.1; Charter Success Criteria | Test |
| REQ-021 | Audit Log Immutability (write protection) | Must Have | 3 | US-204 | Inspection & Test |
| REQ-022 | Audit Trail Query Access | Should Have | 3 | US-204 | Test |
| REQ-023 | Single-Resource Latency (< 300 ms p95) | Must Have | 4 | US-302; Charter Sec. 3 | Analysis & Test |
| REQ-024 | Unauthenticated Request Rejection | Must Have | 1 | WBS 1.2.1 | Test |
| REQ-025 | Health Check Endpoint | Must Have | 1 | WBS 1.4.1 | Test |
| REQ-026 | OpenAPI Documentation Availability | Must Have | 1 | WBS 1.3.2; Team Charter Sec. 7 | Inspection & Test |
| REQ-027 | Migration Rollback | Must Have | 1 | WBS 1.1.2 | Test |
| REQ-028 | Audit Retention | Must Have | 3 | Charter Success Criteria | Inspection |
| REQ-029 | TLS in Transit | Must Have | 4 | Charter Sec. 2 (assumed practice) | Inspection & Test |
| REQ-030 | English Fallback for Unsupported Locale | Could Have | 4 | US-303 | Test |
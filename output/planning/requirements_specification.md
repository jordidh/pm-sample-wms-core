# Requirements Specification: Warehouse Inventory & Storage Management REST API (WMS Core)

| Field | Value |
| --- | --- |
| Document | Requirements Specification |
| Project Name | Warehouse Inventory & Storage Management REST API (WMS Core) |
| Current Version | 1.0 |
| Date | 2026-10-20 |

## Changelog

| Version | Date | Change Description |
| --- | --- | --- |
| 1.0 | 2026-10-20 | Initial version |

## 0. Purpose, Scope Basis and Assumptions

**Purpose:** Define verifiable functional and non-functional requirements for the WMS Core REST API, traced to the Project Charter (v1.0), Team Charter (v1.0), and the Work Breakdown Structure (v1.0).

**Scope basis:**
* In scope: Charter Section 2.1. Out of scope: Charter Section 2.2 (SKU master data, hardware drivers, ERP receiving/shipping orchestration).
* Requirement IDs REQ-001 to REQ-038 are taken from the Project Charter Section 2.1 and are not renumbered.
* Requirement statements are refined from the charter summaries. Where the charter gives no detail, the gap is recorded as an assumption (Section 0.1), not invented as fact.

**Assumptions and open items:**
* A1: SKUs are validated by an external master data source; the API stores SKU as an external string reference (Charter Section 6).
* A2: An external identity provider issuing JWT/OAuth2 tokens is available or will be supplied by the organization (Charter Section 6). Provider name is <UNKNOWN>.
* A3: 100k records is the planning-horizon volume, to be confirmed (Charter Section 6).
* A4: The three roles are `admin`, `supervisor`, and `operator` (Team Charter Section 8.2). Their permission matrix is defined in WBS 2.2.2 and is not yet baselined; the permissions in Section 2.6 are proposed.
* A5: Localization scope is EN, ES, and CA only (Charter Section 6).
* A6: Reason code catalog, weight/dimension limits, and capacity units are business-owned values not provided in the source inputs (<UNKNOWN>). Business Owner decides them (Team Charter Section 5).
* A7: Iteration cadence is two weeks, to be confirmed by the Project Manager.
* A8: Milestone target dates are <UNKNOWN>; they are set in WBS 1.1.4.
* A9: Error code names used in this document (`LOCATION_NOT_EMPTY`, `STOCK_INSUFFICIENT_QUANTITY`) are cited from the Team Charter as examples. The full catalog is defined in WBS 2.3.1.

**Priority scale:** MUST (required for milestone gate), SHOULD (required for release, deferrable only by change control), COULD (desirable).

**Verification methods:** Test (automated or manual test execution), Inspection (review of artifacts), Analysis (calculation, modelling, or static analysis), Demonstration (observed operation).

**Source keys:** PC = Project Charter v1.0; TC = Team Charter v1.0; WBS = Work Breakdown Structure v1.0 (work package code).

---

## 1. Functional Requirements

### 1.1 Hierarchical Warehouse Locations

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| REQ-001 | The system shall store warehouse locations in a multi-tier tree with levels warehouse > zone > aisle > rack > shelf > bin, and shall expose CRUD operations on `/locations`. | MUST | PC 2.1; WBS 2.1.1, 3.1.1 | Given a valid location payload with a parent of the correct level, When `POST /locations` is called by an `admin` or `supervisor`, Then the location is created and returned with HTTP 201 and a unique identifier. Given a child whose level is not directly below its parent, When `POST /locations` is called, Then the request is rejected with HTTP 422 and a RFC 7807 error. | Test |
| REQ-002 | The system shall allow reading, updating, and listing locations through `/locations` and `/locations/{id}`. | MUST | PC 2.1; WBS 3.1.1 | Given an existing location, When `GET /locations/{id}` is called, Then HTTP 200 is returned with the location representation and its ETag. Given `GET /locations` with filters, Then only matching locations are returned. | Test |
| REQ-003 | The system shall apply soft delete to locations: a deleted location is hidden from normal reads and queries but retained in storage, and can be restored under defined rules. | MUST | PC 2.1; WBS 2.1.1, 3.1.2 | Given a location with no active child locations or stock, When `DELETE /locations/{id}` is called, Then the location is marked deleted, returns HTTP 204, and is absent from `GET /locations` by default. Given a soft-deleted location, When restore is requested by an authorized role, Then it becomes active again. | Test |
| REQ-004 | The system shall enforce location capacity and status: a location shall not receive placements beyond its capacity, and shall not receive placements while its status is not active. | MUST | PC 2.1; WBS 3.1.3 | Given a location at its capacity limit, When a container or stock placement exceeds the remaining capacity, Then the request is rejected with HTTP 409 and error code `LOCATION_CAPACITY_EXCEEDED`, and no data changes. Given a location with a non-active status, When a placement is attempted, Then the request is rejected with a RFC 7807 error and no data changes. | Test |
| REQ-005 | The system shall reject any relocation of a location that would make a location its own ancestor or descendant of itself (no circular nesting). | MUST | PC 2.1; WBS 3.1.4 | Given location A is the parent of location B, When `PATCH /locations/{A}` sets A's parent to B, Then the request is rejected with HTTP 409 (or 422) and a RFC 7807 error, and the tree is unchanged. | Test |

### 1.2 Containers

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| REQ-006 | The system shall provide CRUD operations on `/containers` and track each container by its identifier. | MUST | PC 2.1; WBS 2.1.2, 3.2.1 | Given a valid container payload, When `POST /containers` is called, Then HTTP 201 is returned with the container. Given an existing container, When `GET /containers/{id}` is called, Then HTTP 200 is returned. | Test |
| REQ-007 | The system shall enforce a unique SSCC or barcode per container. | MUST | PC 2.1; WBS 2.1.2, 3.2.1 | Given a container with SSCC "X", When another container is created with SSCC "X", Then the request is rejected with HTTP 409 and error code `CONTAINER_SSCC_DUPLICATE` (proposed name, per WBS 2.3.1), and no second record is stored. Concurrent duplicate creation results in exactly one stored record. | Test |
| REQ-008 | The system shall support nesting containers and moving containers, and shall prevent orphaned containers (a container whose parent or location reference is invalid or missing). | MUST | PC 2.1; WBS 3.2.2 | Given a container nested in a parent container, When the parent is moved, Then the child moves with it. Given a move or nesting request that references a non-existent or deleted target, Then the request is rejected with a RFC 7807 error and no container is orphaned. | Test |
| REQ-009 | The system shall validate containers against maximum weight and maximum dimensions, and reject placements that exceed them. | MUST | PC 2.1; WBS 3.2.3 | Given a container whose total contained weight exceeds its maximum weight, When the placement or nesting is submitted, Then the request is rejected with HTTP 422 and a RFC 7807 error, and no data changes. Maximum values per container type are <UNKNOWN> (A6). | Test |
| REQ-010 | The system shall apply safe container deletion: a container that contains stock or child containers shall not be deleted. When a container is moved, its contained stock shall be displaced automatically to the new location, and the displacement shall be recorded in the movement log. | MUST | PC 2.1; WBS 3.2.4, 3.2.5 | Given a container containing stock or child containers, When `DELETE /containers/{id}` is called, Then the request is rejected with HTTP 409 and error code `CONTAINER_NOT_EMPTY` (proposed name, per WBS 2.3.1), and no data changes. Given a container with stock, When the container is moved to location L, Then all its stock is reported at location L in the same transaction, and a movement log entry exists for the displacement. | Test |

### 1.3 Stock and Inventory Operations

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| REQ-011 | The system shall register stock for an externally validated SKU reference with a quantity and unit of measure (UoM). | MUST | PC 2.1, 6 (A1); WBS 4.1.1 | Given a SKU string and a quantity with a valid UoM, When `POST /stocks` is called, Then HTTP 201 is returned with the stock record. Given a UoM not in the UoM catalog, Then the request is rejected with HTTP 422 and a RFC 7807 error. SKU master data is not validated by this system (Out of Scope, PC 2.2). | Test |
| REQ-012 | The system shall store lot or batch identifier and expiry date for each stock record. | MUST | PC 2.1; WBS 2.1.3, 4.1.1 | Given stock registered with lot "L1" and expiry date "E", When `GET /stocks/{id}` is called, Then the lot and expiry are returned unchanged. Given an expiry date earlier than the registration date, Then the request is rejected with HTTP 422. | Test |
| REQ-013 | The system shall accept only positive quantities for stock creation, moves, splits, and merges. | MUST | PC 2.1, 3; WBS 4.1.2 | Given a quantity of 0 or less, When any stock operation is submitted, Then the request is rejected with HTTP 422 and a RFC 7807 error, and no data changes. | Test |
| REQ-014 | The system shall manage stock status with the values `available`, `blocked`, and `quarantine`, and shall allow only stock with status `available` to be used in moves that fulfil demand (proposed rule, to be confirmed by Business Owner). | MUST | PC 2.1; WBS 4.1.2; TC 5 | Given stock with status `blocked`, When a status-restricted operation is requested, Then it is rejected with HTTP 409 and a RFC 7807 error. Given an invalid status value, Then HTTP 422 is returned. | Test |
| REQ-015 | The system shall require a reason code for every stock adjustment. | MUST | PC 2.1; WBS 4.1.3 | Given an adjustment without a reason code, When submitted, Then HTTP 422 is returned with a RFC 7807 error and no data changes. Given a valid reason code from the catalog, Then the adjustment is applied and the reason is recorded in the movement log. | Test |
| REQ-016 | The system shall require a reason code for every write-off. | MUST | PC 2.1; WBS 4.1.3 | Given a write-off without a reason code, When submitted, Then HTTP 422 is returned and no data changes occur. Given a valid reason code, Then the write-off is applied and the reason is recorded in the movement log. | Test |
| REQ-017 | The system shall prevent negative inventory: no operation shall reduce a stock quantity below zero. | MUST | PC 2.1, 9 (SC2); WBS 4.1.4 | Given stock with quantity 5, When a move, split, adjustment, or write-off requests 6 units, Then the request is rejected with HTTP 409 and error code `STOCK_INSUFFICIENT_QUANTITY`, and the quantity remains 5. | Test |
| REQ-018 | The system shall support full and partial moves of stock between locations or containers. | MUST | PC 2.1; WBS 4.2.1 | Given stock of quantity 10, When a partial move of 4 units is requested to a valid location, Then 4 units are moved and 6 units remain at the source, and both balances are correct. Given a full move, Then the whole quantity is at the target. | Test |
| REQ-019 | The system shall perform split, merge, group, and ungroup operations atomically: either all records change or none does. | MUST | PC 2.1; WBS 4.2.2, 4.2.3, 4.2.4 | Given a merge of two stock records of the same SKU, lot, and UoM, When it succeeds, Then one record holds the summed quantity and the other is closed. Given a failure during the operation (induced in test), Then all records retain their pre-operation state. Given `/stocks/group` into a container, Then the stock is grouped; ungroup returns stock to the container's location. | Test |

### 1.4 Audit Trail

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| REQ-020 | The system shall write an immutable movement log entry for every stock or container mutation, containing entity, operation, user, timestamp, before state, after state, and reason. The log shall be queryable via `GET /movements` with filters. | MUST | PC 2.1, 3, 9 (SC3); WBS 2.1.3, 4.3.1, 4.3.2, 4.3.3 | Given any successful stock mutation, When the transaction commits, Then exactly one log entry per affected entity exists with all seven fields populated. Given a `PATCH`, `PUT`, or `DELETE` request targeting a movement log entry, Then HTTP 405 (or 403) is returned and the entry is unchanged. Given `GET /movements?entity=…&from=…&to=…`, Then only matching entries are returned in chronological order. | Test; Inspection |

### 1.5 Concurrency and Data Integrity

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| REQ-021 | The system shall execute every multi-record operation within a single ACID transaction. | MUST | PC 2.1; WBS 2.1.4, 4.2.2, 4.2.3, 4.2.4 | Given a multi-resource operation that fails at its final step, When it is executed, Then no partial change is visible to other readers. Verified by fault injection in integration tests. | Test; Analysis |
| REQ-022 | The system shall use optimistic locking via ETag or version on every mutable resource. A write with a stale ETag or version shall be rejected. | MUST | PC 2.1; WBS 2.1.4, 4.2.5 | Given a resource with ETag "v1", When two clients update concurrently and the second sends "v1" via `If-Match`, Then the second request returns HTTP 412 with error code `CONCURRENCY_CONFLICT` (proposed name), and the first update is retained. A missing `If-Match` on a mutation returns HTTP 428. | Test |

### 1.6 Security and Access Control

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| REQ-023 | The system shall authenticate requests with JWT bearer tokens (OAuth2) and authorize them by RBAC with three roles: `admin`, `supervisor`, `operator`. | MUST | PC 2.1, 5; TC 8.2; WBS 2.2.1, 2.2.2, 2.2.3, 2.2.4 | Given a request without a valid token, Then HTTP 401 is returned. Given a valid token whose role lacks the permission for an endpoint, Then HTTP 403 is returned. Permission matrix (proposed): `admin` all operations; `supervisor` all stock and location operations, no role or configuration changes; `operator` read operations plus move, split, merge, and group. Final matrix is baselined in WBS 2.2.2 (A4). | Test; Inspection |

### 1.7 Error Handling and Localization

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| REQ-024 | The system shall return all error responses as RFC 7807 `application/problem+json`, including a machine-readable error code from the error code catalog. | MUST | PC 2.1, 3, 9 (SC6); TC 8.1; WBS 2.3.1, 2.3.2, 5.3.3 | Given any 4xx or 5xx response, Then the body contains `type`, `title`, `status`, `detail`, `instance`, and `code`, and `Content-Type` is `application/problem+json`. Every catalog error code is verified (WBS 5.3.3). | Test; Inspection |
| REQ-025 | The system shall be packaged as a Docker image, expose liveness and readiness health check endpoints, and emit structured JSON logs that include `trace_id`. | MUST | PC 2.1; WBS 2.4.1, 2.4.2, 2.4.3, 5.4.1, 5.4.2 | Given the container is started, When `GET /health/live` and `GET /health/ready` are called, Then HTTP 200 is returned when healthy and 503 when a dependency is unavailable. Given a request, Then each log line is valid JSON containing `trace_id`. | Demonstration; Test |
| REQ-026 | The system shall meet performance targets: p95 latency < 300 ms for single-item reads, and < 1 s for list and aggregate queries over 100k records. | MUST | PC 2.1, 3, 4, 9 (SC4); WBS 2.1.5, 5.1.x | Given a dataset of 100k records and the defined load profile, When single-item reads are measured, Then p95 < 300 ms. When list and aggregate endpoints are measured, Then response time < 1 s. | Test (performance); Analysis |
| REQ-027 | The system shall maintain automated test coverage of at least 80% in CI. | MUST | PC 2.1, 5, 9 (SC5); TC 8.2; WBS 2.4.4, 5.3.1 | Given a CI pipeline run, When the coverage gate executes, Then a result below 80% fails the build. | Test; Analysis |
| REQ-028 | The system shall publish an OpenAPI 3 specification and Swagger UI describing all endpoints and error responses. | MUST | PC 2.1, 3, 9 (SC6); WBS 2.3.5, 5.4.3 | Given the running service, When the OpenAPI document is requested, Then it validates as OpenAPI 3 and documents every endpoint in scope. Swagger UI renders it. | Inspection; Demonstration |

### 1.8 Localization and Error Catalog Details

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| REQ-029 | The system shall maintain a versioned error code catalog; each code has a stable identifier, HTTP status, and default message. Changes require Technical Lead approval and a changelog entry. | MUST | PC 3 (error handling); TC 3.2; WBS 2.3.1 | Given the catalog, When it is inspected, Then every code has an identifier, status, and default message, and a changelog exists for all catalog versions. | Inspection |
| REQ-030 | The system shall return HTTP 5xx responses that contain no stack trace, database schema detail, internal path, or library version. | MUST | PC 5 (security); TC 8.2; WBS 2.3.3 | Given an induced unhandled exception, When the request is served, Then the response body contains only a generic `title`, `code` (`INTERNAL_ERROR`), and `trace_id`, and none of the excluded content. | Test; Inspection |
| REQ-031 | The system shall include a `trace_id` in every 5xx response, matching the `trace_id` in the server log for that request. | MUST | PC 2.1 (leak-proof 5xx with trace_id); TC 8.2; WBS 2.3.3 | Given an induced 5xx, When the response `trace_id` is looked up in logs, Then exactly one log entry with the same `trace_id` and the error detail exists. | Test |
| REQ-032 | The system shall resolve error message text in English (EN) when requested with `Accept-Language: en`. | MUST | PC 2.1, 9 (SC8); WBS 2.3.4, 5.2.1 | Given `Accept-Language: en`, When an error is returned, Then the `title` and `detail` are the EN bundle text. | Test |
| REQ-033 | The system shall resolve error message text in Spanish (ES) when requested with `Accept-Language: es`. | MUST | PC 2.1, 9 (SC8); WBS 2.3.4, 5.2.1 | Given `Accept-Language: es`, When an error is returned, Then the `title` and `detail` are the ES bundle text. | Test |
| REQ-034 | The system shall resolve error message text in Catalan (CA) when requested with `Accept-Language: ca`. | MUST | PC 2.1, 9 (SC8); WBS 2.3.4, 5.2.1 | Given `Accept-Language: ca`, When an error is returned, Then the `title` and `detail` are the CA bundle text. | Test |
| REQ-035 | The system shall fall back to EN when the requested language is unsupported or absent. | MUST | PC 2.1; WBS 2.3.4 | Given `Accept-Language: fr` or no header, When an error is returned, Then the EN text is returned and the `code` is unchanged. | Test |
| REQ-036 | The system shall ensure every error code has EN, ES, and CA entries in the message bundles. | MUST | PC 9 (SC8); WBS 5.2.1, 5.2.3 | Given the bundles and the error catalog, When validation runs, Then zero catalog codes lack a bundle entry in any of the three languages. | Test |
| REQ-037 | The system shall keep the machine-readable `code` identical across all languages; only human-readable text is localized. | MUST | PC 3; WBS 2.3.4 | Given the same error in EN, ES, and CA, Then the `code` field is identical in all three responses. | Test |
| REQ-038 | The system shall have ES and CA message texts reviewed by a professional localization reviewer before the M4 gate. | MUST | PC 4 (M4); WBS 5.2.2 | Given the ES and CA bundles, Then a signed review record exists before M4 gate approval. | Inspection |

---

## 2. Non-Functional Requirements

### 2.1 Performance

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| NFR-P-01 | Single-item reads shall have p95 latency < 300 ms at the 100k-record volume. | MUST | PC 3, 5, 9 (SC4); WBS 5.1.3 | Given 100k records and the defined load profile, When p95 is measured over the agreed run, Then p95 < 300 ms. | Test (performance) |
| NFR-P-02 | List and aggregate queries shall complete in < 1 s at the 100k-record volume. | MUST | PC 3, 5, 9 (SC4); WBS 5.1.2 | Given 100k records, When list and aggregate endpoints are called, Then each response completes in < 1 s. | Test (performance) |
| NFR-P-03 | Performance targets shall be verified with a documented load profile (concurrency level, request mix, duration). | MUST | PC 4 (M4); WBS 5.1.5 | Given the performance report, Then the load profile is documented and results are reproducible. | Inspection |
| NFR-P-04 | Index strategy for the 100k target shall be documented before schema baseline. | MUST | PC 3; WBS 2.1.5 | Given the index strategy document, When reviewed at M1, Then it covers all list and aggregate query paths. | Inspection |

### 2.2 Security

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| NFR-S-01 | All endpoints except health liveness shall require a valid JWT. | MUST | PC 5; WBS 2.2.1 | Given a request without a token to a protected endpoint, Then HTTP 401 is returned. | Test |
| NFR-S-02 | RBAC shall be enforced for `admin`, `supervisor`, and `operator` on every endpoint. | MUST | TC 8.2; WBS 2.2.3, 2.2.4 | Given each role, When each endpoint is called, Then the response matches the baselined permission matrix (100% of endpoints covered by tests). | Test; Inspection |
| NFR-S-03 | External responses shall not expose stack traces, database schema details, or library versions. | MUST | PC 5; TC 3.3 | Given error scenarios including induced 5xx, Then no such content appears in responses. | Test; Inspection |
| NFR-S-04 | Security-relevant changes (auth, RBAC, error masking, audit log) shall be reviewed by the Security & Compliance Owner before merge. | MUST | TC 3.3, 8.2 | Given a pull request touching those areas, Then a security review approval is recorded before merge. | Inspection |
| NFR-S-05 | Security reviews shall be completed at M1 and M4 before gate approval. | MUST | PC 9; TC 8.3; WBS 2.4.6, 5.3.2 | Given gate records, Then security review reports exist for M1 and M4. | Inspection |
| NFR-S-06 | Secrets shall not be stored in source code, commits, or responses. | MUST | TC 3.3 | Given a repository scan, Then zero secrets are found. | Analysis |

### 2.3 Reliability and Data Integrity

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| NFR-R-01 | Zero negative-inventory, over-capacity, or orphaned-container records shall exist after acceptance testing. | MUST | PC 9 (SC2); TC 8.3 | Given the acceptance database, When integrity queries run, Then count of violating records = 0. | Test; Analysis |
| NFR-R-02 | Every stock mutation shall produce an immutable movement log entry (100% completeness). | MUST | PC 9 (SC3); WBS 4.4.2 | Given a mutation test suite, When completeness is checked, Then 100% of mutations have a log entry. | Test |
| NFR-R-03 | Concurrent split, merge, and group operations shall produce no lost updates and no duplicate or negative balances. | MUST | PC 3 (risk); TC 8.2; WBS 4.4.1 | Given a concurrency test with N parallel clients, Then the invariants of total quantity conservation and non-negative balances hold in 100% of runs. | Test |
| NFR-R-04 | Database migrations shall be versioned and reversible or forward-fixable, and tested in CI. | MUST | WBS 2.1.6, 2.1.7, 2.4.5 | Given a migration pipeline run, Then migrations apply from empty and from the previous version without error. | Test |
| NFR-R-05 | Stock and movement data shall be durable across service restart. | MUST | PC 4 (reliability objective); WBS 2.1.3 | Given committed transactions followed by restart, Then all committed data is present. | Test |

### 2.4 Usability and Developer Experience

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| NFR-U-01 | REST conventions shall be applied: resource nouns, HTTP verbs, and standard status codes. | MUST | PC 4 (objective 3); TC 2 (value 5) | Given the OpenAPI document, When inspected against the REST convention checklist, Then no non-conforming endpoint remains. | Inspection |
| NFR-U-02 | Every endpoint shall be documented in OpenAPI 3 with request and response schemas and all error codes. | MUST | PC 3; WBS 5.4.3 | Given the published specification, Then 100% of endpoints and error responses are documented. | Inspection |
| NFR-U-03 | Error messages shall be actionable: `detail` shall state what failed and the corrective action where possible. | SHOULD | TC 2 (value 5) | Given a sample of error responses, Then reviewers rate each as actionable. | Inspection; Demonstration |

### 2.5 Maintainability

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| NFR-M-01 | Automated test coverage shall be ≥ 80% in CI. | MUST | PC 5, 9 (SC5); WBS 2.4.4, 5.3.1 | Given the CI coverage report, Then coverage ≥ 80%. | Test; Analysis |
| NFR-M-02 | Every change shall be peer reviewed and merged only through a green pipeline. | MUST | TC 3.3 | Given the merge history, Then 100% of merges have a peer review and passing pipeline. | Inspection |
| NFR-M-03 | Logs shall be structured JSON to support operations and diagnosis. | MUST | PC 2.1 (REQ-025); WBS 2.4.1 | Given log output, Then each line parses as valid JSON. | Test |
| NFR-M-04 | Decisions and changes shall be recorded in the decision log and changelogs. | MUST | TC 3.4, 5, 10 | Given the repository, Then each decision has an entry with date, owner, rationale, and requirement IDs. | Inspection |

### 2.6 Compliance and Traceability

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| NFR-C-01 | Inventory changes shall be fully auditable, with user identity recorded on each movement entry. | MUST | PC 4 (objective 1); REQ-020 | Given a movement entry, Then the user identifier from the JWT subject is recorded. | Test; Inspection |
| NFR-C-02 | Audit log entries shall not be modified or deleted by any role, including `admin`. | MUST | PC 9 (SC3); WBS 4.3.3 | Given each role, When update or delete on the log is attempted, Then the request is rejected and the entry is unchanged. | Test |
| NFR-C-03 | Requirements, contracts, and error catalog shall be under change control, with changes requiring the approval defined in the Team Charter. | MUST | TC 3.2, 5; PC 8 | Given a change to a baselined contract, Then a change request and approval record exist. | Inspection |
| NFR-C-04 | Localization content shall be reviewed for accuracy before release. | MUST | PC 9 (SC8); WBS 5.2.2 | Given the ES and CA bundles, Then a review sign-off exists (REQ-038). | Inspection |

### 2.7 Deployment and Operability

| ID | Statement | Priority | Source | Acceptance Criteria | Verification |
| --- | --- | --- | --- | --- | --- |
| NFR-D-01 | The service shall deploy as a container to Kubernetes or a cloud platform. | MUST | PC 5; WBS 5.4.1 | Given the deployment manifests, When deployed to the target environment, Then the service starts and passes readiness checks. | Demonstration |
| NFR-D-02 | Health check results shall reflect real dependency state (database reachable). | MUST | PC 2.1 (REQ-025) | Given the database is stopped, When `/health/ready` is called, Then HTTP 503 is returned. | Test |

---

## 3. Traceability Matrix

Each requirement maps to WBS work packages (code) and to user stories. User stories are not provided in the inputs (backlog empty); the column records the story reference as **<UNKNOWN> (to be created in backlog refinement)** so that the link is closed before iteration planning.

| Requirement ID | Group | WBS Work Package(s) | Milestone | User Story | Verification |
| --- | --- | --- | --- | --- | --- |
| REQ-001 | Location | 2.1.1, 3.1.1 | M2 | <UNKNOWN> | Test |
| REQ-002 | Location | 3.1.1 | M2 | <UNKNOWN> | Test |
| REQ-003 | Location | 2.1.1, 3.1.2 | M2 | <UNKNOWN> | Test |
| REQ-004 | Location | 3.1.3 | M2 | <UNKNOWN> | Test |
| REQ-005 | Location | 3.1.4 | M2 | <UNKNOWN> | Test |
| REQ-006 | Container | 2.1.2, 3.2.1 | M2 | <UNKNOWN> | Test |
| REQ-007 | Container | 2.1.2, 3.2.1 | M2 | <UNKNOWN> | Test |
| REQ-008 | Container | 3.2.2 | M2 | <UNKNOWN> | Test |
| REQ-009 | Container | 3.2.3 | M2 | <UNKNOWN> | Test |
| REQ-010 | Container | 3.2.4, 3.2.5 | M2 | <UNKNOWN> | Test |
| REQ-011 | Inventory core | 4.1.1 | M3 | <UNKNOWN> | Test |
| REQ-012 | Inventory core | 2.1.3, 4.1.1 | M3 | <UNKNOWN> | Test |
| REQ-013 | Inventory core | 4.1.2 | M3 | <UNKNOWN> | Test |
| REQ-014 | Inventory core | 4.1.2 | M3 | <UNKNOWN> | Test |
| REQ-015 | Inventory core | 4.1.3 | M3 | <UNKNOWN> | Test |
| REQ-016 | Inventory core | 4.1.3 | M3 | <UNKNOWN> | Test |
| REQ-017 | Inventory core | 4.1.4 | M3 | <UNKNOWN> | Test |
| REQ-018 | Inventory core | 4.2.1 | M3 | <UNKNOWN> | Test |
| REQ-019 | Inventory core | 4.2.2, 4.2.3, 4.2.4 | M3 | <UNKNOWN> | Test |
| REQ-020 | Audit | 2.1.3, 4.3.1, 4.3.2, 4.3.3 | M3 | <UNKNOWN> | Test; Inspection |
| REQ-021 | Architecture | 2.1.4, 4.2.2, 4.2.3, 4.2.4 | M1, M3 | <UNKNOWN> | Test; Analysis |
| REQ-022 | Architecture | 2.1.4, 4.2.5 | M1, M3 | <UNKNOWN> | Test |
| REQ-023 | Security | 2.2.1, 2.2.2, 2.2.3, 2.2.4 | M1 | <UNKNOWN> | Test; Inspection |
| REQ-024 | Errors | 2.3.1, 2.3.2, 5.3.3 | M1, M4 | <UNKNOWN> | Test; Inspection |
| REQ-025 | Observability | 2.4.1, 2.4.2, 2.4.3, 5.4.1, 5.4.2 | M1, M4 | <UNKNOWN> | Demonstration; Test |
| REQ-026 | Performance | 2.1.5, 5.1.2, 5.1.3, 5.1.4, 5.1.5 | M4 | <UNKNOWN> | Test (performance); Analysis |
| REQ-027 | Quality | 2.4.4, 5.3.1 | M1, M4 | <UNKNOWN> | Test; Analysis |
| REQ-028 | Deployment/API | 2.3.5, 5.4.3 | M1, M4 | <UNKNOWN> | Inspection; Demonstration |
| REQ-029 | Errors | 2.3.1 | M1 | <UNKNOWN> | Inspection |
| REQ-030 | Errors | 2.3.3 | M1 | <UNKNOWN> | Test; Inspection |
| REQ-031 | Errors | 2.3.3 | M1 | <UNKNOWN> | Test |
| REQ-032 | Localization | 2.3.4, 5.2.1 | M1, M4 | <UNKNOWN> | Test |
| REQ-033 | Localization | 2.3.4, 5.2.1 | M1, M4 | <UNKNOWN> | Test |
| REQ-034 | Localization | 2.3.4, 5.2.1 | M1, M4 | <UNKNOWN> | Test |
| REQ-035 | Localization | 2.3.4 | M1 | <UNKNOWN> | Test |
| REQ-036 | Localization | 5.2.1, 5.2.3 | M4 | <UNKNOWN> | Test |
| REQ-037 | Localization | 2.3.4 | M1 | <UNKNOWN> | Test |
| REQ-038 | Localization | 5.2.2 | M4 | <UNKNOWN> | Inspection |
| NFR-P-01 | Performance | 5.1.3 | M4 | <UNKNOWN> | Test (performance) |
| NFR-P-02 | Performance | 5.1.2 | M4 | <UNKNOWN> | Test (performance) |
| NFR-P-03 | Performance | 5.1.5 | M4 | <UNKNOWN> | Inspection |
| NFR-P-04 | Performance | 2.1.5 | M1 | <UNKNOWN> | Inspection |
| NFR-S-01 | Security | 2.2.1 | M1 | <UNKNOWN> | Test |
| NFR-S-02 | Security | 2.2.3, 2.2.4 | M1 | <UNKNOWN> | Test; Inspection |
| NFR-S-03 | Security | 2.3.3 | M1, M4 | <UNKNOWN> | Test; Inspection |
| NFR-S-04 | Security | 2.4.6, 5.3.2 | M1, M4 | <UNKNOWN> | Inspection |
| NFR-S-05 | Security | 2.4.6, 5.3.2 | M1, M4 | <UNKNOWN> | Inspection |
| NFR-S-06 | Security | 2.2.1 | M1 | <UNKNOWN> | Analysis |
| NFR-R-01 | Reliability | 3.3.1, 3.3.2, 4.4.3 | M2, M3 | <UNKNOWN> | Test; Analysis |
| NFR-R-02 | Reliability | 4.4.2 | M3 | <UNKNOWN> | Test |
| NFR-R-03 | Reliability | 4.2.5, 4.4.1 | M3 | <UNKNOWN> | Test |
| NFR-R-04 | Reliability | 2.1.6, 2.1.7, 2.4.5 | M1 | <UNKNOWN> | Test |
| NFR-R-05 | Reliability | 2.1.3 | M1 | <UNKNOWN> | Test |
| NFR-U-01 | Usability | 2.3.5 | M1 | <UNKNOWN> | Inspection |
| NFR-U-02 | Usability | 2.3.5, 5.4.3 | M1, M4 | <UNKNOWN> | Inspection |
| NFR-U-03 | Usability | 2.3.1 | M1 | <UNKNOWN> | Inspection; Demonstration |
| NFR-M-01 | Maintainability | 2.4.4, 5.3.1 | M1, M4 | <UNKNOWN> | Test; Analysis |
| NFR-M-02 | Maintainability | 2.4.4 | M1 | <UNKNOWN> | Inspection |
| NFR-M-03 | Maintainability | 2.4.1 | M1 | <UNKNOWN> | Test |
| NFR-M-04 | Maintainability | 1.2.2, 1.2.3 | M1 | <UNKNOWN> | Inspection |
| NFR-C-01 | Compliance | 4.3.1, 4.3.2 | M3 | <UNKNOWN> | Test; Inspection |
| NFR-C-02 | Compliance | 4.3.3 | M3 | <UNKNOWN> | Test |
| NFR-C-03 | Compliance | 1.2.3 | M1 | <UNKNOWN> | Inspection |
| NFR-C-04 | Compliance | 5.2.2 | M4 | <UNKNOWN> | Inspection |
| NFR-D-01 | Deployment | 5.4.1, 6.1.1, 6.1.2 | M4, Release | <UNKNOWN> | Demonstration |
| NFR-D-02 | Deployment | 2.4.2 | M1 | <UNKNOWN> | Test |

### 3.1 Reverse Traceability: Work Packages to Requirements

| WBS Package | Requirements |
| --- | --- |
| 1.1.4, 1.2.x | Governance; NFR-M-04, NFR-C-03 |
| 2.1.1 | REQ-001, REQ-003 |
| 2.1.2 | REQ-006, REQ-007, REQ-008 |
| 2.1.3 | REQ-012, REQ-020, NFR-R-05 |
| 2.1.4 | REQ-021, REQ-022 |
| 2.1.5 | REQ-026, NFR-P-04 |
| 2.1.6, 2.1.7, 2.4.5 | NFR-R-04 |
| 2.2.1 | REQ-023, NFR-S-01, NFR-S-06 |
| 2.2.2, 2.2.3, 2.2.4 | REQ-023, NFR-S-02 |
| 2.3.1 | REQ-024, REQ-029, NFR-U-03 |
| 2.3.2 | REQ-024 |
| 2.3.3 | REQ-030, REQ-031, NFR-S-03 |
| 2.3.4 | REQ-032 to REQ-035, REQ-037 |
| 2.3.5 | REQ-028, NFR-U-01, NFR-U-02 |
| 2.4.1 | REQ-025, NFR-M-03 |
| 2.4.2 | REQ-025, NFR-D-02 |
| 2.4.3 | REQ-025 |
| 2.4.4 | REQ-027, NFR-M-01, NFR-M-02 |
| 2.4.6 | NFR-S-04, NFR-S-05 |
| 3.1.1 | REQ-001, REQ-002 |
| 3.1.2 | REQ-003 |
| 3.1.3 | REQ-004 |
| 3.1.4 | REQ-005 |
| 3.2.1 | REQ-006, REQ-007 |
| 3.2.2 | REQ-008 |
| 3.2.3 | REQ-009 |
| 3.2.4, 3.2.5 | REQ-010 |
| 3.3.1, 3.3.2 | REQ-004, REQ-008, REQ-010, NFR-R-01 |
| 4.1.1 | REQ-011, REQ-012 |
| 4.1.2 | REQ-013, REQ-014 |
| 4.1.3 | REQ-015, REQ-016 |
| 4.1.4 | REQ-017 |
| 4.2.1 | REQ-018 |
| 4.2.2, 4.2.3, 4.2.4 | REQ-019, REQ-021 |
| 4.2.5 | REQ-022, NFR-R-03 |
| 4.3.1 | REQ-020, NFR-C-01 |
| 4.3.2 | REQ-020, NFR-C-01 |
| 4.3.3 | REQ-020, NFR-C-02 |
| 4.4.1 | NFR-R-03 |
| 4.4.2 | NFR-R-02 |
| 4.4.3 | REQ-001 to REQ-020, NFR-R-01 |
| 5.1.2, 5.1.3, 5.1.4, 5.1.5 | REQ-026, NFR-P-01, NFR-P-02, NFR-P-03 |
| 5.2.1, 5.2.3 | REQ-032 to REQ-036 |
| 5.2.2 | REQ-038, NFR-C-04 |
| 5.3.1 | REQ-027, NFR-M-01 |
| 5.3.2 | NFR-S-04, NFR-S-05 |
| 5.3.3 | REQ-024 |
| 5.4.1 | REQ-025, NFR-D-01 |
| 5.4.2 | REQ-025 |
| 5.4.3 | REQ-028, NFR-U-02 |
| 6.1.x | NFR-D-01 |

### 3.2 Coverage Check

* REQ-001 to REQ-038: each has at least one WBS package and at least one verification method. Checked: 38 of 38.
* WBS packages with no requirement mapping: 1.1.1, 1.1.2, 1.1.3, 1.2.1, 1.2.4, 2.4.7, 3.3.3, 4.4.4, 5.4.4, 6.1.2–6.1.5 are gate, governance, or release packages. They are traced to the gate criteria that reference REQ IDs (WBS 2.4.7, 3.3.3, 4.4.4, 5.4.4 are gates that consume the requirements above).
* User stories: none exist in the inputs. Story IDs remain <UNKNOWN> and must be created during backlog refinement before Definition of Ready (Team Charter 8.1).

---

## 4. Open Items for Requirements Baseline

| # | Item | Owner | Needed By |
| --- | --- | --- | --- |
| OI-01 | Confirm RBAC permission matrix (A4) | Security Analyst / Business Owner | M1 gate |
| OI-02 | Provide reason code catalog and UoM catalog (A6) | Business Owner | Before M3 |
| OI-03 | Define max weight and dimension values per container type (REQ-009) | Business Owner | Before M2 |
| OI-04 | Confirm stock status rule for demand fulfilment (REQ-014) | Business Owner | Before M3 |
| OI-05 | Confirm 100k planning volume (A3) and load profile (NFR-P-03) | Project Manager / Performance Engineer | Before M4 |
| OI-06 | Create user stories and link to REQ IDs in backlog | Technical Lead / Business Owner | Before iteration 1 |
| OI-07 | Name identity provider (A2) | Technical Lead | Before M1 |
| OI-08 | Confirm milestone dates and team composition (WBS 1.1.4) | Project Manager | Before M2 |
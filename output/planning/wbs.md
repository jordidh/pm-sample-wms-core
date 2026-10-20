# Work Breakdown Structure: Warehouse Inventory & Storage Management REST API (WMS Core)

| Field | Value |
| --- | --- |
| Document | Work Breakdown Structure (Predictive Components) |
| Project Name | Warehouse Inventory & Storage Management REST API (WMS Core) |
| Current Version | 1.0 |
| Date | 2026-10-20 |

## Changelog

| Version | Date | Change Description |
| --- | --- | --- |
| 1.0 | 2026-10-20 | Initial version |

## 0. Scope Basis and Assumptions

* Development approach is **hybrid** (Business Case Section 6). This WBS covers the **predictive components**: governance, fixed data model and transactional rules, RBAC boundaries, error contract, NFR validation, and release. Adaptive stock-operation refinements are in the backlog document.
* Work packages are sized under 40 effort hours. Effort is in hours; duration is in working days assuming one dedicated resource-equivalent per package unless stated otherwise. Team composition is not provided (<UNKNOWN>); durations are planning estimates.
* Milestone dates are not provided (charter Section 4 <UNKNOWN>). Dates are expressed as relative project days (Day 1 = project start).
* Out of scope (charter Section 2.2): SKU master data, hardware drivers, ERP receiving/shipping integration.
* Requirements REQ-001 to REQ-038 are mapped to work packages below.
* Current-state baseline, cost estimates, and Option B (commercial WMS) evaluation are prerequisites recorded in Business Case Section 9 and are tracked as governance packages.

## 1. Project Management and Governance

### 1.1 Initiation and Approvals
* 1.1.1 Confirm sponsor, name project manager, and complete charter approval table (Section 11 of Charter)
* 1.1.2 Collect current-state baseline metrics (discrepancy rate, reconciliation hours, write-off value)
* 1.1.3 Document Option B evaluation (commercial WMS) and confirm build decision
* 1.1.4 Produce cost and resource estimate and set milestone target dates (M1–M4)

### 1.2 Planning and Control
* 1.2.1 Establish iteration cadence (two-week assumption) and sprint calendar
* 1.2.2 Establish risk, issue, and assumption log
* 1.2.3 Establish change control process and out-of-scope enforcement list
* 1.2.4 Milestone gate reviews M1–M4 (governance)

## 2. M1: Architectural Foundation and CI/CD

### 2.1 Database Foundation
* 2.1.1 Design relational schema for location hierarchy (warehouse > zone > aisle > rack > shelf > bin) with soft-delete columns (REQ-001, REQ-003)
* 2.1.2 Design schema for containers, SSCC/barcode uniqueness, and nesting (REQ-006, REQ-007)
* 2.1.3 Design schema for stock allocations, lot/batch, expiry, UoM, status, and movement log (REQ-011, REQ-012, REQ-020)
* 2.1.4 Define transaction boundaries, isolation levels, and version/ETag columns for optimistic locking (REQ-021, REQ-022)
* 2.1.5 Define index strategy for 100k-record query targets (REQ-026)
* 2.1.6 Implement migration tooling and baseline migration scripts
* 2.1.7 Implement database schema migrations and integration test database fixtures

### 2.2 Security and RBAC
* 2.2.1 Integrate JWT/OAuth2 validation middleware with identity provider (REQ-023)
* 2.2.2 Define RBAC role-permission matrix for admin, supervisor, operator
* 2.2.3 Implement RBAC authorization middleware and role-based endpoint guards
* 2.2.4 Implement security tests for authentication and authorization denial paths

### 2.3 Error Handling and API Contract
* 2.3.1 Define error code catalog and RFC 7807 problem+json schema (REQ-024, REQ-029)
* 2.3.2 Implement global RFC 7807 exception handler
* 2.3.3 Implement leak-proof 5xx responses with trace_id and no stack trace or schema disclosure (REQ-030, REQ-031)
* 2.3.4 Implement localization resolver for EN, ES, CA error messages (REQ-032 to REQ-038)
* 2.3.5 Create OpenAPI 3 skeleton and Swagger UI wiring (REQ-028)

### 2.4 Platform, Observability and CI/CD
* 2.4.1 Implement structured JSON logging with trace_id propagation (REQ-025)
* 2.4.2 Implement health check endpoints (liveness and readiness) (REQ-025)
* 2.4.3 Create Dockerfile and local compose environment (REQ-025)
* 2.4.4 Configure CI pipeline: build, lint, unit test, and coverage gate at 80% (REQ-027)
* 2.4.5 Configure CI pipeline: migration and integration test stages
* 2.4.6 Foundation security review (M1 gate) (Business Case Section 8.2)
* 2.4.7 M1 gate review and CI pipeline operational sign-off

## 3. M2: Location and Container Hierarchy Engine

### 3.1 Location Engine
* 3.1.1 Implement location CRUD endpoints (`/locations`) (REQ-001, REQ-002)
* 3.1.2 Implement soft-delete behavior and restore rules for locations (REQ-003)
* 3.1.3 Implement capacity and status enforcement for locations (REQ-004)
* 3.1.4 Implement non-cyclic relocation validation for location tree (REQ-005)
* 3.1.5 Automated tests for location engine (unit and integration)

### 3.2 Container Engine
* 3.2.1 Implement container CRUD endpoints (`/containers`) with unique SSCC/barcode enforcement (REQ-006, REQ-007)
* 3.2.2 Implement container nesting and container move with orphan prevention (REQ-008)
* 3.2.3 Implement maximum weight and dimension validation (REQ-009)
* 3.2.4 Implement safe container deletion rules (REQ-010)
* 3.2.5 Implement automatic stock displacement on container move (REQ-010)
* 3.2.6 Automated tests for container engine (unit and integration)

### 3.3 M2 Verification
* 3.3.1 Capacity and over-capacity placement acceptance tests
* 3.3.2 Orphaned-container acceptance tests
* 3.3.3 M2 gate review

## 4. M3: Stock Management and Advanced Movements

### 4.1 Stock Allocation
* 4.1.1 Implement SKU stock registration with quantity, UoM, lot/batch, and expiry (REQ-011, REQ-012)
* 4.1.2 Implement positive-quantity and status (`available`, `blocked`, `quarantine`) rules (REQ-013, REQ-014)
* 4.1.3 Implement mandatory reason codes for adjustments and write-offs (REQ-015, REQ-016)
* 4.1.4 Implement negative-inventory prevention rules (REQ-017)

### 4.2 Stock Operations (Predictive Core)
* 4.2.1 Implement full and partial stock move endpoints (REQ-018)
* 4.2.2 Implement atomic split operation with transactional guarantees (REQ-019, REQ-021)
* 4.2.3 Implement atomic merge operation (`/stocks/merge`) (REQ-019, REQ-021)
* 4.2.4 Implement atomic group and ungroup into containers (`/stocks/group`, ungroup) (REQ-019, REQ-021)
* 4.2.5 Implement optimistic locking via ETag/version on stock mutations (REQ-022)

### 4.3 Audit Trail
* 4.3.1 Implement immutable movement log writer for all stock mutations (REQ-020)
* 4.3.2 Implement `GET /movements` with filters (REQ-020)
* 4.3.3 Implement audit immutability controls (no update or delete paths)

### 4.4 M3 Verification
* 4.4.1 Concurrency tests for split, merge, and group operations
* 4.4.2 Movement log completeness test (100% of mutations logged)
* 4.4.3 Functional acceptance tests REQ-001 to REQ-020
* 4.4.4 M3 gate review

## 5. M4: Performance, Localization and Production Readiness

### 5.1 Performance Validation
* 5.1.1 Generate 100k-record synthetic dataset and seed scripts
* 5.1.2 Execute 100k-record stress tests for list and aggregate endpoints (REQ-026)
* 5.1.3 Execute p95 latency tests for single-item reads (< 300 ms) (REQ-026)
* 5.1.4 Tune indexes and queries to meet targets
* 5.1.5 Performance test report

### 5.2 Localization
* 5.2.1 Complete EN, ES, CA message bundles (REQ-032 to REQ-038)
* 5.2.2 Professional translation review for ES and CA
* 5.2.3 Localization validation tests

### 5.3 Quality and Security
* 5.3.1 Raise test coverage to ≥ 80% across the suite (REQ-027)
* 5.3.2 Security review at M4 (Business Case Section 8.2)
* 5.3.3 Verify RFC 7807 errors across the full error code catalog (REQ-024)

### 5.4 Deployment Readiness
* 5.4.1 Finalize Docker image and Kubernetes or cloud deployment manifests (REQ-025)
* 5.4.2 Validate health checks in deployment environment
* 5.4.3 Publish final OpenAPI 3 specification and Swagger UI (REQ-028)
* 5.4.4 M4 gate review and release approval

## 6. Release and Deployment

* 6.1.1 Production environment provisioning
* 6.1.2 Production deployment and smoke tests
* 6.1.3 Monitoring and alerting setup
* 6.1.4 Operations handover and runbook
* 6.1.5 Production go-live acceptance

## 7. Operate, Support and Retirement (Post-Release, Not Scheduled in Baseline)

* 7.1 Operate and support (incident handling, performance monitoring, defect fixes) — ongoing, reviewed annually
* 7.2 Retirement planning (data archival, consumer migration) — out of baseline schedule

## WBS Dictionary

| Code | Name | Description | Deliverable | Effort (h) | Duration (days) | Predecessors | Owner Role |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1.1.1 | Confirm sponsor and PM | Name sponsor and PM; approve charter | Signed charter approval table | 8 | 2 | None | Project Sponsor |
| 1.1.2 | Current-state baseline | Collect discrepancy rate, reconciliation hours, write-off value | Baseline metrics report | 24 | 5 | 1.1.1 FS +0 | Business Analyst |
| 1.1.3 | Option B evaluation | Evaluate commercial WMS options against requirements | Option B assessment memo | 32 | 5 | 1.1.1 FS +0 | Solution Architect |
| 1.1.4 | Estimates and milestone dates | Cost, team, and milestone date baseline | Cost estimate and schedule baseline | 24 | 4 | 1.1.2 FS +0; 1.1.3 FS +0 | Project Manager |
| 1.2.1 | Iteration calendar | Set two-week cadence and sprint calendar | Iteration calendar | 4 | 1 | 1.1.4 FS +0 | Project Manager |
| 1.2.2 | Risk and assumption log | Initialize RAID log | RAID log | 6 | 1 | 1.1.1 FS +0 | Project Manager |
| 1.2.3 | Change control | Establish change control and out-of-scope list | Change control procedure | 8 | 1 | 1.1.1 FS +0 | Project Manager |
| 1.2.4 | Milestone gate governance | Gate review templates and cadence | Gate criteria document | 8 | 1 | 1.2.1 FS +0 | Project Manager |
| 2.1.1 | Location schema design | Hierarchy tables with soft-delete columns | Location schema DDL and ERD | 24 | 3 | 1.1.4 FS +0 | Data Architect |
| 2.1.2 | Container schema design | Container tables, SSCC uniqueness, nesting | Container schema DDL | 24 | 3 | 2.1.1 FS +0 | Data Architect |
| 2.1.3 | Stock and movement schema design | Stock allocation, lot, expiry, UoM, status, movement log | Stock and movement schema DDL | 32 | 4 | 2.1.2 FS +0 | Data Architect |
| 2.1.4 | Transaction and locking design | Transaction boundaries, isolation, version columns | Transaction and locking design note | 16 | 2 | 2.1.3 FS +0 | Solution Architect |
| 2.1.5 | Index strategy | Index plan for 100k-record targets | Index strategy document | 12 | 2 | 2.1.3 FS +0 | Data Architect |
| 2.1.6 | Migration tooling setup | Configure migration framework | Migration tool configuration | 12 | 2 | 2.1.4 FS +0 | Backend Developer |
| 2.1.7 | Baseline migrations and fixtures | Baseline migration scripts and test fixtures | Migration scripts and fixtures | 20 | 3 | 2.1.6 FS +0; 2.1.5 FS +0 | Backend Developer |
| 2.2.1 | JWT/OAuth2 integration | Token validation with identity provider | Auth middleware | 24 | 3 | 1.1.4 FS +0 | Backend Developer |
| 2.2.2 | RBAC matrix | Role-permission matrix for admin, supervisor, operator | RBAC matrix document | 8 | 1 | 1.1.4 FS +0 | Security Analyst |
| 2.2.3 | RBAC middleware | Role-based endpoint guards | RBAC middleware | 20 | 3 | 2.2.1 FS +0; 2.2.2 FS +0 | Backend Developer |
| 2.2.4 | Auth security tests | Authentication and authorization denial tests | Security test suite | 12 | 2 | 2.2.3 FS +0 | QA Engineer |
| 2.3.1 | Error catalog and problem schema | Error codes and RFC 7807 schema | Error code catalog | 16 | 2 | 1.1.4 FS +0 | Solution Architect |
| 2.3.2 | Global RFC 7807 handler | Exception handler producing problem+json | Exception handler | 16 | 2 | 2.3.1 FS +0 | Backend Developer |
| 2.3.3 | Leak-proof 5xx | Mask stack traces and schema details; trace_id | Error masking module | 16 | 2 | 2.3.2 FS +0 | Backend Developer |
| 2.3.4 | Localization resolver | EN/ES/CA message resolution for errors | Localization resolver | 16 | 2 | 2.3.2 FS +0 | Backend Developer |
| 2.3.5 | OpenAPI skeleton and Swagger UI | OpenAPI 3 skeleton and Swagger UI | OpenAPI skeleton | 12 | 2 | 2.3.1 FS +0 | Backend Developer |
| 2.4.1 | Structured JSON logging | JSON logs with trace_id | Logging configuration | 12 | 2 | 2.3.2 FS +0 | Backend Developer |
| 2.4.2 | Health checks | Liveness and readiness endpoints | Health check endpoints | 8 | 1 | 2.1.7 FS +0 | Backend Developer |
| 2.4.3 | Docker image and compose | Dockerfile and local compose environment | Docker image and compose file | 16 | 2 | 2.4.2 FS +0 | DevOps Engineer |
| 2.4.4 | CI build, lint, unit test, coverage gate | CI pipeline with 80% coverage gate | CI pipeline | 20 | 3 | 2.2.4 FS +0; 2.3.5 FS +0 | DevOps Engineer |
| 2.4.5 | CI migration and integration stages | Migration and integration test stages in CI | CI pipeline stages | 12 | 2 | 2.4.4 FS +0; 2.1.7 FS +0 | DevOps Engineer |
| 2.4.6 | Foundation security review | M1 security review | Security review report | 8 | 1 | 2.2.4 FS +0; 2.3.3 FS +0 | Security Analyst |
| 2.4.7 | M1 gate | Foundation review and CI sign-off | M1 gate decision | 4 | 1 | 2.4.5 FS +0; 2.4.3 FS +0; 2.4.6 FS +0; 2.3.4 FS +0 | Project Sponsor |
| 3.1.1 | Location CRUD | `/locations` CRUD endpoints | Location endpoints | 24 | 3 | 2.4.7 FS +0 | Backend Developer |
| 3.1.2 | Location soft delete and restore | Soft delete and restore rules | Soft-delete behavior | 12 | 2 | 3.1.1 FS +0 | Backend Developer |
| 3.1.3 | Location capacity and status | Capacity and status enforcement | Capacity guardrail logic | 16 | 2 | 3.1.1 FS +0 | Backend Developer |
| 3.1.4 | Non-cyclic relocation | Cycle detection on location tree | Cycle validation | 12 | 2 | 3.1.2 FS +0 | Backend Developer |
| 3.1.5 | Location tests | Unit and integration tests for location engine | Test suite | 16 | 2 | 3.1.3 FS +0; 3.1.4 FS +0 | QA Engineer |
| 3.2.1 | Container CRUD and SSCC uniqueness | `/containers` CRUD with unique SSCC/barcode | Container endpoints | 24 | 3 | 2.4.7 FS +0; 3.1.1 FS +0 | Backend Developer |
| 3.2.2 | Container nesting and move | Nested moves with orphan prevention | Nesting and move logic | 24 | 3 | 3.2.1 FS +0 | Backend Developer |
| 3.2.3 | Weight and dimension validation | Max weight and dimension checks | Validation logic | 12 | 2 | 3.2.1 FS +0 | Backend Developer |
| 3.2.4 | Safe container deletion | Deletion rules preventing orphaned content | Delete rules | 12 | 2 | 3.2.2 FS +0 | Backend Developer |
| 3.2.5 | Stock displacement on container move | Automatic stock displacement | Displacement logic | 16 | 2 | 3.2.2 FS +0 | Backend Developer |
| 3.2.6 | Container tests | Unit and integration tests for container engine | Test suite | 16 | 2 | 3.2.4 FS +0; 3.2.5 FS +0; 3.2.3 FS +0 | QA Engineer |
| 3.3.1 | Capacity acceptance tests | Over-capacity placement tests | Test evidence | 8 | 1 | 3.1.5 FS +0; 3.2.6 FS +0 | QA Engineer |
| 3.3.2 | Orphan acceptance tests | Orphaned-container tests | Test evidence | 8 | 1 | 3.2.6 FS +0 | QA Engineer |
| 3.3.3 | M2 gate | Milestone gate review | M2 gate decision | 4 | 1 | 3.3.1 FS +0; 3.3.2 FS +0 | Project Sponsor |
| 4.1.1 | Stock registration | Stock with quantity, UoM, lot, expiry | Stock endpoints | 24 | 3 | 3.3.3 FS +0 | Backend Developer |
| 4.1.2 | Quantity and status rules | Positive quantities; available, blocked, quarantine | Status and quantity logic | 12 | 2 | 4.1.1 FS +0 | Backend Developer |
| 4.1.3 | Reason codes | Mandatory reason codes for adjustments and write-offs | Reason code logic | 12 | 2 | 4.1.1 FS +0 | Backend Developer |
| 4.1.4 | Negative-inventory prevention | Guard against negative balances | Guard logic | 12 | 2 | 4.1.2 FS +0 | Backend Developer |
| 4.2.1 | Full and partial move | Move endpoints for stock | Move logic | 20 | 3 | 4.1.4 FS +0; 3.2.5 FS +0 | Backend Developer |
| 4.2.2 | Atomic split | Split with transactional guarantee | Split operation | 24 | 3 | 4.2.1 FS +0; 2.1.4 FS +0 | Backend Developer |
| 4.2.3 | Atomic merge | `/stocks/merge` atomic merge | Merge operation | 24 | 3 | 4.2.2 SS +0 | Backend Developer |
| 4.2.4 | Atomic group and ungroup | `/stocks/group` and ungroup | Group and ungroup operations | 24 | 3 | 4.2.2 SS +0 | Backend Developer |
| 4.2.5 | Optimistic locking on stock | ETag/version checks on mutations | Locking implementation | 16 | 2 | 4.2.2 FS +0; 4.1.1 FS +0 | Backend Developer |
| 4.3.1 | Movement log writer | Log writer for all stock mutations | Movement log writer | 20 | 3 | 4.2.2 FS +0; 4.2.3 FS +0; 4.2.4 FS +0 | Backend Developer |
| 4.3.2 | GET /movements | Movement query endpoint with filters | Movement endpoint | 16 | 2 | 4.3.1 FS +0 | Backend Developer |
| 4.3.3 | Audit immutability | Block update and delete on log | Immutability controls | 8 | 1 | 4.3.1 FS +0 | Backend Developer |
| 4.4.1 | Concurrency tests | Split, merge, group concurrency tests | Test suite | 24 | 3 | 4.2.5 FS +0; 4.3.1 FS +0 | QA Engineer |
| 4.4.2 | Movement completeness test | 100% mutation logging verification | Test evidence | 8 | 1 | 4.3.3 FS +0; 4.3.2 FS +0 | QA Engineer |
| 4.4.3 | Functional acceptance REQ-001 to REQ-020 | Acceptance test execution | Acceptance evidence | 24 | 3 | 4.4.1 FS +0; 4.4.2 FS +0 | QA Engineer |
| 4.4.4 | M3 gate | Milestone gate review | M3 gate decision | 4 | 1 | 4.4.3 FS +0 | Project Sponsor |
| 5.1.1 | 100k dataset and seed scripts | Synthetic dataset generation | Seed scripts | 12 | 2 | 4.4.4 FS +0 | QA Engineer |
| 5.1.2 | 100k stress tests | List and aggregate stress tests | Stress test results | 20 | 3 | 5.1.1 FS +0 | Performance Engineer |
| 5.1.3 | p95 single-item read tests | Latency tests for single-item reads | Latency results | 12 | 2 | 5.1.1 FS +0 | Performance Engineer |
| 5.1.4 | Index and query tuning | Tune indexes and queries | Tuning changes | 20 | 3 | 5.1.2 FS +0; 5.1.3 FS +0 | Backend Developer |
| 5.1.5 | Performance report | Consolidated performance report | Performance report | 8 | 1 | 5.1.4 FS +0 | Performance Engineer |
| 5.2.1 | EN/ES/CA bundles | Complete message bundles | Message bundles | 16 | 2 | 4.4.4 FS +0 | Backend Developer |
| 5.2.2 | Translation review | Professional review of ES and CA | Review sign-off | 16 | 3 | 5.2.1 FS +0 | Localization Reviewer |
| 5.2.3 | Localization validation tests | Validate bundles against error catalog | Test evidence | 8 | 1 | 5.2.2 FS +0 | QA Engineer |
| 5.3.1 | Coverage uplift to 80% | Raise coverage to ≥ 80% | Coverage report | 24 | 3 | 4.4.4 FS +0 | QA Engineer |
| 5.3.2 | M4 security review | Security review | Security review report | 8 | 1 | 4.4.4 FS +0 | Security Analyst |
| 5.3.3 | RFC 7807 catalog verification | Verify every catalog error code | Verification evidence | 12 | 2 | 5.2.3 FS +0; 5.3.1 FS +0 | QA Engineer |
| 5.4.1 | Docker and deployment manifests | Final image and Kubernetes or cloud manifests | Deployment artifacts | 16 | 2 | 5.1.5 FS +0; 5.3.2 FS +0 | DevOps Engineer |
| 5.4.2 | Deployment health validation | Health checks in target environment | Validation evidence | 8 | 1 | 5.4.1 FS +0 | DevOps Engineer |
| 5.4.3 | Final OpenAPI and Swagger UI | Publish final specification | OpenAPI 3 specification | 8 | 1 | 5.3.3 FS +0 | Backend Developer |
| 5.4.4 | M4 gate | Release approval gate | Release approval | 4 | 1 | 5.4.2 FS +0; 5.4.3 FS +0; 5.2.3 FS +0 | Project Sponsor |
| 6.1.1 | Production provisioning | Provision production environment | Production environment | 16 | 2 | 5.4.4 FS +0 | DevOps Engineer |
| 6.1.2 | Production deployment | Deploy and smoke test | Deployed service | 8 | 1 | 6.1.1 FS +0 | DevOps Engineer |
| 6.1.3 | Monitoring and alerting | Set up monitoring and alerts | Monitoring dashboards | 12 | 2 | 6.1.1 FS +0 | DevOps Engineer |
| 6.1.4 | Operations handover | Runbook and handover | Runbook and handover pack | 12 | 2 | 6.1.2 FS +0; 6.1.3 FS +0 | Project Manager |
| 6.1.5 | Go-live acceptance | Production go-live acceptance | Go-live acceptance record | 4 | 1 | 6.1.4 FS +0 | Project Sponsor |

## Requirement Traceability Summary

| Requirement Group | WBS Packages |
| --- | --- |
| REQ-001 to REQ-005 (Location) | 3.1.x, 2.1.1 |
| REQ-006 to REQ-010 (Container) | 3.2.x, 2.1.2 |
| REQ-011 to REQ-019 (Inventory core) | 4.1.x, 4.2.x |
| REQ-020 (Audit) | 4.3.x, 2.1.3 |
| REQ-021, REQ-022 (ACID, locking) | 2.1.4, 4.2.2, 4.2.5 |
| REQ-023 (JWT/RBAC) | 2.2.x |
| REQ-024, REQ-029 to REQ-038 (Errors, localization) | 2.3.x, 5.2.x, 5.3.3 |
| REQ-025 (Observability, Docker, health) | 2.4.x, 5.4.x |
| REQ-026 (Performance) | 2.1.5, 5.1.x |
| REQ-027 (Coverage) | 2.4.4, 5.3.1 |
| REQ-028 (OpenAPI) | 2.3.5, 5.4.3 |

## Critical Path Analysis

**Critical path sequence (predictive WBS, schedule model in relative days):**
1.1.1 → 1.1.3 → 1.1.4 → 2.1.1 → 2.1.2 → 2.1.3 → 2.1.5 → 2.1.7 → 2.4.2 → 2.4.5 → 2.4.7 → 3.1.1 → 3.1.3 → 3.1.5 → 3.3.1 → 3.3.3 → 4.1.1 → 4.1.2 → 4.1.4 → 4.2.1 → 4.2.2 → 4.2.3 → 4.3.1 → 4.4.1 → 4.4.3 → 4.4.4 → 5.1.1 → 5.1.2 → 5.1.4 → 5.1.5 → 5.4.1 → 5.4.2 → 5.4.4 → 6.1.1 → 6.1.2 → 6.1.4 → 6.1.5

**Total project duration:** see schedule model (project_duration_days). Duration is a planning estimate from the dictionary durations, excluding resource leveling.

**Key schedule risks:**
* Concurrency defects in split, merge, and group operations (4.2.2–4.2.4, 4.4.1) may extend the critical path; rework is not buffered.
* 100k-record performance tuning (5.1.2, 5.1.4) may fail targets and loop back to schema or index design (2.1.5).
* Schema design is on the critical path; late changes to the data model cascade into all milestones.
* Undefined budget, team, and milestone dates (charter Section 4) mean durations are not yet resource-constrained.
* Option B evaluation (1.1.3) and baseline data (1.1.2) gate funding before M2; delays here delay M2 start.
* Compliance and security reviews (2.4.6, 5.3.2) are gate-dependent and may hold release.

**Float:** Packages not on the critical path (for example 1.2.x governance, 2.2.x security chain partially, 2.3.4, 2.3.5, 3.1.2, 3.2.3, 4.3.3, 5.2.x) carry float as recorded in the schedule model.

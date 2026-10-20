# Project Charter: Warehouse Inventory & Storage Management REST API (WMS Core)

| Field | Value |
| --- | --- |
| Document | Project Charter |
| Project Name | Warehouse Inventory & Storage Management REST API (WMS Core) |
| Current Version | 1.0 |
| Date | 2026-10-20 |

## Changelog

| Version | Date | Change Description |
| --- | --- | --- |
| 1.0 | 2026-10-20 | Initial version |

## 1. Purpose and Objectives

**Purpose:** Build a robust, scalable, and secure backend RESTful API that serves as the core engine for warehouse storage, container tracking, and stock inventory management.

**Objectives:**

1. **Full Inventory Traceability:** Complete end-to-end auditability of every stock creation, displacement, split, merge, and write-off.
2. **Operational Accuracy:** Prevent negative inventory, over-capacity placements, orphaned containers, and concurrency race conditions.
3. **Developer Experience & Seamless Integration:** Standardized REST conventions, OpenAPI documentation, and machine-readable error handling per RFC 7807 (`problem+json`).
4. **High Reliability:** Sub-second query performance and data consistency through atomic multi-resource transactions.

## 2. High-Level Scope

### 2.1 In Scope

* **Hierarchical Warehouse Locations (REQ-001 – REQ-005):** Multi-tier location tree (warehouse > zone > aisle > rack > shelf > bin); CRUD with soft delete; capacity and status enforcement; relocation without circular nesting.
* **Container Management (REQ-006 – REQ-010):** Unique SSCC/barcode tracking; nesting and movement; maximum weight and dimension validation; safe deletion; movement history.
* **Stock & Inventory Operations (REQ-011 – REQ-019):** SKU registration with quantities, UoM, lot/batch, and expiry; statuses (`available`, `blocked`, `quarantine`); full and partial moves; split and merge; grouping into containers and ungrouping; mandatory adjustment and write-off reason codes.
* **Audit Trail (REQ-020):** Immutable movement log with entity, operation, user, timestamp, before/after states, and reason.
* **Security, Architecture & NFRs (REQ-021 – REQ-038):** ACID transactions (REQ-021); optimistic locking via ETag/version (REQ-022); JWT/OAuth2 with RBAC (REQ-023); RFC 7807 errors with localization (EN, ES, CA) and leak-proof 5xx responses with `trace_id` (REQ-024, REQ-029 – REQ-038); performance and ≥ 80% test coverage (REQ-026, REQ-027); Docker, health checks, structured JSON logging, OpenAPI 3 / Swagger UI (REQ-025, REQ-028).

### 2.2 Out of Scope

* Product Master Data management (SKUs are external validated string references).
* Hardware driver integration (printer and scanner device drivers).
* Purchase order receiving and sales order shipping orchestration (future ERP integration phases).

## 3. High-Level Requirements

| Requirement Group | Requirement IDs | Summary |
| --- | --- | --- |
| Location Engine | REQ-001 – REQ-005 | Location tree, CRUD, soft delete, capacity guardrails, non-cyclic relocation |
| Container Engine | REQ-006 – REQ-010 | Unique SSCC/barcode, nesting, weight limits, safe delete, automatic stock displacement on move |
| Inventory Core | REQ-011 – REQ-019 | Positive quantities, atomic split/merge/group, reason codes |
| Audit & Logs | REQ-020 | Immutable movement log, `GET /movements` with filters |
| Architecture & Security | REQ-021 – REQ-023 | ACID, optimistic locking, JWT/OAuth2 RBAC |
| Error Handling & Localization | REQ-024, REQ-029 – REQ-038 | RFC 7807, error codes, EN/ES/CA, leak-proof 5xx |
| Performance & Quality | REQ-026, REQ-027 | p95 < 300 ms single reads; < 1 s list/aggregate over 100k records; ≥ 80% coverage in CI |
| Observability & Deployment | REQ-025, REQ-028 | Docker, health checks, JSON logs, OpenAPI 3 / Swagger UI |

**Key API endpoints (from brief):** `/locations`, `/containers`, `/stocks`, `/stocks/merge`, `/stocks/group`, `/movements`, plus move, split, ungroup, and soft-delete actions.

## 4. Key Deliverables and Milestones

| Milestone | Deliverables | Target Date |
| --- | --- | --- |
| M1: Architectural Foundation & CI/CD | DB schema with transactions and optimistic locking; auth and RBAC middleware; RFC 7807 handler; OpenAPI skeleton | <UNKNOWN> |
| M2: Location & Container Hierarchy Engine | Location and container CRUD; nested moves; capacity validation | <UNKNOWN> |
| M3: Stock Management & Advanced Movements | Stock allocations; atomic split, merge, and grouping; movement history logging | <UNKNOWN> |
| M4: Performance, Localization & Production Readiness | 100k-record stress testing; EN/ES/CA message bundles; Docker image delivery; ≥ 80% test suite | <UNKNOWN> |

Milestone dates are not provided in the brief and must be set during planning.

## 5. Constraints

* **Technical:** Containerized (Docker) deployment to Kubernetes or cloud; RESTful JSON; RFC 7807 errors; OpenAPI 3.
* **Performance:** p95 < 300 ms for single-item reads; < 1 s for list and aggregate queries over 100k records.
* **Security & Compliance:** No stack traces, database schema details, or library versions exposed externally; RBAC with three roles.
* **Quality:** ≥ 80% automated test coverage in CI.
* **Budget and schedule:** <UNKNOWN>. Not provided in source input.
* **Resources:** <UNKNOWN>. Team composition not provided.

## 6. Assumptions

* SKUs are validated by an external master data source.
* Hardware integration is handled outside this project.
* An identity provider for JWT/OAuth2 is available or will be supplied by the organization.
* Target runtime is Docker-based, deployable to Kubernetes or cloud.
* 100k records represent the planning-horizon data volume (to be confirmed).
* Localization scope is limited to EN, ES, and CA.
* Consumer systems and integration partners will be identified during Milestone 1.

## 7. High-Level Risks

| Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- |
| Concurrency defects in split, merge, and group operations | Medium | High | Atomic transactions, optimistic locking, concurrency tests in CI |
| Performance targets missed at 100k records | Medium | High | Early performance testing; index strategy in M1 |
| Scope creep into ERP or hardware integration | Medium | Medium | Change control against out-of-scope list |
| Budget and schedule undefined | High | High | Establish estimates and milestone dates before M2 |
| Security gaps in RBAC or error masking | Low | High | Security reviews at M1 and M4 |

## 8. Delivery Cadence

* **Approach:** Hybrid. Predictive governance (fixed scope boundaries, milestone gates, documented contracts) with adaptive delivery inside each milestone.
* **Iterations:** Time-boxed iterations; cadence length to be confirmed by the project manager (assumed two weeks).
* **Milestone reviews:** A formal review and gate at the end of each milestone (M1–M4).
* **Stakeholder demos:** Working demonstrations at the end of each iteration.
* **Change control:** Changes to scope or strategic objectives require a formal change request and sponsor approval.

## 9. Success Criteria

1. All in-scope requirements REQ-001 – REQ-038 are implemented and accepted.
2. Zero negative-inventory, over-capacity, or orphaned-container records in acceptance testing.
3. Every stock mutation produces an immutable movement log entry.
4. p95 latency: < 300 ms single-item reads; < 1 s list and aggregate queries over 100k records.
5. Automated CI test coverage ≥ 80%.
6. OpenAPI 3 specification and Swagger UI published; RFC 7807 errors verified across the error code catalog.
7. Docker image delivered and passing health checks.
8. EN, ES, and CA message bundles validated.

## 10. Sponsor and Project Manager Authority

**Project Sponsor (name: <UNKNOWN>)**
* Approves the business case, charter, and any change to strategic objectives (major version changes).
* Approves funding releases and milestone gate decisions.
* Resolves cross-functional escalations.

**Project Manager (name: <UNKNOWN>)**
* Plans and manages day-to-day delivery, schedule, and resources within approved scope and budget.
* Manages minor changes (refinements) and maintains the risk and issue logs.
* Escalates to the sponsor any change that alters scope, budget, or strategic objectives.
* Authority limits (budget thresholds, approval ceilings) to be confirmed by the sponsor.

## 11. Approval

| Role | Name | Signature | Date |
| --- | --- | --- | --- |
| Project Sponsor | <UNKNOWN> | | |
| Project Manager | <UNKNOWN> | | |
| Business Owner | <UNKNOWN> | | |
| Technical Lead | <UNKNOWN> | | |

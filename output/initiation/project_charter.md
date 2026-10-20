# Project Charter: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 1.0
- **Date:** 2026-10-20

### Changelog
| Version | Date | Change Description |
|---|---|---||
| 1.0 | 2026-10-20 | Initial version of the project charter. |

---

## 1. Purpose & Objectives
To build a robust, scalable, and secure backend RESTful API serving as the core engine for warehouse storage, container tracking, and stock inventory management. Key objectives include full inventory traceability, operational accuracy, superior developer experience, and high system reliability.

---

## 2. High-Level Scope
### In Scope
1. **Hierarchical Warehouse Locations (REQ-001 – REQ-005):** Multi-tier tree (`warehouse` > `zone` > `aisle` > `rack` > `shelf` > `bin`), lifecycle CRUD, capacity/status enforcement, and non-cyclic relocation.
2. **Container Management (REQ-006 – REQ-010):** SSCC/barcode tracking, nesting support, weight/dimension constraints, and movement history.
3. **Stock & Inventory Operations (REQ-011 – REQ-019):** SKU tracking, status handling (`available`, `blocked`, `quarantine`), full/partial movements, split/merge/group/ungroup operations, and mandatory reason codes.
4. **Audit Trail & Traceability (REQ-020):** Immutable movement logs capturing entity, operation, user, timestamp, state changes, and reasons.
5. **Security, Architecture & Cross-Cutting (REQ-021 – REQ-038):** ACID transactions, optimistic locking (ETag), JWT/OAuth2 RBAC, RFC 7807 error responses, p95 latency targets, and OpenAPI 3 specs.

### Out of Scope
- Product Master Data management (SKUs accepted as external references).
- Hardware driver integration (direct physical printer or scanner device drivers).
- Purchase order receiving / sales order shipping orchestration.

---

## 3. High-Level Requirements
- Provide standardized REST conventions, JSON responses, and RFC 7807 error handling (`application/problem+json`).
- Guarantee data consistency via atomic multi-resource transactions and optimistic concurrency locking.
- Enforce role-based access control across `admin`, `supervisor`, and `operator` roles.
- Achieve p95 latency < 300ms for single reads and < 1s for list/aggregate queries over 100k records.

---

## 4. Key Deliverables & Milestones
1. **Milestone 1: Architectural Foundation & CI/CD Setup**
   - Database schema design, transaction support, optimistic locking, RBAC middleware, RFC 7807 error handler, and OpenAPI skeleton.
2. **Milestone 2: Location & Container Hierarchy Engine**
   - Location and container CRUD, nested moves, and capacity validation.
3. **Milestone 3: Stock Management & Advanced Movements**
   - Stock allocations, split/merge/grouping atomic transactions, and movement history logging.
4. **Milestone 4: Performance, Localization & Production Readiness**
   - Stress testing (100k records), EN/ES/CA message bundles, Docker image delivery, and $\ge 80\%$ test coverage.

---

## 5. Constraints
- Strict adherence to REST semantics and RFC 7807 error standards.
- Zero exposure of internal stack traces or database schema details in error responses.
- Execution within containerized (Docker/Kubernetes) target environments.

---

## 6. Assumptions
- External systems supply valid SKU master data references.
- Client applications handle physical barcode scanning and hardware presentation layer duties.

---

## 7. High-Level Risks
- Concurrency bottlenecks during high-frequency stock updates.
- Complexity in validating hierarchical container nesting constraints and capacity limits.

---

## 8. Delivery Cadence
- Iterative milestone-based delivery with continuous integration, automated testing ($\ge 80\%$ coverage), and structured code reviews.

---

## 9. Success Criteria
- Complete end-to-end auditability of inventory movements.
- Zero negative inventory or orphaned container states.
- Successful passage of performance benchmarks (p95 latency thresholds).
- Full compliance with security, RBAC, and error-handling specifications.

---

## 10. Sponsor & Project Manager Authority
- **Project Sponsor:** Authorized to approve strategic scope adjustments, milestone sign-offs, and final deployment release.
- **Project Manager / Tech Lead:** Authorized to manage day-to-day backlog prioritization, sprint execution, technical architecture decisions, and resource allocation within approved project boundaries.

---

## 11. Approval Section
- **Sponsor Signature:** _________________ Date: __________
- **Project Manager Signature:** _________________ Date: __________
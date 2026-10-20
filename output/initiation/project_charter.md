# Project Charter: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 1.0
- **Date:** 2026-10-20

## Changelog
| Version | Date | Change Description |
|---|---|---|
| 1.0 | 2026-10-20 | Initial version. Draft created at project inception from the Project Brief, and aligned with the existing approved 1.0 Project Charter dated 2026-10-17 (no strategic objectives renegotiated). |

## 1. Purpose and Objectives
To build a robust, scalable, and secure backend RESTful API that serves as the core engine for warehouse storage, container tracking, and stock inventory management, ensuring 100% auditability and transactional integrity.

## 2. High-Level Scope
### In Scope
- Hierarchical Warehouse Locations (`warehouse` > `zone` > `aisle` > `rack` > `shelf` > `bin`) and lifecycle management.
- Container Management (SSCC/barcode tracking, nesting, weight/dimension constraints).
- Stock & Inventory Operations (SKU registration, status handling, full/partial movements, split, merge, group, ungroup, reason codes).
- Audit Trail & Traceability (immutable movement log).
- Security, Architecture & Non-Functional Requirements (ACID transactions, optimistic locking, JWT/OAuth2 RBAC, RFC 7807 error handling, performance targets, Docker containerization).

### Out of Scope
- Product Master Data management (SKUs accepted as external validated string references).
- Hardware driver integration (direct physical printer or scanner device drivers).
- Purchase order receiving / sales order shipping orchestration.

## 3. High-Level Requirements
- REQ-001 through REQ-020: Location, Container, Stock, and Audit capabilities.
- REQ-021 through REQ-038: ACID transactions, optimistic locking (ETag), RBAC (`admin`, `supervisor`, `operator`), RFC 7807 error responses, p95 latency targets (< 300ms single, < 1s list over 100k records), automated CI with $\ge 80\%$ test coverage, and Dockerized deployment.

## 4. Key Deliverables and Milestones
1. **Milestone 1: Architectural Foundation & CI/CD Setup:** Database schema, auth middleware, RFC 7807 error handler, OpenAPI skeleton.
2. **Milestone 2: Location & Container Hierarchy Engine:** CRUD, nested moves, capacity validation.
3. **Milestone 3: Stock Management & Advanced Movements:** Allocations, split/merge/group transactions, movement logging.
4. **Milestone 4: Performance, Localization & Production Readiness:** Stress testing (100k records), EN/ES/CA localization, Docker image, $\ge 80\%$ test coverage.

## 5. Constraints
- Strict adherence to RESTful JSON semantics and RFC 7807 error formats.
- Zero exposure of internal stack traces or database schemas in error responses.

## 6. Assumptions
- SKUs are validated externally prior to submission.
- External systems support HTTP status standards and ETag concurrency headers.

## 7. High-Level Risks
- Concurrency race conditions during simultaneous stock adjustments.
- Query performance degradation over massive inventory volumes.

## 8. Delivery Cadence
- Milestone-driven iterative delivery with continuous CI/CD validation.

## 9. Success Criteria
- Full inventory traceability and zero negative inventory or orphaned containers.
- Achievement of p95 latency thresholds and $\ge 80\%$ test coverage.

## 10. Sponsor and Project Manager Authority
- Project Manager is authorized to manage day-to-day execution within approved milestones.
- Sponsor holds authority for milestone sign-offs and scope boundary changes.

## 11. Approval Section
- **Approved By:** Project Sponsor
- **Date:** 2026-10-17
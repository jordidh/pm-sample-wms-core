# Product Backlog: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 1.1
- **Date:** 2026-10-20

## Changelog
| Version | Date | Change Description |
|---|---|---|
| 1.0 | 2026-10-17 | Initial backlog baseline for adaptive Milestones 2, 3, and 4 |
| 1.1 | 2026-10-20 | Baseline planning refinement: added MoSCoW priority and dependency fields to each item, added explicit technical dependencies (FS/SS) between backlog items, and aligned the release plan with the charter milestones. Item scope, story points, and IDs are unchanged. Minor refinement, so the version is bumped from 1.0 to 1.1. |

## Assumptions
- Story points are relative estimates carried over from backlog 1.0. They are not converted to hours here.
- Milestone 1 (predictive) is excluded from this backlog and is governed by the WBS.
- Feature 1.1.2, 2.1.2, and 3.1.2 are referenced in the Release Plan of backlog 1.0 but are not defined in the backlog. They are recorded as an open gap and not invented here.
- No scope change request was received during baseline planning.

## 1. Backlog Hierarchy (Themes, Epics, Features, User Stories)

### Theme 1: Location & Container Management (Milestone 2)
#### Epic 1.1: Hierarchical Location Engine
- **Feature 1.1.1:** Location CRUD & Lifecycle
  - **US-101 (Must Have, 5 SP):** As an Admin, I want to create, read, update, and deactivate warehouse locations (`warehouse` > `zone` > `aisle` > `rack` > `shelf` > `bin`) so that the physical layout is accurately represented.
    - Dependencies: Milestone 1 1.1.1 (FS), 1.2.2 (FS), 1.3.2 (FS)
  - **US-102 (Should Have, 3 SP):** As a Supervisor, I want to query location capacity constraints so that over-allocations are prevented.
    - Dependencies: US-101 (FS)
#### Epic 1.2: Container Tracking & Nesting
- **Feature 1.2.1:** Container Operations & SSCC/Barcode
  - **US-103 (Must Have, 5 SP):** As an Operator, I want to register containers with SSCC/barcodes, weight, and dimension constraints so that physical stock can be securely held.
    - Dependencies: US-101 (FS), 1.2.2 (FS)
  - **US-104 (Must Have, 5 SP):** As an Operator, I want to nest containers inside other containers (e.g., boxes on a pallet) so that complex shipments are tracked accurately.
    - Dependencies: US-103 (FS)

### Theme 2: Stock Management & Advanced Movements (Milestone 3)
#### Epic 2.1: Inventory & Stock Operations
- **Feature 2.1.1:** Stock Registration & Status
  - **US-201 (Must Have, 8 SP):** As an Operator, I want to register incoming stock SKUs with status handling and batch/expiry tracking so that inventory freshness is maintained.
    - Dependencies: US-103 (FS), US-101 (FS)
  - **US-202 (Must Have, 8 SP):** As an Operator, I want to execute full and partial stock movements between locations and containers using reason codes so that inventory location is auditable.
    - Dependencies: US-201 (FS), US-102 (FS), US-104 (FS)
#### Epic 2.2: Advanced Stock Transformations
- **Feature 2.2.1:** Split, Merge, Group & Audit
  - **US-203 (Should Have, 5 SP):** As a Supervisor, I want to split, merge, group, and ungroup stock items with atomic transactions so that inventory adjustments do not cause data discrepancies.
    - Dependencies: US-202 (FS), US-201 (FS)
  - **US-204 (Must Have, 5 SP):** As an Auditor, I want an immutable audit log recording every inventory movement and adjustment so that 100% traceability is guaranteed.
    - Dependencies: US-202 (SS), US-203 (SS)

### Theme 3: Performance, Localization & Production Readiness (Milestone 4)
#### Epic 3.1: Performance & Concurrency
- **Feature 3.1.1:** Optimization & Locking
  - **US-301 (Must Have, 5 SP):** As a System Integrator, I want optimistic locking via ETag/version headers on all mutating resources so that concurrency race conditions are eliminated.
    - Dependencies: US-101 (FS), US-103 (FS), US-201 (FS), US-203 (FS)
  - **US-302 (Must Have, 5 SP):** As a System Administrator, I want sub-second query performance (p95 < 300ms single, < 1s list over 100k records) so that high-volume operations run smoothly under load.
    - Dependencies: US-202 (FS), US-204 (FS)
#### Epic 3.2: Localization & Delivery
- **Feature 3.2.1:** Multi-language & Deployment
  - **US-303 (Could Have, 3 SP):** As an International User, I want error messages and metadata supported in EN, ES, and CA so that regional operations are supported.
    - Dependencies: 1.3.1 (FS)
  - **US-304 (Must Have, 5 SP):** As a DevOps Engineer, I want >= 80% test coverage and production-ready Docker containers so that secure deployments are repeatable.
    - Dependencies: 1.4.2 (FS), US-302 (FS), US-301 (FS)

## 2. Release & Iteration Plan
- **Release 1 (Milestone 2 Iteration):** Features 1.1.1, 1.2.1 (Location & Container Engine). Feature 1.2.2 is not defined in backlog 1.0 and is recorded as an open gap.
- **Release 2 (Milestone 3 Iteration):** Features 2.1.1, 2.2.1 (Stock Operations & Audit Log). Feature 2.1.2 is not defined in backlog 1.0 and is recorded as an open gap.
- **Release 3 (Milestone 4 Iteration):** Features 3.1.1, 3.2.1 (Performance, Concurrency, Localization & Production Readiness). Feature 3.1.2 is not defined in backlog 1.0 and is recorded as an open gap.

## 3. Open Gaps
- Feature references 1.1.2, 1.2.2, 2.1.2, and 3.1.2 appear in the release plan but have no backlog definition. Sponsor or product owner clarification is required before the Release Plan is baselined.
- Story points are not converted to hours or durations. Velocity is not stated in the source materials.
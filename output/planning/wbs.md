# WBS: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 1.1
- **Date:** 2026-10-20

## Changelog
| Version | Date | Change Description |
|---|---|---|
| 1.0 | 2026-10-17 | Initial WBS baseline for Milestone 1 (Architectural Foundation & CI/CD Setup) |
| 1.1 | 2026-10-20 | Baseline planning refinement: re-validated the Milestone 1 WBS against the 100% rule and the < 40 hour work package rule, added the Milestone 2-4 summary branches as placeholders that point to the adaptive backlog (no new predictive work packages), added the explicit dependency type and lag columns, and confirmed the schedule model. No change to Milestone 1 scope, effort, or durations. Minor refinement, so the version is bumped from 1.0 to 1.1. |

## Assumptions
- The approach is hybrid. Milestone 1 is predictive and is decomposed here. Milestones 2-4 are iterative and are decomposed in the Product Backlog, so they appear here only as summary nodes.
- Effort is in hours and duration is in calendar working days, as stated in the source WBS 1.0. Lag is 0 for all dependencies because none is stated in the source.
- Owner roles are taken from the source WBS 1.0. No named individuals are assigned.
- No scope change request was received during baseline planning.

## 1. WBS Hierarchy (Predictive Components)
1. Milestone 1: Architectural Foundation & CI/CD Setup (Predictive)
   1.1. Database Schema & Migration Setup
      1.1.1. Core PostgreSQL Schema Design (Locations, Containers, Stock)
      1.1.2. Migration Scripts & Rollback Mechanisms
   1.2. Security & Authentication Middleware
      1.2.1. JWT/OAuth2 RBAC Token Validation
      1.2.2. Role-Based Permission Enforcement Decorators
   1.3. Error Handling & OpenAPI Framework
      1.3.1. RFC 7807 problem+json Exception Handlers
      1.3.2. OpenAPI/Swagger Documentation Skeleton Setup
   1.4. CI/CD Pipeline & Infrastructure
      1.4.1. Dockerfile & Containerization Setup
      1.4.2. Automated CI Pipeline with Test Coverage Gates
2. Milestone 2: Location & Container Hierarchy Engine (Adaptive; see Product Backlog Theme 1, Release 1)
3. Milestone 3: Stock Management & Advanced Movements (Adaptive; see Product Backlog Theme 2, Release 2)
4. Milestone 4: Performance, Localization & Production Readiness (Adaptive; see Product Backlog Theme 3, Release 3)

Note on the 100% rule: Level 1 items 1-4 cover the full project scope in the charter (In Scope items and Milestones 1-4). Work packages 1.1.1 through 1.4.2 are the leaf-level predictive packages. Each leaf is under 40 hours (maximum 24 hours).

## 2. WBS Dictionary Table
| Code | Name | Description | Deliverable | Effort (Hours) | Duration (Days) | Predecessors | Owner Role |
|---|---|---|---|---|---|---|---|
| 1.1.1 | Core PostgreSQL Schema Design | Design tables, foreign keys, and indexes for location, container, and stock entities. | ERD Diagram & Migration Files | 24 | 3 | None | Database Architect |
| 1.1.2 | Migration Scripts & Rollback | Implement flyway/alembic migration scripts with verified rollback paths. | Migration Tooling Config | 16 | 2 | 1.1.1 (FS, lag 0) | Backend Developer |
| 1.2.1 | JWT/OAuth2 RBAC Token Validation | Implement token parsing and validation middleware for incoming requests. | Auth Middleware Module | 24 | 3 | None | Security Engineer |
| 1.2.2 | Role-Based Permission Decorators | Implement role checks (`admin`, `supervisor`, `operator`) across endpoints. | RBAC Enforcement Decorators | 16 | 2 | 1.2.1 (FS, lag 0) | Backend Developer |
| 1.3.1 | RFC 7807 Exception Handlers | Build standardized problem+json error formatting and global exception handling. | Error Handling Middleware | 16 | 2 | None | Backend Developer |
| 1.3.2 | OpenAPI Documentation Skeleton | Configure Swagger/OpenAPI generators with standard response headers. | OpenAPI Skeleton Config | 16 | 2 | 1.3.1 (FS, lag 0) | Technical Writer |
| 1.4.1 | Dockerfile & Containerization | Create multi-stage production Dockerfile and docker-compose orchestration. | Docker Artifacts | 16 | 2 | 1.1.2 (FS, lag 0), 1.3.2 (FS, lag 0) | DevOps Engineer |
| 1.4.2 | Automated CI Pipeline & Coverage | Configure CI pipeline enforcing >= 80% unit test coverage on base skeleton. | CI Pipeline Config | 24 | 3 | 1.4.1 (FS, lag 0) | DevOps Engineer |

Total Milestone 1 effort: 152 hours.

## 3. Critical Path Analysis
- **Critical Path Sequence:** 1.1.1 -> 1.1.2 -> 1.4.1 -> 1.4.2
- **Total Project Duration (Milestone 1):** 10 days (assuming parallel tracks for auth and error handling where float permits).
- **Activities with Float:**
  - 1.2.1 (Auth Token Validation): 0 float (drives 1.2.2). Note: 1.2.1 is not on the critical path by the forward/backward pass, which shows 1.2.2 with 2 days total float; the source label "0 float" applies to 1.2.1 as it is the head of the 1.2 branch.
  - 1.2.2 (RBAC Decorators): 2 days float
  - 1.3.1 (RFC 7807 Handlers): 0 float (drives 1.3.2)
  - 1.3.2 (OpenAPI Skeleton): 1 day float (feeds 1.4.1 which has 0 float)
- **Key Schedule Risks:**
  - Schema migration delays blocking containerization.
  - Strict test coverage gating failing pipeline execution.
  - Float on the 1.2 and 1.3 branches is consumed if 1.4.1 slips, since 1.4.1 depends on both 1.1.2 and 1.3.2.

## 4. Scope Boundary
- Out of scope (per charter): Product Master Data management, hardware driver integration, purchase order receiving and sales order shipping orchestration.
- Milestone 2-4 functional decomposition is held in the Product Backlog and is not duplicated here, to avoid two sources of truth for the same scope.
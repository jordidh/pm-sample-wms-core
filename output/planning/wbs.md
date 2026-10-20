# WBS: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 1.0
- **Date:** 2026-10-20

### Changelog
| Version | Date | Change Description |
|---|---|---|
| 1.0 | 2026-10-20 | Initial WBS baseline version. |

---

## 1. WBS Hierarchy (100% Rule)
- 1. WMS Core Project
  - 1.1 Architectural Foundation & CI/CD Setup
    - 1.1.1 Database Schema Design & ACID Setup (ID: 1.1.1)
    - 1.1.2 Optimistic Locking & ETag Middleware (ID: 1.1.2)
    - 1.1.3 RBAC Security Framework (ID: 1.1.3)
    - 1.1.4 RFC 7807 Error Handling Handler (ID: 1.1.4)
    - 1.1.5 OpenAPI 3 Specification Skeleton (ID: 1.1.5)
  - 1.2 Location & Container Hierarchy Engine
    - 1.2.1 Location Tree CRUD & Capacity Engine (ID: 1.2.1)
    - 1.2.2 Non-Cyclic Relocation Validator (ID: 1.2.2)
    - 1.2.3 Container SSCC Tracking & Nesting (ID: 1.2.3)
  - 1.3 Stock Management & Advanced Movements
    - 1.3.1 SKU Tracking & Status Engine (ID: 1.3.1)
    - 1.3.2 Atomic Stock Movements & Reason Codes (ID: 1.3.2)
    - 1.3.3 Split, Merge, Group, and Ungroup Operations (ID: 1.3.3)
    - 1.3.4 Immutable Audit Trail Logger (ID: 1.3.4)
  - 1.4 Performance, Localization & Production Readiness
    - 1.4.1 Performance Stress Testing & Optimization (ID: 1.4.1)
    - 1.4.2 Multi-Language Message Bundles (EN/ES/CA) (ID: 1.4.2)
    - 1.4.3 Docker Containerization & CI/CD Pipeline (ID: 1.4.3)
    - 1.4.4 Test Coverage Verification (>=80%) (ID: 1.4.4)

---

## 2. WBS Dictionary Table
| Code | Name | Description | Deliverable | Effort (Hours) | Duration (Days) | Predecessors | Owner Role |
|---|---|---|---|---|---|---|---|
| 1.1.1 | Database Schema Design & ACID Setup | Establish PostgreSQL core schema, tables, foreign keys, and multi-resource ACID transaction handling. | DDL scripts & transaction manager | 32 | 4 | None | Lead Architect |
| 1.1.2 | Optimistic Locking & ETag Middleware | Implement versioning headers and ETag validation to prevent concurrent race conditions. | Middleware module | 24 | 3 | 1.1.1 (FS) | Backend Engineer |
| 1.1.3 | RBAC Security Framework | Implement JWT/OAuth2 verification and role-based access control for admin, supervisor, and operator. | Security filter & auth guards | 24 | 3 | 1.1.1 (FS) | Security Engineer |
| 1.1.4 | RFC 7807 Error Handling Handler | Implement application/problem+json error responses without leaking stack traces. | Global exception handler | 16 | 2 | 1.1.1 (FS) | Backend Engineer |
| 1.1.5 | OpenAPI 3 Specification Skeleton | Generate base swagger specification structure matching REST conventions. | openapi.yaml | 16 | 2 | 1.1.1 (FS) | Technical Writer |
| 1.2.1 | Location Tree CRUD & Capacity Engine | Develop multi-tier warehouse location tree management and capacity/status rules. | Location service module | 32 | 4 | 1.1.2 (FS), 1.1.3 (FS) | Backend Engineer |
| 1.2.2 | Non-Cyclic Relocation Validator | Validate that moving tree nodes does not introduce circular parent-child references. | Validation service | 24 | 3 | 1.2.1 (FS) | Backend Engineer |
| 1.2.3 | Container SSCC Tracking & Nesting | Implement container lifecycle, SSCC/barcode checks, nesting limits, and weight constraints. | Container module | 32 | 4 | 1.2.1 (FS) | Backend Engineer |
| 1.3.1 | SKU Tracking & Status Engine | Manage inventory items, SKU references, and status flags (available, blocked, quarantine). | Stock inventory module | 32 | 4 | 1.2.3 (FS) | Backend Engineer |
| 1.3.2 | Atomic Stock Movements & Reason Codes | Execute full and partial stock movements with mandatory reason code enforcement. | Movement transaction engine | 36 | 5 | 1.3.1 (FS) | Backend Engineer |
| 1.3.3 | Split, Merge, Group, and Ungroup Operations | Implement complex inventory transformations via atomic database transactions. | Transformation service | 32 | 4 | 1.3.2 (FS) | Backend Engineer |
| 1.3.4 | Immutable Audit Trail Logger | Record immutable logs for all movements, state transitions, users, and timestamps. | Audit logging engine | 24 | 3 | 1.3.3 (FS) | Backend Engineer |
| 1.4.1 | Performance Stress Testing & Optimization | Run performance benchmarks ensuring p95 < 300ms reads and < 1s for 100k records. | Performance report | 32 | 4 | 1.3.4 (FS) | QA / Performance Engineer |
| 1.4.2 | Multi-Language Message Bundles (EN/ES/CA) | Add localizable error and response message bundles for English, Spanish, and Catalan. | Message bundle files | 16 | 2 | 1.1.4 (FS) | Localization Engineer |
| 1.4.3 | Docker Containerization & CI/CD Pipeline | Containerize the service and establish automated build/test pipelines. | Dockerfile & CI script | 24 | 3 | 1.4.1 (FS), 1.4.2 (FS) | DevOps Engineer |
| 1.4.4 | Test Coverage Verification (>=80%) | Run comprehensive test suites to confirm unit and integration test coverage meets >=80%. | Coverage audit report | 16 | 2 | 1.4.3 (FS) | QA Engineer |

---

## 3. Critical Path Analysis
- **Critical Path Sequence:** 1.1.1 -> 1.1.2 -> 1.2.1 -> 1.2.2 -> 1.2.3 -> 1.3.1 -> 1.3.2 -> 1.3.3 -> 1.3.4 -> 1.4.1 -> 1.4.3 -> 1.4.4
- **Total Project Duration:** 42 days
- **Activities with Float:**
  - 1.1.3 (RBAC Security Framework): Float = 0 (Critical Path alternative branch via 1.2.1)
  - 1.1.4 (RFC 7807 Error Handling Handler): Float = 2 days
  - 1.1.5 (OpenAPI 3 Specification Skeleton): Float = 2 days
  - 1.4.2 (Multi-Language Message Bundles): Float = 2 days
- **Key Schedule Risks:**
  - High concurrency race conditions during stock mutations delaying performance stress testing.
  - Complex hierarchical tree queries impacting p95 list query latencies under 100k records load.
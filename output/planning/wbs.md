# WBS: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 1.0
- **Date:** 2026-10-17

## Changelog
| Version | Date | Change Description |
|---|---|---|
| 1.0 | 2026-10-17 | Initial WBS baseline for Milestone 1 (Architectural Foundation & CI/CD Setup) |

## 1. WBS Hierarchy (Predictive Components)
1. Milestone 1: Architectural Foundation & CI/CD Setup
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

## 2. WBS Dictionary Table
| Code | Name | Description | Deliverable | Effort (Hours) | Duration (Days) | Predecessors | Owner Role |
|---|---|---|---|---|---|---|---|
| 1.1.1 | Core PostgreSQL Schema Design | Design tables, foreign keys, and indexes for location, container, and stock entities. | ERD Diagram & Migration Files | 24 | 3 | None | Database Architect |
| 1.1.2 | Migration Scripts & Rollback | Implement flyway/alembic migration scripts with verified rollback paths. | Migration Tooling Config | 16 | 2 | 1.1.1 (FS) | Backend Developer |
| 1.2.1 | JWT/OAuth2 RBAC Token Validation | Implement token parsing and validation middleware for incoming requests. | Auth Middleware Module | 24 | 3 | None | Security Engineer |
| 1.2.2 | Role-Based Permission Decorators | Implement role checks (`admin`, `supervisor`, `operator`) across endpoints. | RBAC Enforcement Decorators | 16 | 2 | 1.2.1 (FS) | Backend Developer |
| 1.3.1 | RFC 7807 Exception Handlers | Build standardized problem+json error formatting and global exception handling. | Error Handling Middleware | 16 | 2 | None | Backend Developer |
| 1.3.2 | OpenAPI Documentation Skeleton | Configure Swagger/OpenAPI generators with standard response headers. | OpenAPI Skeleton Config | 16 | 2 | 1.3.1 (FS) | Technical Writer |
| 1.4.1 | Dockerfile & Containerization | Create multi-stage production Dockerfile and docker-compose orchestration. | Docker Artifacts | 16 | 2 | 1.1.2 (FS), 1.3.2 (FS) | DevOps Engineer |
| 1.4.2 | Automated CI Pipeline & Coverage | Configure CI pipeline enforcing $\ge 80\%$ unit test coverage on base skeleton. | CI Pipeline Config | 24 | 3 | 1.4.1 (FS) | DevOps Engineer |

## 3. Critical Path Analysis
- **Critical Path Sequence:** 1.1.1 $\rightarrow$ 1.1.2 $\rightarrow$ 1.4.1 $\rightarrow$ 1.4.2
- **Total Project Duration (Milestone 1):** 10 days (assuming parallel tracks for auth and error handling where float permits).
- **Activities with Float:** 
  - 1.2.1 (Auth Token Validation): 0 float (drives 1.2.2)
  - 1.2.2 (RBAC Decorators): 2 days float
  - 1.3.1 (RFC 7807 Handlers): 0 float (drives 1.3.2)
  - 1.3.2 (OpenAPI Skeleton): 0 float (drives 1.4.1)
- **Key Schedule Risks:** Schema migration delays blocking containerization, and strict test coverage gating failing pipeline execution.
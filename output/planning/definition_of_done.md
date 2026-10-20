# Definition of Done (DoD): Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 2.0
- **Date:** 2026-10-20

## Changelog
| Version | Date | Change Description |
|---|---|---|
| 1.0 | 2026-10-17 | Initial Definition of Done baseline covering story, feature, iteration, and release levels |
| 2.0 | 2026-10-20 | Baseline planning re-issue. Retained the four DoD levels from v1.0 and added the explicit criteria required for code quality, testing, documentation, review, security, deployment, and acceptance sign-off at each level. Added work package (WBS) level criteria for Milestone 1, a Definition of Ready reference, and the review and evolution process. Version bumped from 1.0 to 2.0 because the quality gates now apply to the Milestone 1 work packages and to security and release criteria that were not previously defined. |

## Assumptions
- Quality thresholds are taken from the Team Charter (>= 80% coverage, peer review before merge) and the Project Charter (p95 targets, RFC 7807, ACID, optimistic locking).
- Numeric thresholds not stated in the source (for example, the vulnerability severity threshold beyond "high or critical", and the container health check startup window) are recorded as <UNKNOWN> and listed in Open Items.
- Tooling (GitHub, Jira, CI platform, linters, scanners) is carried over from the Team Charter as working assumptions and is to be confirmed.
- Milestone 1 work packages (WBS 1.1.1 to 1.4.2) are predictive. Milestones 2 to 4 are backlog items (US-101 to US-304) and are governed by the story and feature levels.
- Requirement IDs referenced below are from Requirements Specification v2.0.

## 0. Definition of Ready (Entry Criteria)
A story or work package may start only when:
- Acceptance criteria are written in Given/When/Then form or as a testable statement, and each references a requirement ID (for example, REQ-001).
- Predecessors listed in the WBS dictionary or the Product Backlog are complete (FS dependencies) or the SS dependency is satisfied.
- Owner role is assigned (WBS Owner Role field, or Backlog team assignment).
- Open gaps that affect the item are either resolved or explicitly accepted by the Project Manager.

## 1. Story / Work Package Level
Applies to each user story (US-xxx) and each WBS work package (1.x.x).

- **Code Quality & Style:** Code follows the project architecture and passes the static analysis linters with zero warnings.
- **Peer Review:** Code is peer-reviewed and approved by at least one other engineer through a Pull Request before merge (Team Charter Section 2).
- **Unit Testing:** Unit tests cover normal, boundary, and error cases for each acceptance criterion, and contribute to the >= 80% coverage threshold (REQ-015).
- **Traceability:** Each change references the story or work package code and the requirement ID(s) it satisfies, and the Requirements Traceability Matrix is updated where a new requirement is introduced.
- **Local Verification:** All new and existing unit tests pass in the local development environment.
- **Security Hygiene:** No secrets, credentials, or tokens are committed to the repository. Authentication and authorization paths are covered by tests for REQ-011 and REQ-024 where the story touches them.
- **Work Package Specific Outputs:**
  - WBS 1.1.1: ERD Diagram and migration files reviewed by the Database Architect; foreign keys and indexes exist for location, container, and stock entities (REQ-019, REQ-020).
  - WBS 1.1.2: Each migration has a verified rollback that restores the prior schema (REQ-027).
  - WBS 1.2.1: Token validation rejects missing, expired, and malformed tokens with HTTP 401 (REQ-024).
  - WBS 1.2.2: Role checks for `admin`, `supervisor`, and `operator` return HTTP 403 for unauthorized access (REQ-011).
  - WBS 1.3.1: Every 4xx and 5xx response uses `application/problem+json` with no stack trace or database schema text (REQ-012).
  - WBS 1.3.2: OpenAPI document is generated and lists the skeleton endpoints (REQ-026).
  - WBS 1.4.1: Multi-stage Dockerfile and docker-compose file build successfully (REQ-016).
  - WBS 1.4.2: CI pipeline fails any build below 80% coverage (REQ-015).

## 2. Feature Level
Applies to each feature (for example, 1.1.1, 1.2.1, 2.1.1, 2.2.1, 3.1.1, 3.2.1) and its user stories.

- **Integration Testing:** Automated integration tests cover database interactions and API endpoints for each acceptance criterion of the feature's stories.
- **Error Handling:** All error responses for the feature conform to RFC 7807 (REQ-012), and localized messages are verified for EN, ES, and CA where REQ-014 applies.
- **API Documentation:** OpenAPI documentation is updated for new or modified endpoint schemas, parameters, error responses, and response headers (REQ-026).
- **Concurrency & ACID:** Mutating operations use optimistic locking (ETag) where REQ-010 applies, and failure paths are tested to show full rollback with no partial state (REQ-009).
- **Data Integrity:** Tests confirm no negative stock quantities (REQ-019), no orphaned containers or stock (REQ-020), and no cycles in container nesting (REQ-018), where the feature touches them.
- **Audit Logging:** Each feature that performs an inventory movement, transfer, or adjustment writes an audit entry verified by test (REQ-008), and the audit log is verified as non-modifiable (REQ-021).
- **Feature Acceptance:** The Product Owner or Project Manager confirms that every story in the feature meets its acceptance criteria.

## 3. Iteration / Increment Level
Applies to each milestone iteration (Release 1 = Milestone 2, Release 2 = Milestone 3, Release 3 = Milestone 4; Milestone 1 is the foundation increment).

- **Regression Testing:** Full automated test suite passes in the CI pipeline with no regressions.
- **Coverage Gate:** The CI quality gate verifies total test coverage is >= 80% (REQ-015).
- **Static and Dependency Checks:** Static analysis reports zero warnings, and dependency scans run in CI with results recorded.
- **Container Verification:** Application builds into a multi-stage Docker container, and the health check endpoint returns healthy within the startup window of <UNKNOWN> seconds (REQ-016, REQ-025).
- **Health Check:** The health endpoint returns HTTP 200 when the database is reachable and HTTP 503 when it is not (REQ-025).
- **Documentation Currency:** OpenAPI specification and the Requirements Traceability Matrix match the delivered endpoints and requirements (REQ-026).
- **Stakeholder Demo:** Completed feature increments are demonstrated at the Milestone Review, with the Sponsor or delegate present for milestone sign-off.
- **Retrospective:** A retrospective is held at the end of each milestone, and the DoD is reviewed (Section 5).

## 4. Release Level
Applies to each release that delivers a milestone to production-readiness (Release 3 / Milestone 4 is the production release gate).

- **Performance Benchmarking:** Load tests on a database seeded with 100,000 records confirm p95 latency below 300 ms for single-resource operations (REQ-023) and below 1 s for list queries (REQ-013).
- **Concurrency Validation:** Concurrent update tests confirm that stale ETags return HTTP 409 Conflict (REQ-010).
- **Localization Validation:** Error messages are verified in EN, ES, and CA, with EN fallback for unsupported locales (REQ-014, REQ-030). Because REQ-014 is Could Have, the Sponsor must confirm whether this is a release blocker.
- **Audit & Security Scan:** Security scan and dependency audit report zero high or critical severity findings. The formal vulnerability threshold beyond high and critical is <UNKNOWN>.
- **Transport Security:** Verification confirms that TLS is enforced for all API traffic (REQ-029).
- **Audit Retention:** Retention configuration for audit entries is verified against the approved period (REQ-028), which is <UNKNOWN> until confirmed.
- **Deployment Readiness:** Production deployment scripts, database migration rollback procedures (REQ-027), and docker-compose orchestration are verified in a staging environment.
- **Operational Handover:** Runbook covering deployment, health check, rollback, and audit log access is delivered and reviewed by the operations owner.
- **Open Items Closed:** All open gaps in the Requirements Specification v2.0 that affect the release scope are resolved or formally accepted by the Sponsor.
- **Executive Sign-off:** Formal approval is granted by the Project Sponsor for milestone and release sign-off, as stated in the Project Charter Section 10.

## 5. Review and Evolution of the Definition of Done
- **Retrospective Review:** The DoD is reviewed at each post-milestone retrospective by the engineering team and the Project Manager.
- **Change Control:** Changes to quality gates or sign-off criteria are proposed through a lightweight ADR and approved by consensus of the engineering team. Changes that alter the sponsor's sign-off authority require Sponsor approval.
- **Versioning:** Each change is recorded in the Changelog with a version bump per the rules applied to this document.
- **Continuous Improvement:** Thresholds marked <UNKNOWN> are confirmed and recorded in the next version.

## 6. Open Items
- Vulnerability severity threshold beyond "high or critical": <UNKNOWN>.
- Container health check startup window: <UNKNOWN>.
- Audit retention period: <UNKNOWN>.
- Tooling confirmation (GitHub, Jira, CI platform, linters, security scanners): pending team confirmation.
- Release gating status of Could Have items (REQ-014, REQ-030): pending Sponsor decision.
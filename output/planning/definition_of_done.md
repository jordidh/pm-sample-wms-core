# Definition of Done: Warehouse Inventory & Storage Management REST API (WMS Core)

| Field | Value |
| --- | --- |
| Document | Definition of Done |
| Project Name | Warehouse Inventory & Storage Management REST API (WMS Core) |
| Current Version | 1.0 |
| Date | 2026-10-20 |

## Changelog

| Version | Date | Change Description |
| --- | --- | --- |
| 1.0 | 2026-10-20 | Initial version |

## 0. Purpose and Basis

This Definition of Done (DoD) defines when a deliverable is complete and fit for purpose at four levels: story/work package, feature, iteration/increment, and release. It applies the Team Charter Section 8.2 criteria and the Requirements Specification v1.0 (REQ-001 to REQ-038, NFR-*).

**Assumptions:**
* Team Charter Section 8.2 is the baseline DoD. This document extends it with levels, measurable thresholds, and sign-off roles.
* Team composition and role names are <UNKNOWN>; roles are referenced as in the WBS Dictionary (Backend Developer, QA Engineer, DevOps Engineer, Security Analyst, Performance Engineer, Localization Reviewer, Project Manager, Project Sponsor, Business Owner, Technical Lead).
* The Security & Compliance Owner is named in the Team Charter but has no name (<UNKNOWN>).
* Tooling (CI product, coverage tool, test framework) is not specified and is assumed to be selected in WBS 2.4.4.

**Terms:**
* **Story:** A user story or WBS work package with a requirement ID link.
* **Feature:** A capability that satisfies one or more requirement groups (for example, Location Engine, Container Engine, Stock Operations, Audit Trail).
* **Increment:** The integrated, deployable result of an iteration.
* **Release:** The set of milestones M1–M4 plus production go-live (WBS 6.1.x).

---

## 1. Story / Work Package Level DoD

A story or work package is Done when every item below is true.

### 1.1 Code Quality
* [ ] Code is implemented for the scope of the linked requirement IDs only; no unlinked functionality is added.
* [ ] Code is peer reviewed (at least one reviewer) and merged via pull request.
* [ ] Lint and static analysis pass with zero new blocking findings.
* [ ] No secrets, stack traces, database schema details, or library versions appear in responses, logs visible externally, or commits (NFR-S-03, NFR-S-06).
* [ ] Trunk-based branch is short-lived and merged; no long-running feature branches are left open.

### 1.2 Testing
* [ ] Automated tests exist for every acceptance criterion of the linked requirement (Given/When/Then cases in the Requirements Specification).
* [ ] Negative cases are tested: invalid input, unauthorized role, not-found, and rule violation (for example `STOCK_INSUFFICIENT_QUANTITY`, `LOCATION_CAPACITY_EXCEEDED`).
* [ ] For any stock operation (split, merge, group, ungroup, move, adjust, write-off): concurrency and transactional rollback tests exist (REQ-019, REQ-021, REQ-022, NFR-R-03).
* [ ] For any stock mutation: the movement log entry is asserted (REQ-020, NFR-R-02).
* [ ] For any endpoint: RBAC allow and deny paths are tested for each of `admin`, `supervisor`, `operator` (REQ-023, NFR-S-02).
* [ ] For any error path: response is RFC 7807 `application/problem+json` with a catalog `code` (REQ-024, REQ-029).
* [ ] All tests pass in CI; no test is skipped without a linked defect and an expiry date.
* [ ] Line coverage of changed code is at or above 80%, and the overall suite remains at or above 80% (REQ-027, NFR-M-01).

### 1.3 Data and Persistence
* [ ] Schema or migration change is versioned, applies from empty and from the previous version, and is tested in CI (NFR-R-04).
* [ ] Optimistic locking (ETag or version) is enforced on mutations and tested with a stale-ETag case (REQ-022).
* [ ] Zero negative-inventory, over-capacity, or orphaned-container records are produced by the story's tests (NFR-R-01).

### 1.4 Documentation
* [ ] OpenAPI 3 specification updated for the changed endpoints and error responses (REQ-028, NFR-U-02).
* [ ] Error code catalog updated if a new code is added; Technical Lead approval and changelog entry recorded (REQ-029).
* [ ] Changelog of the affected document updated with version and date.
* [ ] Decision log entry recorded for any business rule interpretation (NFR-M-04).

### 1.5 Localization (where user-facing messages change)
* [ ] EN, ES, and CA message keys added for each new or changed error or user-facing message (REQ-032 to REQ-036).
* [ ] Every new code has the same `code` value in all three languages (REQ-037).

### 1.6 Observability
* [ ] Structured JSON log entries include `trace_id` for the new code path (REQ-025, NFR-M-03).
* [ ] Health check behaviour is unchanged or updated and tested for the affected component (NFR-D-02).

### 1.7 Acceptance
* [ ] Story demonstrated to the Product Owner/Business Owner in the iteration review, or recorded as not demonstrable with reason.
* [ ] Acceptance criteria verified and marked Accepted by the Business Owner (for business rules) or QA Engineer (for technical criteria).

---

## 2. Feature Level DoD

A feature (for example, Location Engine, Container Engine, Stock Allocation, Stock Operations, Audit Trail, Security and RBAC, Error Contract and Localization, Performance) is Done when all story-level DoD items are met for all its stories and the following are true.

### 2.1 Requirement Coverage
* [ ] Every requirement in the feature's group is implemented and has acceptance test evidence (for example, Location group REQ-001 to REQ-005; Container group REQ-006 to REQ-010; Inventory core REQ-011 to REQ-019; Audit REQ-020).
* [ ] Traceability matrix (Requirements Specification Section 3) is updated: each requirement maps to its WBS package(s) and user story IDs, and no entry remains `<UNKNOWN>` for the feature.

### 2.2 Verification Evidence
* [ ] Feature-level integration tests pass against a database with migrations applied.
* [ ] Feature-level negative and concurrency tests pass (for stock operations, split, merge, group, and ungroup are tested together, including concurrent execution).
* [ ] Movement log completeness for the feature is verified at 100% of mutations (NFR-R-02).
* [ ] Zero negative-inventory, over-capacity, or orphaned-container records in the feature's acceptance dataset (NFR-R-01).

### 2.3 Security Review
* [ ] Security-relevant changes in the feature (auth, RBAC, error masking, audit log) are reviewed by the Security & Compliance Owner, and findings are closed or formally accepted (TC 3.3, NFR-S-04).

### 2.4 Operational Readiness
* [ ] Feature is deployable from the CI pipeline to a test environment; deployment is verified by health checks.
* [ ] Structured logs and `trace_id` propagation are verified for the feature's endpoints.

### 2.5 Feature Demonstration
* [ ] Feature demonstrated at the iteration review with Business Owner and Warehouse Supervisors present (TC 4).

---

## 3. Iteration / Increment Level DoD

An iteration (two-week time box, assumed per Project Charter Section 8) is Done when all of the following are true.

### 3.1 Iteration Goal and Backlog
* [ ] Iteration goal is met or the shortfall is recorded with reason and re-planned.
* [ ] All committed stories meet the story-level DoD, or are moved back to the backlog with a documented reason.
* [ ] Every item in the iteration met the Definition of Ready (TC 8.1) at the start of the iteration.

### 3.2 Build and Pipeline
* [ ] The main branch builds, and the CI pipeline is green (build, lint, unit test, integration test, migration test, coverage gate at 80%) (WBS 2.4.4, 2.4.5).
* [ ] Increment is packaged as a Docker image and deployed to the test environment (REQ-025).

### 3.3 Quality Gates
* [ ] No open defects of severity Critical or High against the increment's stories.
* [ ] Open defects of severity Medium or Low are logged with owner and target iteration.
* [ ] Regression suite for all previously accepted requirements passes.
* [ ] Concurrency test suite for split, merge, group, and ungroup passes (WBS 4.4.1 at M3).
* [ ] Overall automated coverage ≥ 80%.

### 3.4 Review and Governance
* [ ] Iteration review (demo) held; feedback recorded in the issue tracker.
* [ ] Iteration retrospective held; 1–2 improvement actions recorded and owners assigned (TC 4, 9).
* [ ] RAID log updated (risks, issues, assumptions), with the weekly risk review completed (TC 4).
* [ ] Decision log updated with all decisions made in the iteration, restated from chat within 1 working day (TC 6).

### 3.5 Documentation
* [ ] Changelogs of changed documents are updated.
* [ ] Requirements Specification traceability matrix is current.

---

## 4. Milestone Level DoD (Gates M1–M4)

Each milestone is Done when its deliverables meet the criteria below and the gate review is recorded with a go/no-go decision by the Project Sponsor (TC 5). Milestone gate reviews are 90 minutes with the steering committee and team (TC 4).

### 4.1 M1: Architectural Foundation and CI/CD
* [ ] DB schema, transaction boundaries, isolation levels, and version/ETag columns are baselined; changes after baseline go through change control (TC 3.2, NFR-C-03).
* [ ] Index strategy document is reviewed and covers all list and aggregate query paths (NFR-P-04).
* [ ] JWT/OAuth2 validation is integrated with the named identity provider (A2, OI-07), and RBAC matrix is baselined (OI-01, REQ-023).
* [ ] RFC 7807 handler and error code catalog are in place; leak-proof 5xx with `trace_id` verified (REQ-024, REQ-029, REQ-030, REQ-031).
* [ ] OpenAPI 3 skeleton and Swagger UI are published (REQ-028).
* [ ] Structured JSON logging and health checks (liveness, readiness) are operational (REQ-025, NFR-D-02).
* [ ] Docker image and local compose environment build and run.
* [ ] CI pipeline is operational with build, lint, unit test, integration test, migration test, and 80% coverage gate (REQ-027, NFR-R-04).
* [ ] Foundation security review completed with findings closed or formally accepted (NFR-S-05, WBS 2.4.6).
* [ ] M1 gate decision recorded (WBS 2.4.7).

### 4.2 M2: Location and Container Hierarchy Engine
* [ ] REQ-001 to REQ-010 are implemented and accepted.
* [ ] Over-capacity and orphaned-container acceptance tests pass with zero violating records (WBS 3.3.1, 3.3.2; NFR-R-01).
* [ ] Location and container endpoints are documented in OpenAPI and error codes are in the catalog.
* [ ] Weight and dimension limits are confirmed by the Business Owner (OI-03).
* [ ] M2 gate decision recorded (WBS 3.3.3).

### 4.3 M3: Stock Management and Advanced Movements
* [ ] REQ-011 to REQ-020 are implemented and accepted.
* [ ] Concurrency tests for split, merge, and group pass with 100% of runs meeting invariants (WBS 4.4.1; NFR-R-03).
* [ ] Movement log completeness test: 100% of mutations logged, and log immutability verified for all roles (WBS 4.4.2; NFR-R-02, NFR-C-02).
* [ ] Functional acceptance tests for REQ-001 to REQ-020 pass (WBS 4.4.3).
* [ ] Reason code and UoM catalogs are confirmed by the Business Owner (OI-02, OI-04).
* [ ] M3 gate decision recorded (WBS 4.4.4).

### 4.4 M4: Performance, Localization and Production Readiness
* [ ] Performance tests at 100k records meet: single-item reads p95 < 300 ms; list and aggregate < 1 s (REQ-026, NFR-P-01, NFR-P-02); performance report published with load profile (NFR-P-03, WBS 5.1.5).
* [ ] EN, ES, and CA message bundles are complete for every catalog code (REQ-032 to REQ-036; WBS 5.2.1, 5.2.3).
* [ ] Professional review of ES and CA is signed off before gate approval (REQ-038, NFR-C-04).
* [ ] RFC 7807 verification is executed across the full error code catalog (REQ-024; WBS 5.3.3).
* [ ] Coverage is at or above 80% across the suite (REQ-027; WBS 5.3.1).
* [ ] M4 security review completed with findings closed or formally accepted (NFR-S-05; WBS 5.3.2).
* [ ] Docker image and Kubernetes or cloud manifests are finalized; health checks validated in the target environment (REQ-025, NFR-D-01; WBS 5.4.1, 5.4.2).
* [ ] Final OpenAPI 3 specification published in Swagger UI (REQ-028; WBS 5.4.3).
* [ ] Release approval gate decision recorded by the Project Sponsor (WBS 5.4.4).

---

## 5. Release Level DoD

The release is Done (production go-live accepted) when all of the following are true.

### 5.1 Requirement Acceptance
* [ ] All in-scope requirements REQ-001 to REQ-038 are implemented and accepted (Project Charter Success Criterion 1).
* [ ] All NFRs in the Requirements Specification are verified by their stated verification method, with evidence attached.
* [ ] Zero negative-inventory, over-capacity, or orphaned-container records in acceptance testing (Project Charter Success Criterion 2; NFR-R-01).
* [ ] Every stock mutation produces an immutable movement log entry, verified (Success Criterion 3; NFR-R-02).
* [ ] Performance targets met at 100k records (Success Criterion 4).
* [ ] Automated CI coverage ≥ 80% (Success Criterion 5).
* [ ] OpenAPI 3 specification and Swagger UI published; RFC 7807 errors verified across the error catalog (Success Criterion 6).
* [ ] Docker image delivered and passing health checks (Success Criterion 7).
* [ ] EN, ES, and CA message bundles validated (Success Criterion 8).

### 5.2 Security and Compliance
* [ ] Security reviews at M1 and M4 completed before gate approval (TC 8.3).
* [ ] RBAC verified for `admin`, `supervisor`, and `operator` on all endpoints (NFR-S-02).
* [ ] Secrets scan shows zero secrets in repository (NFR-S-06).
* [ ] Audit log immutability verified for all roles (NFR-C-02).

### 5.3 Operations Readiness
* [ ] Production environment provisioned (WBS 6.1.1); production deployment and smoke tests passed (WBS 6.1.2).
* [ ] Monitoring and alerting configured (WBS 6.1.3).
* [ ] Runbook and operations handover completed (WBS 6.1.4).
* [ ] Health checks verified in production (NFR-D-02).

### 5.4 Documentation and Handover
* [ ] Final OpenAPI specification, error code catalog with changelog, and decision log are complete.
* [ ] Requirements Specification traceability matrix shows no `<UNKNOWN>` story IDs or open verification gaps for release scope.
* [ ] Open items OI-01 to OI-08 are closed or formally accepted with risk recorded.

### 5.5 Acceptance Sign-off
| Role | Name | Criterion | Signature | Date |
| --- | --- | --- | --- | --- |
| Project Sponsor | <UNKNOWN> | Go-live acceptance (WBS 6.1.5) | | |
| Business Owner | <UNKNOWN> | Business rules accepted (REQ-001 to REQ-020) | | |
| Technical Lead | <UNKNOWN> | Contract and error catalog baselined | | |
| Security & Compliance Owner | <UNKNOWN> | Security reviews M1 and M4 closed | | |
| QA / Test Lead | <UNKNOWN> | Verification evidence complete | | |
| Project Manager | <UNKNOWN> | Release scope and risks recorded | | |

---

## 6. Exit Criteria Summary

| Level | Mandatory Exit Evidence | Sign-off |
| --- | --- | --- |
| Story / WP | Tests green, peer review, OpenAPI and changelog updated, movement log asserted | Peer reviewer, QA Engineer |
| Feature | Requirement coverage 100% for group, security review closed, concurrency tests passed | Technical Lead, Security & Compliance Owner |
| Iteration | Pipeline green, demo held, no open Critical/High defects, coverage ≥ 80% | Business Owner, Project Manager |
| Milestone (M1–M4) | Gate criteria in Section 4 met | Project Sponsor (go/no-go) |
| Release | Section 5 criteria met, acceptance sign-off | Project Sponsor, all roles in Section 5.5 |

---

## 7. Governance of the Definition of Done

* **Owner:** Technical Lead, with QA / Test Lead as co-owner (proposed; Team Charter Section 10 names the Project Manager and Technical Lead as charter owners).
* **Review cadence:** At each milestone gate (M1–M4), and at least once per quarter (assumed, aligned with Team Charter Section 10).
* **Triggers for ad hoc review:** Repeated defects escaping to later stages; a failed gate; a change to requirements or NFR thresholds; a new team member; a change to tooling or delivery approach.
* **Change process:** Proposed change is posted to the issue tracker with rationale and affected requirement IDs. Technical Lead approves changes to the DoD within the baselined contract. Changes that weaken a threshold (for example coverage below 80%, performance targets, or security review requirements) require Project Sponsor approval and a formal change request, because they alter the success criteria in the Project Charter.
* **Versioning:** Minor version for clarifications and added checks; major version when a threshold or a success criterion changes. Each change is recorded in the changelog with version, date, and description.
* **Metrics tracked each iteration:** Percentage of stories meeting DoD on first demo; defects escaping to later iterations; coverage trend; movement log completeness; RBAC test coverage. Metrics are reviewed at the iteration retrospective.
* **Retrospective input:** DoD items that were skipped, or that caused rework, are raised at the retrospective and either clarified (minor) or escalated (major).

## 8. Open Items

| # | Item | Owner |
| --- | --- | --- |
| DOD-OI-01 | Confirm tooling for coverage measurement and CI gate (WBS 2.4.4) | DevOps Engineer |
| DOD-OI-02 | Name Security & Compliance Owner and sponsor (Charter Section 11) | Project Sponsor |
| DOD-OI-03 | Confirm iteration cadence (two weeks, assumed) | Project Manager |
| DOD-OI-04 | Confirm whether the feature-level security review is required for every feature or only for security-relevant features | Technical Lead |
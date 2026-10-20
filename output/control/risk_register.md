# Risk Register: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 1.0
- **Date:** 2026-10-20

### Changelog
| Version | Date | Change Description |
|---|---|---|
| 1.0 | 2026-10-20 | Initial baseline risk register assessment. |

---

## 1. Complexity Drivers
- **Technical Complexity:** High concurrency data integrity challenges, multi-tier hierarchical tree validation without cyclic references, optimistic locking (ETag), and strict sub-second performance thresholds (p95 < 300ms single items, < 1s for 100k+ records lists).
- **Organizational Complexity:** Strict adherence to REST semantics, RFC 7807 error standards, and robust role-based access control (RBAC).
- **Vendor / External Complexity:** Zero hardware driver or physical scanner dependencies (pure backend REST API), relying strictly on external SKU master data references.
- **Regulatory & Compliance Complexity:** 100% immutable audit logging requirements for all stock movements, creations, splits, merges, and write-offs.
- **People / Resource Complexity:** Compressed 40-day critical path execution requiring highly specialized backend engineers and database architects.

## 2. Risk Evaluation Scales & Thresholds

### Probability (1-5)
1. **Rare (1):** Unlikely to occur under normal operating conditions (< 10%).
2. **Unlikely (2):** May occur under specific, exceptional circumstances (10% - 30%).
3. **Possible (3):** Might occur at some point during project execution (31% - 50%).
4. **Likely (4):** Will probably occur in most standard scenarios (51% - 80%).
5. **Almost Certain (5):** Expected to occur frequently or continuously (> 80%).

### Impact (1-5)
1. **Negligible (1):** Minimal disruption; easily absorbed without schedule/cost variance.
2. **Minor (2):** Small impact on non-critical path tasks or minor rework required.
3. **Moderate (3):** Noticeable impact on project schedule or budget; requires formal contingency.
4. **Major (4):** Significant impact on critical path or core deliverables; major schedule or cost variance.
5. **Critical (5):** Catastrophic failure of project objectives, security breach, or total delivery halt.

### Score Thresholds (Probability x Impact)
- **Low (1 - 4):** Monitor and accept.
- **Medium (5 - 9):** Mitigate with standard task-level contingency.
- **High (10 - 15):** Active management, mandatory mitigation owner, and contingency reserve allocation.
- **Critical (16 - 25):** Immediate executive escalation, active mitigation, and dedicated contingency budget/schedule reserves.

---

## 3. Risk Register Table

| ID | Category | Threat / Opp | Risk Statement (Cause - Event - Effect) | Affected WBS / Backlog | Prob | Impact | Score | Ranking | Response Strategy | Mitigation / Response Steps | Trigger / Early Warning Sign | Risk Owner | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| RSK-01 | Technical | Threat | Due to high-volume concurrent database operations, race conditions may occur during massive concurrent stock movements, resulting in negative inventory or orphaned container states. | 1.3.1, 1.3.2, 1.3.3 | 4 | 5 | 20 | 1 | Mitigate | Enforce database-level ACID transactions, row-level locking, and strict optimistic locking validation headers (ETag/version checks). | Increased conflict response rates in staging load tests. | Backend Engineer | Open |
| RSK-02 | Technical | Threat | Due to deep multi-tier location hierarchies and recursive tree traversal queries, list and aggregate queries over 100k records may fail to meet the p95 < 1s latency target. | 1.2.1, 1.2.2, 1.4.1, US-202 | 4 | 4 | 16 | 2 | Mitigate | Implement materialized path indexes, closure tables, and optimized database indexing strategies for tree structures. | Slow query log entries exceeding 500ms during preliminary data seeding. | Lead Architect | Open |
| RSK-03 | Technical | Threat | Due to complex hierarchical container nesting rules and weight constraint validations, cyclic references or capacity overages might be inadvertently permitted. | 1.2.1, 1.2.2, 1.2.3 | 3 | 4 | 12 | 3 | Mitigate | Implement automated graph cycle detection algorithms within the non-cyclic relocation validator service. | Edge case test failures in recursive hierarchy validation scripts. | Backend Engineer | Open |
| RSK-04 | Schedule | Threat | Due to tight sequential dependencies on the critical path (1.1.1 through 1.4.4), any upstream delays in core database schema design or optimistic locking will cascade and push final delivery past the 40-day schedule. | 1.1.1, 1.1.2, 1.4.3, 1.4.4 | 3 | 3 | 9 | 4 | Mitigate | Maintain rigorous daily standups, enforce strict Definition of Done, and utilize non-critical float activities (1.1.4, 1.1.5, 1.4.2) as resource buffers. | Milestone 1 sign-off delay exceeding 2 days. | Project Manager | Open |
| RSK-05 | External | Threat | Due to reliance on external SKU master data references, changes or unavailability of external SKU schemas could disrupt stock tracking and validation engines. | 1.3.1, 1.3.2 | 2 | 3 | 6 | 5 | Accept | Establish clear contract testing and strict data validation boundaries for all incoming SKU string references. | Inconsistent data format errors during integration trials. | Technical Lead | Open |
| RSK-06 | Technical | Opp | By implementing standardized REST semantics, OpenAPI 3 specs, and RFC 7807 problem+json error handling, developer onboarding and client SDK generation speed can be dramatically enhanced. | 1.1.4, 1.1.5, US-101, US-102 | 4 | 3 | 12 | 6 | Exploit | Provide automated swagger UI endpoints and complete RFC 7807 error libraries during Milestone 1. | High developer adoption rates and positive feedback during initial API consumption tests. | Technical Writer | Open |

---

## 4. Top Risks & Contingency Plans

1. **RSK-01: High Concurrency Race Conditions & Negative Inventory (Score: 20)**
   - *Contingency Plan:* If database-level ACID transactions and optimistic locking fail to eliminate race conditions under peak load, halt non-critical write operations, switch database transaction isolation levels to Serializable, and deploy hotfixes with dedicated database locking locks within a 2-day contingency window.

2. **RSK-02: Hierarchical Query Latency Degradation on 100k+ Records (Score: 16)**
   - *Contingency Plan:* If p95 latency exceeds 1s during performance stress testing (WBS 1.4.1), engage database tuning experts to introduce read replicas, cache hierarchical tree paths in Redis, and apply pagination limits on deep tree queries.

3. **RSK-03: Cyclic References & Nesting Validation Failures (Score: 12)**
   - *Contingency Plan:* If edge-case cyclic references bypass initial validation, immediately disable container nesting relocation endpoints via feature flag and patch the graph traversal algorithm.

---

## 5. Contingency Reserves Sizing

- **Schedule Contingency Reserve:** **5 Days**
  - *Basis of Estimate:* Sized based on the critical path duration (40 days) combined with the cumulative exposure of high-severity concurrency and tree traversal risks (RSK-01 and RSK-02). Represents 12.5% of total project duration, substantiated by historical software engineering benchmarks for complex transactional backend architectures.

- **Cost / Effort Contingency Reserve:** **80 Hours**
  - *Basis of Estimate:* Calculated as 10% of total estimated effort across all WBS work packages (~768 total labor hours), specifically allocated to cover remediation for concurrency locking adjustments, performance query optimizations, and stress-testing bug fixes.

---

## 6. Materialized Issues
*Currently, no issues have materialized as the project is at baseline assessment stage (Version 1.0). Future materialized issues will be logged here and mapped directly to their parent risks in the Risk Register.*
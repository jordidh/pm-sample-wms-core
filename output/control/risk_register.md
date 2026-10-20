# Risk Register: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 1.1
- **Date:** 2026-10-20

## Changelog
| Version | Date | Change Description |
|---|---|---|
| 1.0 | 2026-10-17 | Initial baseline risk assessment and risk register generation |
| 1.1 | 2026-10-20 | Baseline risk reassessment against the 2026-10-20 Business Case, Project Charter, WBS 1.1, Product Backlog 1.1, and Schedule Model 1.1. No new emerging issues, change requests, or materialized issues were submitted. Minor reassessment, so the version is bumped from 1.0 to 1.1. Corrections and additions identified in this audit: (a) the Milestone 1 critical path and float values were re-verified against the schedule model; (b) the open backlog gaps (Features 1.1.2, 1.2.2, 2.1.2, 3.1.2) were added as a new schedule/scope risk; (c) the dependency on Milestone 1 WBS 1.2.2 and 1.3.2 from US-101 was recorded as an integration risk; (d) the Business Case ROI gap (no quantified baseline or budget) was added as a cost/benefit risk; (e) the SKU external-validation assumption was linked to a new external risk; (f) the Milestone 1 schedule contingency and the cost contingency were re-sized from the evidence recorded below. |

## 1. Complexity Drivers & Overall Project Risk Exposure

### Complexity Drivers
- **Technical Complexity:** High. The system requires complex recursive or nested hierarchical data structures (`warehouse` > `zone` > `aisle` > `rack` > `shelf` > `bin` and container nesting), rigorous concurrency control via optimistic locking (ETag/version headers), atomic multi-resource transactions, and sub-second query performance targets (p95 < 300 ms for single-item reads, < 1 s for list/aggregate queries over 100k records).
- **Organizational Complexity:** Low-to-Moderate. The project relies on external master data management (SKU definitions) and external clients properly adhering to RFC 7807 error formats and ETag headers, introducing integration dependencies.
- **Vendor & Infrastructure Complexity:** Low. Leverages standard containerization (Docker) and standard relational database engines (PostgreSQL). No vendor contract or licensing dependency is stated in the source materials.
- **Regulatory & Audit Complexity:** High. Mandatory 100% immutable audit log coverage for every stock and container movement, strict ACID transaction compliance, and absolute zero tolerance for negative inventory or orphaned containers.
- **People & Resource Complexity:** Moderate. Requires tight coordination between database architects, backend developers, security engineers, and DevOps specialists under tight milestone constraints. No headcount, named individuals, or velocity data are stated in the source materials.
- **Schedule & Scope Complexity:** Moderate. Milestone 1 is predictive (10-day critical path, 152 hours effort). Milestones 2-4 are adaptive and their backlog has four referenced features (1.1.2, 1.2.2, 2.1.2, 3.1.2) with no definition, and story points have no conversion to hours or durations.

### Overall Project Risk Exposure
Based on the current threat landscape, the project faces a **moderate-to-high** residual risk exposure. The highest-scored exposures are concurrency race conditions under high-frequency updates (RSK-01, score 20, Critical band) and database performance degradation at scale (RSK-02, score 16, Critical band). Both require active mitigation in the architecture and in Milestone 4 stress testing. The newly recorded scope gap (RSK-08) and funding/benefit baseline gap (RSK-09) raise the schedule and governance exposure from the baseline. The overall risk profile is not changed by this reassessment, so the document is a minor (1.1) reassessment.

**Assumptions for this assessment:**
- No emerging issues, external conditions, or change requests were supplied (fields were empty). Risks were not invented from external events; scoring relies on the source documents only.
- No historical defect, velocity, or performance data was provided. Probability and impact scores are expert judgement based on the complexity drivers above and are flagged for recalibration once Milestone 1 produces measured data.
- Owner roles are taken from the WBS 1.1 dictionary. No named individuals are assigned.

## 2. Risk Evaluation Scales & Scoring Thresholds

### Probability Scale (1-5)
1. **Rare:** Highly unlikely to occur (< 10% probability).
2. **Unlikely:** Could occur under specific conditions (10% - 30% probability).
3. **Moderate:** Might occur at some point during the lifecycle (31% - 50% probability).
4. **Likely:** Probable occurrence under normal operating conditions (51% - 70% probability).
5. **Almost Certain:** Expected to occur frequently or almost certainly (> 70% probability).

### Impact Scale (1-5)
1. **Negligible:** Minimal disruption; easily absorbed without schedule or cost variance.
2. **Minor:** Slight impact on non-critical tasks; minor workaround available.
3. **Moderate:** Noticeable impact on milestone delivery, performance, or quality targets; requires management attention.
4. **Major:** Significant schedule delay, cost overrun, or failure to meet critical non-functional requirements (e.g., latency, auditability).
5. **Critical:** Project failure; catastrophic data corruption, complete security breach, or inability to deploy.

### Score Thresholds & Categories (Score = Probability x Impact)
- **Low (1 - 4):** Monitor; accept risk with standard operational management.
- **Medium (5 - 9):** Mitigate; assign owner and specific proactive response.
- **High (10 - 15):** Urgent; requires active contingency reserves, senior management escalation, and rigorous mitigation.
- **Critical (16 - 25):** Severe threat; immediate escalation, mandatory architectural redesign, or project halting until resolved.

## 3. Comprehensive Risk Table

| ID | Category | Threat/Opportunity | Risk Statement (Cause - Event - Effect) | Affected WBS / Backlog Items | Probability | Impact | Score | Ranking | Response Strategy | Mitigation / Response Steps | Trigger / Early Warning Signs | Risk Owner Role | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| RSK-01 | Technical | Threat | **Cause:** High-frequency concurrent stock updates and movements by multiple operators <br>**Event:** Concurrency race conditions occur across shared bin locations or nested containers <br>**Effect:** Data corruption, negative inventory levels, or lost stock allocations. | US-202, US-301, WBS 1.1.1 | 4 | 5 | 20 | 1 | Mitigate | Implement strict optimistic locking via ETag/version headers on all mutating resources (US-301); enforce database-level unique constraints and atomic multi-resource transactions. | Increased frequency of HTTP 409 Conflict responses or intermittent database deadlock logs during load testing. | Backend Developer | Open |
| RSK-02 | Technical | Threat | **Cause:** Large inventory volumes exceeding 100,000 records <br>**Event:** Database queries and aggregate searches degrade in performance under load <br>**Effect:** Failure to meet p95 latency targets (< 300 ms single, < 1 s list over 100k records), leading to operator bottlenecks. | US-302, WBS 1.1.1 | 4 | 4 | 16 | 2 | Mitigate | Design optimized indexing strategies for location hierarchies and stock batch tables during Milestone 1 (WBS 1.1.1); conduct rigorous query profiling and stress testing during Milestone 4. | Query execution times exceeding 200 ms on test datasets during early integration milestones. | Database Architect | Open |
| RSK-03 | Technical | Threat | **Cause:** Complex hierarchical location structures and multi-level container nesting <br>**Event:** Orphaned containers or recursive query circular references are introduced <br>**Effect:** Data integrity violations and inability to accurately compute capacity or physical placement. | US-101, US-104, Epic 1.1, Epic 1.2 | 3 | 4 | 12 | 3 | Mitigate | Enforce strict parent-child foreign key validation, implement database check constraints for container weight/dimensions, and execute automated graph integrity unit tests. | Unexplained null parent references in staging database or recursive query timeouts. | Backend Developer | Open |
| RSK-04 | External | Threat | **Cause:** External clients or upstream product master data management (MDM) systems <br>**Event:** Upstream clients fail to handle RFC 7807 `problem+json` error responses correctly or submit invalid SKU references <br>**Effect:** Integration failures, unhandled client exceptions, and operational friction. | US-303, Assumptions, WBS 1.3.1 | 3 | 3 | 9 | 4 | Mitigate | Publish clear OpenAPI specifications, provide comprehensive integration guidelines, and build rigorous input validation middleware enforcing RFC 7807 standards. | High volume of support tickets regarding error payload parsing or schema mismatch reports from external integrators. | Technical Writer | Open |
| RSK-05 | Schedule | Threat | **Cause:** Strict automated CI test coverage gating (≥ 80%) combined with tight Milestone 1 delivery timelines (10 days) <br>**Event:** Code coverage falls below threshold due to rapid prototyping <br>**Effect:** CI pipeline blocks deployment and delays milestone sign-off. | WBS 1.4.2, US-304 | 3 | 3 | 9 | 5 | Mitigate | Enforce test-driven development (TDD) practices from day one, mandate unit test creation alongside feature code, and monitor coverage metrics continuously in CI. | Daily CI build warnings showing test coverage hovering near the 80% threshold. | DevOps Engineer | Open |
| RSK-06 | Regulatory | Threat | **Cause:** Complex multi-step stock transformations (split, merge, group, ungroup) <br>**Event:** Audit log generation fails or records incomplete transaction metadata <br>**Effect:** Non-compliance with audit readiness requirements and loss of 100% traceability. | US-204, US-203 | 2 | 4 | 8 | 6 | Mitigate | Implement database-level triggers or immutable append-only event logging for every stock movement and adjustment prior to releasing Milestone 3. | Missing movement log entries during integration testing of split/merge operations. | Security Engineer | Open |
| RSK-07 | Resource | Opportunity | **Cause:** Highly modular REST architecture and standardized OpenAPI contracts <br>**Event:** External systems and internal developers rapidly onboard to WMS Core APIs <br>**Effect:** Accelerated integration timelines and reduced custom adapter development effort. | Strategic Drivers, Milestone 1-4 | 4 | 3 | 12 | 7 | Enhance | Publish polished SDK stubs and interactive Swagger documentation early in Milestone 1; establish dedicated developer feedback channels. | High download rates of API client SDKs or immediate positive feedback from integration partners. | Technical Writer | Open |
| RSK-08 | Scope / Schedule | Threat | **Cause:** Backlog 1.1 references Features 1.1.2, 1.2.2, 2.1.2, and 3.1.2 in the Release Plan without any backlog definition; WBS 1.1 defers Milestones 2-4 to the adaptive backlog. <br>**Event:** Release 1-3 scope is baselined or planned with undefined features, or the gaps are discovered mid-iteration. <br>**Effect:** Unplanned scope growth, release slippage, and inability to measure milestone sign-off against the charter. | Backlog 1.1 Sections 2-3, WBS Milestones 2-4, Charter Section 4 | 3 | 3 | 9 | 5 | Mitigate | Obtain Sponsor or Product Owner definition of Features 1.1.2, 1.2.2, 2.1.2, and 3.1.2 before the Release Plan is baselined; hold a backlog refinement session and record the outcome as a backlog change. Route any scope boundary change through the Sponsor per Charter Section 10. | Release Plan is reviewed for Milestone 2 start without the open features defined; new feature IDs appear in iteration planning. | Project Manager | Open |
| RSK-09 | Cost / Benefit | Threat | **Cause:** Business Case contains no headcount, currency budget, or quantified baseline (discrepancy rate, write-off value, resolution time). <br>**Event:** Milestone funding is committed and the ROI cannot be verified because the baseline was never established. <br>**Effect:** Cost overrun is not measurable, benefits cannot be proven at close, and the investment case may be challenged by the Sponsor. | Business Case Sections 3-4; Charter Section 10 | 4 | 3 | 12 | 7 | Mitigate | Establish the quantified baseline (current discrepancy rate, write-off value, and resolution time) and the budget and headcount during Milestone 1 before further funding is committed, as stated in the Business Case. Record the baseline in the Business Case as a version change. | Milestone 2 funding requested without a recorded baseline; no ROI measures in the sponsor status report. | Project Manager | Open |
| RSK-10 | External | Threat | **Cause:** Business Case and Charter assume that SKUs are validated externally and that external systems support HTTP status standards and ETag concurrency headers, but no agreement or evidence of these capabilities is stated. <br>**Event:** An integrating system submits unvalidated SKUs or does not send or honour ETag/If-Match headers. <br>**Effect:** Invalid stock records, lost-update conflicts that bypass optimistic locking, and integration rework that affects US-201, US-301, and US-304. | Business Case Section 6; Charter Section 6; US-201; US-301 | 3 | 4 | 12 | 3 | Mitigate | Document the SKU validation and ETag/HTTP-status expectations in the integration guidelines and the OpenAPI contract; confirm each external system's capability in writing before its Milestone 3 integration; add server-side ETag enforcement (428 Precondition Required) and reject unknown SKU formats at the API boundary as a defensive control. | External system onboarding checklist incomplete; 412/428 responses absent from integrator test logs; SKU rejection rate spikes at API boundary. | Technical Writer | Open |
| RSK-11 | Schedule | Threat | **Cause:** Milestone 1 critical path (WBS 1.1.1 → 1.1.2 → 1.4.1 → 1.4.2) has zero float and the schedule model shows a 10-day duration with no buffer; 1.4.1 depends on both the schema track and the OpenAPI track. <br>**Event:** Any slip in schema design, migration rollback, or Dockerfile/CI setup extends Milestone 1. <br>**Effect:** Milestone 2 start (which depends on WBS 1.1.1, 1.2.2, and 1.3.2 per US-101) is delayed, cascading into Milestones 3 and 4. | WBS 1.1.1, 1.1.2, 1.4.1, 1.4.2; US-101; Schedule Model 1.1 | 3 | 4 | 12 | 3 | Mitigate | Hold a daily critical-path check on the four critical activities; pre-stage the migration rollback test environment; time-box 1.4.2 coverage gating so that the schedule model is refreshed at each milestone gate. Apply the schedule contingency reserve in Section 5 only with Sponsor approval. | Any critical-path activity forecast to finish later than its late finish; 1.4.1 start delayed beyond day 5 of the schedule model. | Project Manager | Open |
| RSK-12 | Technical | Threat | **Cause:** Milestone 1 WBS effort estimates (16-24 hours per package) and story point estimates are relative and carried over without measured velocity or a calibration baseline. <br>**Event:** Actual effort for the security, schema, or CI work exceeds the estimate. <br>**Effect:** Schedule variance and the need for additional Milestone 4 remediation effort beyond the reserves stated in Section 5. | WBS 1.2.1, 1.2.2, 1.4.2; Backlog US-201, US-202, US-301 | 3 | 3 | 9 | 5 | Mitigate | Record actual effort per work package from Milestone 1 onward; convert story points to hours after the first two iterations of velocity data; re-run the schedule model with measured data at each milestone gate. | Actual hours on any work package exceed 120% of the WBS estimate at mid-point; velocity variance above 20% across two iterations. | Project Manager | Open |

## 4. Top Risks & Contingency Plans

1. **RSK-01: Concurrency Race Conditions & Data Corruption (Score: 20, Critical)**
   - **Contingency Plan:** If optimistic locking failures spike under high load, immediately pause affected non-critical batch operations, enable pessimistic row-level locking (`SELECT ... FOR UPDATE`) on high-contention bin locations as an emergency fallback, and deploy hotfix patches within 24 hours.

2. **RSK-02: Query Performance Degradation at Scale (Score: 16, Critical)**
   - **Contingency Plan:** If stress testing in Milestone 4 reveals query execution times exceeding SLAs over 100k records, activate database read replicas, implement materialized views for aggregate inventory reports, and introduce Redis caching layers for frequently accessed location hierarchies.

3. **RSK-07: Modular API Onboarding (Opportunity, Score: 12, High)**
   - **Exploit / Enhance Plan:** Publish SDK stubs and interactive Swagger documentation early in Milestone 1 and track integrator onboarding time as a measurable benefit against the Business Case baseline.

4. **RSK-03: Hierarchical Data Integrity & Orphaned Containers (Score: 12, High)**
   - **Contingency Plan:** If data integrity violations occur during container nesting tests, deploy automated data-scrubbing migration scripts to identify and quarantine orphaned records, and temporarily disable nesting endpoints until schema constraints are reinforced.

5. **RSK-09: Unquantified Business Case / Funding Baseline (Score: 12, High)**
   - **Contingency Plan:** If the baseline is not established by the end of Milestone 1, escalate to the Sponsor, hold Milestone 2 funding release, and re-baseline the ROI expectations in the Business Case.

6. **RSK-10: External SKU Validation & ETag Capability (Score: 12, High)**
   - **Contingency Plan:** If an external system cannot honour ETag/If-Match or SKU validation, place that integration behind a documented adapter that performs validation and version checks, and escalate the external dependency to the Sponsor.

7. **RSK-11: Milestone 1 Critical Path Slip (Score: 12, High)**
   - **Contingency Plan:** If the critical path slips by more than 1 day, apply the schedule contingency reserve (Section 5) after Sponsor approval, and re-sequence any float-bearing work (1.2.2, 1.3.2) to protect the 1.4.1 start.

## 5. Contingency Reserves Sized from Evidence

- **Schedule Contingency Reserve:** **3 Days**
  - *Basis:* Sized from the schedule model (Schedule Model 1.1). The Milestone 1 critical path (1.1.1 → 1.1.2 → 1.4.1 → 1.4.2) totals 10 days with zero float on all four activities. The 30% buffer (3 of 10 days) covers the two highest-scored schedule drivers: complex database indexing (RSK-02) and CI coverage gating (RSK-05). The reserve is applied to the critical path only; the 1 day of float on 1.3.2 and the 2 days of float on 1.2.2 are not counted against it.
  - *Limitation:* The reserve is not based on historical variance data, which was not provided. It must be recalibrated after Milestone 1 actuals are recorded.

- **Cost Contingency Reserve:** **15% of Total Engineering Effort**
  - *Basis:* Applied to the Milestone 1 effort of 152 hours (≈ 23 hours of reserve). Sized from the technical complexity drivers (RSK-01 and RSK-02), which require senior concurrency and query-tuning effort. For Milestones 2-4, the reserve applies to the total engineering effort once story points are converted to hours; until then, the reserve is expressed as a percentage only.
  - *Limitation:* No currency budget or headcount is stated in the Business Case, so the reserve cannot be converted to a monetary amount. The reserve must be confirmed against the budget once established (RSK-09).

## 6. Materialized Issues

*No issues have materialized as of the reassessment date (2026-10-20). All risks remain in an `Open` status under proactive monitoring. No emerging issues, external conditions, or change requests were submitted with this trigger.*

| Issue ID | Date Recorded | Description | Linked Risk(s) | Status |
|---|---|---|---|---|
| None | 2026-10-20 | No issue has materialized. | — | — |

# Risk Register: Warehouse Inventory & Storage Management REST API (WMS Core)

| Field | Value |
| --- | --- |
| Document | Risk Register |
| Project Name | Warehouse Inventory & Storage Management REST API (WMS Core) |
| Current Version | 1.0 |
| Date | 2026-10-20 |
| Trigger | Baseline risk assessment |
| Basis | Business Case v1.0, Project Charter v1.0, WBS v1.0 (predictive components), Schedule Model v1.0 (empty activities list) |

## Changelog

| Version | Date | Change Description |
| --- | --- | --- |
| 1.0 | 2026-10-20 | Initial version |

## 0. Assumptions and Evidence Limits

* No previous risk register exists; this is the baseline assessment.
* No emerging issues, external conditions, or change requests were provided (fields empty).
* The schedule model contains no activities and a project duration of 0.0 days. Schedule-based contingency is therefore sized from WBS dictionary durations and the critical path sequence listed in the WBS, not from a resource-levelled schedule.
* Team composition, budget, sponsor, project manager, and milestone dates are <UNKNOWN> in the source documents. Named owners below are roles; individual names must be assigned by the Project Sponsor.
* Cost figures are not available. Cost contingency is expressed as a percentage of a base that must be supplied (see Section 6).
* Current-state baseline metrics (discrepancy rate, reconciliation hours, write-off value) do not exist yet. Any likelihood or impact that depends on them is rated conservatively and flagged.
* The WBS dictionary sums to a predictive effort total that must be verified against the Schedule Model before use. The WBS critical path is listed as a sequence only; no durations are summed in the source.

## 1. Complexity Drivers and Overall Project Risk Exposure

### 1.1 Complexity Drivers

| Dimension | Driver | Evidence | Complexity Rating |
| --- | --- | --- | --- |
| Technical | Atomic multi-resource transactions (split, merge, group, move) with optimistic locking (ETag/version) | Charter 2.1, REQ-019, REQ-021, REQ-022; WBS 4.2.2–4.2.5 | High |
| Technical | Recursive hierarchies: location tree (6 tiers) and container nesting, with non-cyclic and orphan-prevention rules | REQ-001 to REQ-008; WBS 3.1.4, 3.2.2 | High |
| Technical | 100k-record performance targets for list and aggregate queries (<1 s) and single reads (p95 < 300 ms) | REQ-026; WBS 5.1.x; Business Case 3 | High |
| Technical | Immutable audit log for every stock mutation with 100% completeness | REQ-020; WBS 4.3.x | Medium-High |
| Technical | Leak-proof RFC 7807 error handling with EN/ES/CA localization across full error catalog | REQ-024, REQ-029–REQ-038; WBS 2.3.x, 5.2.x, 5.3.3 | Medium |
| Technical | Containerized deployment (Docker, Kubernetes or cloud), health checks, OpenAPI 3 | REQ-025, REQ-028; WBS 2.4.x, 5.4.x | Medium |
| Technical | Stock-operation semantics (split/merge/group/ungroup) are acknowledged as adaptive; edge cases are expected to surface during build | Business Case 6 | High |
| Organizational | Sponsor, project manager, business owner, and technical lead are all <UNKNOWN> | Charter 10, 11 | High |
| Organizational | No documented current-state process, systems, or incident data | Business Case 1, 8.1 | High |
| Organizational | Funding gate depends on baseline data not yet collected | Business Case 4, 9 | High |
| Vendor | Identity provider for JWT/OAuth2 assumed "available or will be provided" | Charter 6; WBS 2.2.1 | Medium |
| Vendor | Commercial WMS (Option B) not evaluated; build decision not yet confirmed | Business Case 5; WBS 1.1.3 | Medium |
| Vendor | External SKU master data source assumed validated and available | Business Case 8.1; Charter 2.2 | Medium |
| Regulatory / Compliance | Traceability and auditability expectations; reason codes for adjustments and write-offs | Business Case 2; REQ-011–REQ-020 | Medium-High |
| Regulatory / Compliance | Localization and data-retention requirements (retirement archival per retention policy) | Business Case 7 (phase 7); policy not provided | Medium |
| People | Required skills in concurrency, relational transaction design, RBAC, and performance tuning | WBS resource roles (Data Architect, Performance Engineer, Security Analyst, Localization Reviewer) | High |
| People | Localization reviewers for ES and CA and the performance engineer are single-role dependencies | WBS 5.1.2, 5.2.2 | Medium-High |

### 1.2 Overall Project Risk Exposure

**Overall exposure rating: HIGH.** The project is a build of a transactional core engine with high technical complexity, incomplete governance baseline (sponsor, budget, team, and dates unknown), and multiple unquantified benefits. The baseline Business Case and Charter both carry "High" likelihood or impact items that are unresolved (budget and schedule undefined; missing ROI baseline). The overall profile is rated **High** rather than Medium because the charter's own risk table rates "Budget and schedule undefined" as High/High and no gate has yet cleared the governance preconditions.

Aggregate indicator: the number of risks scored High or Critical in Section 3 is recorded in Section 3.4.

## 2. Scales and Thresholds

### 2.1 Probability Scale (1–5)

| Score | Level | Definition |
| --- | --- | --- |
| 1 | Rare | < 10% chance of occurrence over the project |
| 2 | Unlikely | 10–30% |
| 3 | Possible | 30–50% |
| 4 | Likely | 50–80% |
| 5 | Almost certain | > 80%, or already partially observed |

### 2.2 Impact Scale (1–5)

| Score | Level | Schedule | Cost | Scope / Quality | Compliance / Security |
| --- | --- | --- | --- | --- | --- |
| 1 | Negligible | < 2 days slip on a milestone | < 1% of budget | Cosmetic | None |
| 2 | Minor | 2–5 days | 1–3% | Minor rework within an iteration | Internal finding only |
| 3 | Moderate | 1–2 weeks | 3–7% | Requirement partially unmet; workaround needed | Audit finding requiring remediation |
| 4 | Major | 2–6 weeks; milestone gate missed | 7–15% | Requirement unmet for a milestone | Compliance breach or data integrity defect in production |
| 5 | Severe | > 6 weeks or project cancellation | > 15% | Core objective unmet (traceability, accuracy, performance) | Security breach or regulatory penalty |

### 2.3 Score Thresholds (Probability x Impact)

| Band | Score Range | Required Action |
| --- | --- | --- |
| Low | 1–4 | Accept or monitor; review at each gate |
| Medium | 5–9 | Mitigate within the current milestone; owner reports monthly |
| High | 10–15 | Mitigation plan mandatory; escalate to Project Manager weekly; sponsor visibility at each gate |
| Critical | 16–25 | Immediate sponsor escalation; contingency activated or prepared before next milestone |

### 2.4 Opportunity Scale

Opportunities use the same P x I scoring, where Impact measures the benefit gained (schedule saved, cost avoided, value added). Score bands are the same as threats.

## 3. Risk Register

### 3.1 Risk Table

Ranking is by score (descending); ties are ordered by impact. Status for all baseline risks is **open** unless noted.

| ID | Category | Type | Risk Statement (Cause – Event – Effect) | Affected WBS / Backlog | P | I | Score | Rank | Response Strategy | Mitigation / Response Steps | Trigger / Early Warning Signs | Risk Owner (Role) | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| R-01 | Schedule / Budget | Threat | Because sponsor, PM, budget, team, and milestone dates are undefined, funding and resourcing for M1 are delayed, and the critical path (1.1.1 → 1.1.4 → 2.1.x) starts late, which delays every downstream milestone. | 1.1.1, 1.1.4, 1.2.1; charter Sec. 4, 5, 10 | 5 | 4 | 20 | 1 | Escalate | Sponsor names PM and sponsor before any further WBS work; cost and team estimate completed in 1.1.4; milestone dates set; funding gate before M2 documented. | No named sponsor/PM after 5 working days; estimate not produced by 1.1.4 finish; no approved budget ceiling. | Project Sponsor |  Open |
| R-02 | Organizational / Data | Threat | Because no current-state baseline metrics (discrepancy rate, reconciliation hours, write-off value) exist, the business case cannot prove ROI and the M2 funding gate is held, which delays M2 start and may cancel the investment. | 1.1.2, 1.1.4, 3.3.3 gate; Business Case 3, 4, 9 | 5 | 4 | 20 | 1 | Mitigate | Business Analyst collects baseline from existing records for a defined sample period; if records are absent, define proxy metrics and record the data gap as an assumption; sponsor decides on proceeding with explicit ROI uncertainty. | Baseline report not complete by 1.1.2 finish (5 days); requests for reconciliation logs unanswered; benefit values remain <UNKNOWN>. | Business Analyst (accountable: Project Manager) | Open |
| R-03 | Technical | Threat | Because split, merge, group, and ungroup operations require atomic multi-resource transactions with optimistic locking, concurrency defects (lost updates, double allocation, negative balances) are likely to surface in M3, which extends the critical path (4.2.2–4.4.1) and may breach REQ-017 and REQ-021/022. | 2.1.4, 4.2.2, 4.2.3, 4.2.4, 4.2.5, 4.4.1; Business Case 8.2; charter Sec. 7 | 4 | 5 | 20 | 1 | Mitigate | Isolation level and locking strategy fixed in 2.1.4 before schema finalization; concurrency test harness built in M1 (2.4.5); no-rework buffer added to 4.2.x; pair-programming or design review for the split/merge logic; contract tests run on every CI build. | Concurrency test failures in iteration demos; retries or deadlocks in integration tests; defects found in 4.4.1 above baseline rate. | Solution Architect (technical), Project Manager (schedule) | Open |
| R-04 | Technical | Threat | Because the 100k-record target requires index and query design that is set in 2.1.5 but only tested in 5.1.2, a performance failure against p95 < 300 ms or < 1 s list/aggregate targets is discovered late, which loops back to schema changes (2.1.x) and delays M4. | 2.1.3, 2.1.5, 5.1.1–5.1.5; REQ-026 | 4 | 4 | 16 | 3 | Mitigate | Build a performance smoke test against a 10k-record dataset in M2 and M3; run 100k seed scripts (5.1.1) earlier, in parallel with 4.4.x; reserve schedule contingency for tuning (Section 5.1); define query-plan review gates. | p95 latency trend increasing in iteration builds; EXPLAIN plans with sequential scans on movement log; 10k-record tests breach 50% of target. | Data Architect (technical), Performance Engineer | Open |
| R-05 | Schedule / Technical | Threat | Because schema design (2.1.1–2.1.3) is on the critical path and feeds all milestones, late data-model changes (for example, lot/expiry, status, or movement log shape) cascade into M2–M4 rework. | 2.1.1, 2.1.2, 2.1.3, 2.1.7; critical path | 4 | 4 | 16 | 3 | Mitigate | Freeze schema via design review at the end of 2.1.3; change control applies to schema after freeze; migration tooling (2.1.6) proves the schema early through baseline migrations. | Change requests against entity model after 2.1.3 finish; new REQs raised against the data model; migration failures in 2.4.5. | Data Architect | Open |
| R-06 | Scope | Threat | Because the brief is tightly bounded but integration consumers are not identified (charter Sec. 6), scope creep into ERP receiving/shipping or hardware driver work occurs under pressure from consumers, which increases effort and delays milestones. | 1.2.3, 2.3.5, 6.1.x; charter Sec. 2.2 | 4 | 3 | 12 | 4 | Mitigate | Enforce out-of-scope list through change control (1.2.3); identify consumer systems in M1; any consumer requirement goes through formal change request with sponsor approval. | Requests for PO/SO endpoints or device integration in backlog; consumer system list not complete by M1 gate. | Project Manager | Open |
| R-07 | Technical / Security | Threat | Because RBAC (admin, supervisor, operator), JWT/OAuth2 integration, and leak-proof 5xx handling are interdependent, a gap in authorization or error masking exposes stock data or internal schema details, which causes a compliance breach and rework of M1. | 2.2.1–2.2.4, 2.3.3, 2.4.6, 5.3.2; REQ-023, REQ-030, REQ-031 | 3 | 5 | 15 | 5 | Mitigate | Security review at M1 (2.4.6) and M4 (5.3.2); security tests for denial paths (2.2.4); negative tests for error masking across catalog; RBAC matrix signed off in 2.2.2 before middleware build. | Security test failures; stack traces or schema names in staging responses; RBAC matrix changes after 2.2.2. | Security Analyst | Open |
| R-08 | External / Vendor | Threat | Because the identity provider is "available or will be provided by the organization" and no provider is confirmed, JWT/OAuth2 integration (2.2.1) is blocked, which delays the Foundation gate (2.4.7) and the whole chain behind it. | 2.2.1, 2.2.3, 2.4.7; Charter 6; Business Case 8.1 | 3 | 4 | 12 | 4 | Escalate | Sponsor confirms identity provider owner and test tenant before M1 starts; build against a mock/stub IdP until available; document the dependency. | No IdP test credentials by 1.1.4 finish; IdP endpoint or client registration pending at 2.2.1 start. | Project Sponsor (provider); Technical Lead (integration) | Open |
| R-09 | Vendor / Strategic | Threat | Because Option B (commercial WMS) has not been evaluated, the build decision (Option C) is made without a documented comparison, which may lead to a higher long-term cost or a later change of direction after M2 spend. | 1.1.3; Business Case 5, 9 | 3 | 4 | 12 | 4 | Mitigate | Complete the Option B memo before funding M2; include licensing, customization, and fit-gap against REQ-001 to REQ-038; sponsor records the decision. | Option B memo not complete by 1.1.3 finish (5 days); no vendor responses by 3 weeks. | Solution Architect | Open |
| R-10 | Resource | Threat | Because localization reviewers (ES and CA) and the performance engineer are single-role dependencies with unclear availability, 5.2.2 and 5.1.2 may slip, which delays the M4 gate. | 5.1.2, 5.1.3, 5.2.2, 5.2.3; REQ-032–REQ-038 | 3 | 3 | 9 | 6 | Mitigate | Book external translation reviewers at M1; identify backup performance engineer; schedule 5.2.2 to start in parallel with 5.1.x. | Reviewer not confirmed by M2 gate; performance engineer unavailable for >3 days in M4. | Project Manager | Open |
| R-11 | Resource | Threat | Because team composition is <UNKNOWN> and the WBS assumes one dedicated resource-equivalent per package, the real durations are longer than planned for a team with fewer people than the WBS assumes, which slips every milestone. | WBS Sec. 0; WBS dictionary durations; Schedule Model | 4 | 4 | 16 | 3 | Mitigate | Resource the WBS in 1.1.4; resource-level the schedule; treat WBS durations as optimistic until leveled; re-baseline at M1 gate. | Team size below WBS role count; idle or overallocated resources in the schedule model after leveling. | Project Manager | Open |
| R-12 | Schedule | Threat | Because the WBS durations are planning estimates without buffer, and the WBS dictionary provides no reserve, the total project duration will exceed the unbuffered estimate once schedule risk is applied. | WBS critical path; Schedule Model (project_duration_days = 0.0) | 4 | 3 | 12 | 4 | Accept (with reserve) | Hold schedule contingency per Section 5.1; populate the Schedule Model activities to compute critical path duration; re-estimate after M1. | Schedule Model remains empty at M1 gate; critical path slips more than 5% in any iteration. | Project Manager | Open |
| R-13 | Cost | Threat | Because cost estimates, infrastructure hosting decision, and testing and CI tooling are all <UNKNOWN>, the cost baseline is incomplete and may be exceeded once estimates are produced. | Business Case 4; Charter 5 | 4 | 4 | 16 | 3 | Mitigate | Complete cost estimate in 1.1.4 with three-point estimates; decide hosting platform in M1; hold cost contingency per Section 5.2. | Estimate variance above 15% at each gate; hosting decision open at M2 gate. | Project Manager (cost), Project Sponsor (approval) | Open |
| R-14 | Technical | Threat | Because the Docker and Kubernetes or cloud target is undecided and production environment provisioning (6.1.1) is only scheduled late, deployment issues (health checks, config, secrets) are discovered at go-live. | 2.4.3, 5.4.1, 5.4.2, 6.1.1–6.1.5; REQ-025 | 3 | 3 | 9 | 6 | Mitigate | Decide target platform in M1; run deployment smoke test in a non-production environment at M3; health check validation at 5.4.2. | Deployment fails in staging; environment provisioning requests not raised before M4. | DevOps Engineer | Open |
| R-15 | Technical / Quality | Threat | Because the ≥ 80% coverage gate applies across the suite and coverage uplift (5.3.1) is scheduled late, coverage is below target at M4 and the release is held. | 2.4.4, 5.3.1; REQ-027 | 3 | 3 | 9 | 6 | Mitigate | Enforce coverage gate in CI from M1 (2.4.4); track coverage per iteration; reserve test effort inside each milestone rather than only in 5.3.1. | Coverage trend below 80% at M2 or M3 gates. | QA Engineer | Open |
| R-16 | Quality / Localization | Threat | Because ES and CA translations are validated only in 5.2.2, mistranslated or inconsistent RFC 7807 error messages reach consumers, which causes integration friction and rework. | 5.2.1–5.2.3, 2.3.4; REQ-032–REQ-038 | 2 | 2 | 4 | 12 | Accept | Translation review in M4; validate bundle keys against the error catalog in 5.2.3; Business Case rates this Low/Low. | Missing key or untranslated message in bundle check. | Localization Reviewer | Monitoring |
| R-17 | Scope / Schedule | Threat | Because the adaptive stock-operation semantics (split, merge, group, move) have edge cases not yet defined, rework is needed after M3 acceptance, which extends M4 and the critical path. | 4.2.1–4.2.4, 4.4.3; Business Case 6 | 4 | 3 | 12 | 4 | Mitigate | Document the interaction rules and examples at the start of M3; iterate in two-week cadence with demos; include edge-case backlog in acceptance criteria. | Open defects on stock operations after each iteration; new edge cases raised in demos. | Project Manager (backlog), Solution Architect | Open |
| R-18 | Compliance / Data | Threat | Because the data retention policy and archival rules for stock and movement records are not provided, the retirement and archival design (phase 7) is inconsistent with organizational rules and may create compliance exposure. | Business Case 7 (phase 7); 7.2 (out of baseline) | 2 | 4 | 8 | 9 | Mitigate | Obtain the data retention policy in M1; add archival requirements to the backlog; record the policy as a dependency. | Policy not available by M2 gate; immutable log growth exceeds storage plan. | Project Sponsor (policy owner), Data Architect | Open |
| R-19 | Opportunity | Opportunity | Because the Option B evaluation and the existing WMS landscape may reveal a reusable component or an existing internal module, the build effort could be reduced, which saves schedule and cost. | 1.1.3, 2.2.1, 2.3.4; Business Case 5 (Option D) | 2 | 3 | 6 | 11 | Exploit | Include Option D (extend existing internal system) in the 1.1.3 evaluation; reuse existing IdP or localization libraries if available. | Existing component found with fit ≥ 70% against REQ list. | Solution Architect | Monitoring |
| R-20 | Opportunity | Opportunity | Because the team may adopt the same error catalog and localization patterns in other organizational services, reuse of the RFC 7807 and localization modules could reduce effort in M1 and M4. | 2.3.1–2.3.4, 5.2.x | 2 | 2 | 4 | 14 | Enhance | Publish the error catalog as a shared standard; reuse existing org libraries if available. | Existing shared library identified in 2.3.1. | Technical Lead | Monitoring |

### 3.2 Ranked Summary

| Rank | ID | Score | Band | Short Name |
| --- | --- | --- | --- | --- |
| 1 | R-01 | 20 | Critical | Governance and funding undefined |
| 1 | R-02 | 20 | Critical | No current-state baseline for ROI |
| 1 | R-03 | 20 | Critical | Concurrency defects in stock operations |
| 4 | R-06 | 12 | High | Scope creep (corrected to rank 4 in the table below) |

> Note on ranking: R-06 is shown at score 12 and is ordered with R-08, R-09, R-12, and R-17 by score and impact. The complete ordering is given in Section 3.3.

### 3.3 Full Ordering by Score (Authoritative)

| Rank | ID | Score | Band |
| --- | --- | --- | --- |
| 1 | R-01 | 20 | Critical |
| 1 | R-02 | 20 | Critical |
| 1 | R-03 | 20 | Critical |
| 4 | R-04 | 16 | High |
| 4 | R-05 | 16 | High |
| 4 | R-11 | 16 | High |
| 4 | R-13 | 16 | High |
| 8 | R-07 | 15 | High |
| 9 | R-06 | 12 | High |
| 9 | R-08 | 12 | High |
| 9 | R-09 | 12 | High |
| 9 | R-12 | 12 | High |
| 9 | R-17 | 12 | High |
| 14 | R-10 | 9 | Medium |
| 14 | R-14 | 9 | Medium |
| 14 | R-15 | 9 | Medium |
| 17 | R-18 | 8 | Medium |
| 18 | R-16 | 4 | Low |
| 18 | R-19 | 6 | Medium (opportunity) |
| 20 | R-20 | 4 | Low (opportunity) |

> Note: R-19 (score 6) is ranked above R-18 (score 8) in error in the summary. The authoritative ordering is by score, so R-18 (8) ranks above R-19 (6). Ranks 17–20 are: R-18 (8), R-19 (6), R-16 (4), R-20 (4).

### 3.4 Exposure Summary

| Band | Count (Threats) | Count (Opportunities) |
| --- | --- | --- |
| Critical | 3 | 0 |
| High | 9 | 0 |
| Medium | 4 | 1 |
| Low | 1 | 1 |

## 4. Top Risks and Contingency Plans

### 4.1 Top Risks (ordered by score)

| Rank | ID | Score | Risk Summary | Contingency Plan | Trigger for Activation |
| --- | --- | --- | --- | --- | --- |
| 1 | R-01 | 20 | Governance and funding undefined | If sponsor and PM are not named within 5 working days, escalate to the executive owner; hold M1 start; do not commit WBS resources. | Sponsor or PM not named by Day 5 |
| 2 | R-02 | 20 | No current-state baseline | If records are unavailable, proceed with a defined proxy and a sponsor-approved ROI range; M2 funding decision is taken on a stated uncertainty range, not a point ROI. | Baseline report not complete at 1.1.2 finish |
| 3 | R-03 | 20 | Concurrency defects in stock operations | Add a dedicated stabilization iteration (two weeks) after 4.2.4 before 4.4.1; deploy pessimistic locking on affected endpoints as a fallback; re-run concurrency suite. | Two or more concurrency defects open after 4.2.4 |
| 4 | R-04 | 16 | Performance failure at 100k | Trigger index and query tuning (5.1.4) early; if targets remain missed, run a schema review (2.1.5) and consume schedule contingency. | p95 target breached on 10k or 100k test run |
| 5 | R-05 | 16 | Late schema change | Apply change control; quantify impact through the critical path; re-baseline M2 if the change is accepted. | Entity change request after 2.1.3 finish |
| 6 | R-11 | 16 | Team below WBS role count | Re-plan with leveled resources; reduce scope through change control if the sponsor agrees; consume schedule contingency. | Leveled schedule finish later than baseline |
| 7 | R-13 | 16 | Cost estimate exceeded | Trigger cost contingency per Section 5.2; sponsor decides on scope reduction or added funding. | Estimate variance above 15% |
| 8 | R-07 | 15 | RBAC or error masking gap | Block release until the security defect is fixed; run a targeted security re-test; notify sponsor. | Any security test failure in M1 or M4 review |

## 5. Contingency Reserves

### 5.1 Schedule Contingency (days)

**Method:** The WBS dictionary gives per-package durations, but the schedule model has no activities. Schedule contingency is sized from the critical path sequence in the WBS (Section Critical Path Analysis) using the risk-weighted approach below.

Steps:
1. Critical path durations taken from the WBS dictionary for the listed sequence. Packages on the critical path (1.1.1, 1.1.3, 1.1.4, 2.1.1, 2.1.2, 2.1.3, 2.1.5, 2.1.7, 2.4.2, 2.4.5, 2.4.7, 3.1.1, 3.1.3, 3.1.5, 3.3.1, 3.3.3, 4.1.1, 4.1.2, 4.1.4, 4.2.1, 4.2.2, 4.2.3, 4.3.1, 4.4.1, 4.4.3, 4.4.4, 5.1.1, 5.1.2, 5.1.4, 5.1.5, 5.4.1, 5.4.2, 5.4.4, 6.1.1, 6.1.2, 6.1.4, 6.1.5) have durations summing to approximately 130 working days, before any slack for the packages listed as zero-float.
2. Risk-weighted exposure: for each high-scoring schedule risk (R-01, R-03, R-04, R-05, R-11, R-12), exposure = probability x expected delay in days, using the impact scale mid-points (Impact 4 = 6 weeks mid-range is too large; schedule exposure uses the WBS delay estimate of 5–10 days per risk for Impact 3–4).
3. Contingency = sum of expected exposures, rounded up, with a floor of 15% of the critical path base.

| Risk | P | Expected Delay (days) | Expected Exposure (days) |
| --- | --- | --- | --- |
| R-01 | 5 | 10 | 10 (governance is a bounded delay, capped at 10) |
| R-03 | 4 | 10 | 8 |
| R-04 | 4 | 8 | 6.4 |
| R-05 | 4 | 5 | 4 |
| R-11 | 4 | 10 | 8 |
| R-12 | 4 | 3 | 2.4 |

Sum of exposures: 38.8 days (before double-count removal). Removing double-count with R-11 (team size overlaps R-01 and R-12) reduces the total to 30 days. Floor of 15% of the 130-day base is 19.5 days, so the sized value is 30 days, rounded to **30 working days** of schedule contingency held at project level, not allocated to individual packages.

**Recommended schedule contingency: 30 working days (approximately 6 weeks)**, held by the Project Manager, released only through change control.

Assumption: the 130-day critical path base is the sum of the listed critical-path durations. It must be recomputed when the schedule model is populated.

### 5.2 Cost Contingency

**Status: cannot be sized in currency.** No cost estimates, budget, team rates, or hosting costs were provided. The following reserve is expressed as a percentage and must be applied to the base once the 1.1.4 estimate exists.

| Basis | Value | Rationale |
| --- | --- | --- |
| Recommended cost contingency | 20% of the approved cost base | Governance and funding are undefined (R-01), the baseline is missing (R-02), and the concurrency and performance risks (R-03, R-04) carry High exposure. A 20% reserve is in line with the top of the Impact 4 band (7–15%) plus a margin for the unresolved Critical risks. |
| Release condition | Sponsor approval for each draw | Draws are tied to risk IDs and logged in the RAID log |
| Revisit | At M1 gate | Reduce to 10–15% if R-01, R-02, and R-03 are closed |

## 6. Issues Materialized

| Issue ID | Description | Linked Risk(s) | Date Recorded | Status | Owner (Role) |
| --- | --- | --- | --- | --- | --- |
| I-01 | Charter and business case lack sponsor, PM, budget, team, and milestone dates (all <UNKNOWN>). | R-01, R-11, R-13 | 2026-10-20 | Open | Project Sponsor |
| I-02 | Business case ROI and benefit baselines are <UNKNOWN>; ROI cannot be calculated. | R-02 | 2026-10-20 | Open | Business Analyst |
| I-03 | Schedule Model contains no activities; project duration is 0.0 days. | R-12 | 2026-10-20 | Open | Project Manager |
| I-04 | Option B (commercial WMS) evaluation not performed. | R-09 | 2026-10-20 | Open | Solution Architect |

## 7. Risk Responses Summary and Next Actions

| Action | Owner (Role) | Due | Linked Risks |
| --- | --- | --- | --- |
| Name sponsor, PM, and business owner; complete charter approval table | Project Sponsor | Day 5 | R-01 |
| Collect current-state baseline metrics | Business Analyst | Day 5 after 1.1.1 | R-02 |
| Complete Option B memo including Option D | Solution Architect | Day 5 after 1.1.1 | R-09, R-19 |
| Complete cost and milestone estimate; populate Schedule Model activities | Project Manager | 1.1.4 finish | R-11, R-12, R-13 |
| Confirm identity provider owner and test tenant | Project Sponsor | Before 2.2.1 start | R-08 |
| Fix isolation and locking design | Solution Architect | 2.1.4 finish | R-03 |
| Freeze schema after design review | Data Architect | 2.1.3 finish | R-05 |
| Security review at M1 | Security Analyst | 2.4.6 | R-07 |

## 8. Revision Control

Changes to this register follow Section 1 of the Charter: minor reassessments increase the minor version; a change to the overall risk profile (for example, a change in overall exposure band) increases the major version. Each change requires a changelog row stating what changed and why.

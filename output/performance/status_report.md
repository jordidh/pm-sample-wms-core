  # Status Report: Warehouse Inventory & Storage Management REST API (WMS Core)

| Field | Value |
| --- | --- |
| Document | Status Report (Stakeholder Communications Pack) |
| Project Name | Warehouse Inventory & Storage Management REST API (WMS Core) |
| Current Version | 1.0 |
| Status Date | 2026-10-20 |
| Date | 2026-10-20 |
| Trigger | Baseline performance measurement (no actuals reported) |

## Changelog

| Version | Date | Change Description |
| --- | --- | --- |
| 1.0 | 2026-10-20 | Initial version |

## 1. Executive Summary

**Overall status: RED.** The project cannot yet be measured against an approved baseline. This is a baseline measurement, not a performance assessment.

- **Cost:** RED. BAC is not established (cost estimates <UNKNOWN>, currency UNKNOWN). AC = 0, so CPI is null.
- **Schedule:** RED. The Schedule Model has 0 activities and a 0.0-day duration. PV = 0, so SPI is null. Milestone dates are <UNKNOWN>.
- **Scope:** AMBER. Scope is defined, but integration consumers are unidentified (R-06).
- **Quality:** AMBER. No quality evidence yet; concurrency and performance risks are High (R-03, R-04).
- **Risk:** RED. 3 Critical risks (R-01, R-02, R-03), overall exposure HIGH.

**Decision needed now:** The Project Sponsor must name the Sponsor, Project Manager, Business Owner, and Technical Lead before Day 5 (A-01, R-01). Without these names, no WBS resources should be committed and M1 should not start.

## 2. Stakeholder Communication Briefs

Each brief follows the communication plan (Section 4) and the RACI role (Section 3). Briefs are ordered by RACI weight: Accountable and decision-making first.

### 2.1 Project Sponsor (C-01) — Accountable for gate decisions, funding, and go-live

**Information depth:** Decision-level. Full RAG, EVM baseline status, Critical risks, and funding gates.

**Status:** RED. BAC = 0 (not established), PV = 0, EV = 0, AC = 0. CPI, SPI, and TCPI are null. EAC, ETC, and VAC are placeholders and are not forecasts.

**Decisions required:**
1. Name Sponsor, PM, Business Owner, and TL (A-01; R-01). Due: Day 5.
2. Confirm authority limits for budget and approval ceilings (A-02). Due: before M1 start.
3. Approve the funding gate for M2 conditional on baseline data (R-02, A-05). Decide whether to proceed under a stated ROI uncertainty range if records are absent.
4. Confirm the owner of the JWT/OAuth2 identity provider (A-04, R-08). Due: before 2.2.1 start.
5. Confirm the schedule contingency of 30 working days and the cost contingency of 20% of the approved cost base (Risk Register Section 5).

**Next reporting:** Monthly and at each milestone gate (C-01).

### 2.2 Steering Committee (C-02) — Sponsor, PM, BO, TL, Finance

**Information depth:** Milestone readiness, budget vs. plan, risks, change requests, gate readiness.

**Status:** Overall RED. No approved baseline for cost or schedule. No change requests open (Change Log v1.0; RFC-001 not opened).

**Asks:**
1. Confirm steering committee membership and cadence (A-06). Due: before M1 start.
2. Review Critical risks R-01, R-02, R-03 and confirm the escalation owners.
3. Note that M1 gate readiness is blocked until the governance preconditions are met.

**Next reporting:** Monthly and at each gate (C-02).

### 2.3 Project Manager (C-02, C-09, C-15) — Accountable for delivery, schedule, risk log, minor changes

**Information depth:** Full working detail: action owners, dates, EVM inputs, and reserves.

**Actions:**
1. Populate Schedule Model activities from the WBS dictionary and compute the critical path (R-12, I-03). Due: at WBS 1.1.4 finish.
2. Complete the cost and milestone estimate with three-point estimates (1.1.4; R-13). Set milestone dates (A-09). Due: before M2.
3. Resource-level the schedule and confirm team size against WBS role count (R-11).
4. Define percent-complete rules per WBS package so EV can be reported (EVM gap).
5. Establish time-recording and cost capture so AC can be reported.
6. Confirm the cost base for the Finance report (C-09).
7. Maintain the risk and issue log (R-01 to R-20; I-01 to I-04) and report medium and high items monthly.

**Next reporting:** Monthly (C-09 Finance report and steering committee pack).

### 2.4 Business Owner and Warehouse Operations Representatives (C-03) — Responsible for business rules and baseline metrics

**Information depth:** Functional progress, business rule decisions, and UAT scheduling.

**Actions:**
1. Collect current-state baseline metrics (discrepancy rate, reconciliation hours, write-off value) from existing records for a defined sample period (A-05; R-02). Due: before M2 funding.
2. If records are absent, agree proxy metrics with the Business Analyst and record the data gap as an assumption.
3. Approve business rules for stock status, reason codes, and write-off policy (RACI decision row).
4. Confirm UAT scheduling for M3 stock operations (C-03).

**Highlights:** The stock-operation semantics (split, merge, group, move) are adaptive; edge cases will need to be defined before M3 (R-17).

**Next reporting:** Bi-weekly, aligned to iteration review (C-03).

### 2.5 Development and QA Teams (C-04) — Responsible for build and test

**Information depth:** Backlog, acceptance criteria, defects, CI status.

**Actions:**
1. Provide input to the WBS 1.1.4 estimate and the team composition (R-11).
2. Prepare the concurrency test harness (2.4.5) and coverage tracking from M1 (R-15).
3. Prepare the 10k-record performance smoke test for M2 and M3 (R-04).
4. Confirm acceptance criteria for stock operations (R-17).

**Highlights:** No defects or CI results are recorded yet, so none are reported in EVM.

**Next reporting:** Daily stand-up and per iteration planning (C-04).

### 2.6 Security & Compliance Owner (C-05) — Accountable for security review at M1 and M4

**Information depth:** Security design, RBAC matrix, and error-masking evidence.

**Asks:**
1. Review the RBAC matrix (2.2.2) before the middleware build.
2. Plan the M1 security review (2.4.6) and M4 review (5.3.2).
3. Confirm the expected error-masking tests across the RFC 7807 catalog (R-07).

**Highlights:** Risk R-07 (score 15, High) relates to RBAC and error-masking gaps.

**Next reporting:** At M1 and M4 gates; ad hoc on architecture changes (C-05).

### 2.7 DevOps / Platform Engineering (C-06) — Responsible for deployment and operations readiness

**Information depth:** Environment needs, deployment plan, health checks, observability.

**Asks:**
1. Confirm the hosting target (Kubernetes or cloud) and infrastructure owner (A-07; R-14). Decision due in M1.
2. Prepare the deployment smoke test for M3.

**Next reporting:** Weekly and before each release candidate (C-06).

### 2.8 Warehouse Supervisors and Operators (C-07) — Informed on stock operations and training

**Information depth:** Highlights only. No action required at this stage.

**Highlights:** The system is not yet built. Demonstrations will start at iteration demos. Training notices will be issued before go-live. Operators will receive RFC 7807 errors in EN, ES, and CA (M4).

**Next reporting:** Each iteration demo; training before go-live (C-07).

### 2.9 Warehouse Administrators (C-08) — Informed on configuration and provisioning

**Information depth:** Highlights only.

**Highlights:** Configuration, role provisioning, and location-tree changes will be communicated per release.

**Next reporting:** Per release; ad hoc on change (C-08).

### 2.10 Finance / Budget Approver (C-09) — Informed on cost and funding

**Information depth:** Cost actuals, forecast, funding requests, ROI baseline status.

**Status:** No cost actuals (AC = 0). No cost baseline (BAC = 0, currency UNKNOWN). ROI cannot be calculated (I-02).

**Asks:**
1. Confirm the currency and the approval ceiling for funding releases (A-02).
2. Validate the cost estimate once produced in 1.1.4.

**Next reporting:** Monthly finance report (C-09).

### 2.11 Downstream Integration Consumers (C-10) — Informed on API changes

**Information depth:** Highlights only.

**Highlights:** No API has been published. Consumers are not yet identified (A-03). Breaking-change notice of at least 30 days is assumed.

**Next reporting:** Per release; at least 30 days' notice of breaking changes (assumed) (C-10).

### 2.12 Identity Provider Operator (C-11) — Informed on integration contract

**Asks:** Confirm the client registration and role-claim mapping before M1 integration testing. Confirm the owner (A-04).

**Next reporting:** Before M1 integration testing; ad hoc on change (C-11).

### 2.13 Localization Reviewers (C-12) — Informed on message bundles

**Highlights:** Message keys will be shared per M4 bundle cycle. Confirm the ES and CA reviewer (A-08). Due: before M4.

**Next reporting:** Per M4 bundle cycle (C-12).

### 2.14 Operations / Support Team (C-13) — Informed on handover

**Highlights:** Runbooks and handover begin before go-live. Weekly during hypercare (assumed).

**Next reporting:** Before go-live; weekly during hypercare (C-13).

### 2.15 Internal Audit / Inventory Control (C-14) — Informed on audit trail

**Highlights:** Movement log and audit reports will be made available once the movement log is built (M3). Reason code review scheduled per quarter (assumed).

**Next reporting:** Quarterly (assumed) and on request (C-14).

## 3. Open Items and Next Actions

| Action | Owner (Role) | Due |
| --- | --- | --- |
| Name Sponsor, PM, BO, and TL (A-01) | Project Sponsor | Day 5 |
| Confirm authority limits (A-02) | Project Sponsor | Before M1 start |
| Confirm steering committee membership (A-06) | Project Manager | Before M1 start |
| Confirm identity provider owner (A-04) | Project Manager | Before M1 integration testing |
| Collect current-state baseline metrics (A-05) | Business Owner | Before M2 funding |
| Populate schedule model and milestone dates (A-09) | Project Manager | Before M2 |

## 4. Next Reporting Date

**Next status report: at the first reporting cycle after A-01 closes, and no later than Day 5.** Then monthly, aligned to the Steering Committee and Finance report (C-01, C-02, C-09). Bi-weekly stakeholder updates begin once the iteration cadence starts (C-03).

## 5. Assumptions and Evidence Limits

* All currency values are in UNKNOWN currency. BAC is recorded as 0 because no cost estimate exists.
* The Schedule Model is empty; PV, SPI, and milestone forecasts cannot be stated.
* No actuals and no percent-complete data were reported; EV and AC are 0.
* EAC, ETC, and VAC are placeholders and must not be read as forecasts.
* Stakeholder names are <UNKNOWN>; roles are used as identifiers.
* Engagement levels are assumed in the Stakeholder Register and should be validated with the Sponsor.

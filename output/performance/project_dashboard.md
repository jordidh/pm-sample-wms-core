# Project Dashboard: Warehouse Inventory & Storage Management REST API (WMS Core)

| Field | Value |
| --- | --- |
| Document | Project Dashboard (Executive Visual Radiator) |
| Project Name | Warehouse Inventory & Storage Management REST API (WMS Core) |
| Current Version | 1.0 |
| Status Date | 2026-10-20 |
| Date | 2026-10-20 |
| Trigger | Baseline performance measurement (no actuals reported) |

## Changelog

| Version | Date | Change Description |
| --- | --- | --- |
| 1.0 | 2026-10-20 | Initial version |

## 1. Decision Summary (read first)

**Overall RAG: RED (baseline not established; governance and funding undefined)**

**Decision needed from the Project Sponsor:**
1. Name the Sponsor, Project Manager, Business Owner, and Technical Lead (Action A-01; Risk R-01, due Day 5).
2. Confirm authority limits for budget and approval ceilings (Action A-02).
3. Approve the Business Analyst to collect current-state baseline metrics (discrepancy rate, reconciliation hours, write-off value) before M2 funding (Action A-05; Risk R-02).
4. Confirm the owner of the JWT/OAuth2 identity provider and test tenant before the 2.2.1 start (Action A-04; Risk R-08).

**Why RED:** The baseline is not established. BAC is 0 because no cost estimate exists, the Schedule Model has no activities, and no actuals or work package budgets were reported. Performance indices cannot be computed (CPI and SPI are null), so no colour can be justified by a measured index. The RED status reflects the three Critical risks (R-01, R-02, R-03, each scored 20) and the missing governance baseline, not a measured overrun.

## 2. RAG Status by Dimension

| Dimension | RAG | Justifying Metric (2026-10-20) | Basis |
| --- | --- | --- | --- |
| Overall | RED | BAC = 0 (not established); CPI = null; SPI = null; 3 Critical risks | Baseline incomplete; governance preconditions open |
| Scope | AMBER | Scope defined in charter and WBS; 0 change requests raised; 0 RFCs open; R-06 (score 12, High) on scope creep | Scope boundary is stable, but integration consumers are unidentified (A-03) |
| Schedule | RED | SV = 0.0; SPI = null (PV = 0); Schedule Model: 0 activities, duration 0.0 days; R-01 and R-12 (scores 20 and 12) | No schedule baseline exists; WBS critical path (approx. 130 working days of durations, planning estimate) is not yet loaded |
| Cost | RED | CPI = null (AC = 0); BAC = 0 (currency UNKNOWN); CV = 0.0; R-13 (score 16, High) | Cost baseline absent; funding gate depends on undelivered estimate (1.1.4) |
| Quality | AMBER | 0 defects recorded in EVM data; ≥80% coverage gate and 100k stress test not yet executed; R-03 and R-04 (scores 20 and 16, High) | No quality evidence exists yet, so the rating reflects technical risk rather than measured defects |
| Risk | RED | 3 Critical (R-01, R-02, R-03); 9 High (threats); overall exposure rated HIGH | Risk Register v1.0, Section 3.4 |

**RAG thresholds applied:** CPI and SPI: Green ≥ 0.95; Amber 0.90–0.95; Red < 0.90, or null with work overdue. Both CPI and SPI are null (AC and PV are zero), so the Cost and Schedule ratings are set by the baseline gap, not by index values. Each rating must be re-tested when actuals and a populated schedule are available.

## 3. Key Performance Indicators (EVM)

Currency: UNKNOWN. All values are in the unit of the EVM source (UNKNOWN currency).

| KPI | Value | Meaning | Status |
| --- | --- | --- | --- |
| BAC (Budget at Completion) | 0.0 | No cost estimate; placeholder only | Not established |
| PV (Planned Value) | 0.0 | No time-phased schedule baseline | Not established |
| EV (Earned Value) | 0.0 | No physical percent complete reported | No progress reported |
| AC (Actual Cost) | 0.0 | No actuals reported | No actuals reported |
| CV (Cost Variance = EV − AC) | 0.0 | No variance measurable | Not measurable |
| SV (Schedule Variance = EV − PV) | 0.0 | No variance measurable | Not measurable |
| CPI (EV / AC) | null | AC is zero | Not computable |
| SPI (EV / PV) | null | PV is zero | Not computable |
| EAC (Estimate at Completion) | 0.0 (placeholder) | BAC / CPI cannot be computed | Do not read as a forecast |
| ETC (Estimate to Complete) | 0.0 (placeholder) | EAC − AC; not meaningful | Do not read as a forecast |
| VAC (Variance at Completion = BAC − EAC) | 0.0 (placeholder) | Not meaningful | Do not read as a forecast |
| TCPI (To-Complete Performance Index) | null | BAC = AC (both zero) | Not computable |

**Note:** Values shown as 0 for EAC, ETC, and VAC are placeholders and must not be read as a forecast of zero cost.

## 4. S-Curve (Cumulative PV, EV, AC)

The S-curve series is empty. No period-by-period planned value can be derived from the empty schedule model, and no cost baseline exists to phase against. The chart below is a placeholder frame with no data points; it will be populated when the schedule model and cost baseline are issued.

```mermaid
xychart-beta
    title "S-Curve: Cumulative PV, EV, AC (no data yet)"
    x-axis "Reporting period" []
    y-axis "Cumulative value (currency UNKNOWN)" 0 --> 1
    line "PV" []
    line "EV" []
    line "AC" []
```

## 5. Critical Milestones

Milestone dates are <UNKNOWN> (charter Section 4; Action A-09). Forecast dates cannot be stated because the Schedule Model is not populated.

| Milestone | Baseline Date | Forecast Date | Status |
| --- | --- | --- | --- |
| M1: Foundation (DB schema, auth/RBAC, RFC 7807 handler, OpenAPI skeleton, CI/CD) | <UNKNOWN> | <UNKNOWN> | Not started (no baseline); gated by R-01 |
| M2: Hierarchy engines (location and container CRUD, nested moves, capacity) | <UNKNOWN> | <UNKNOWN> | Not started; funding gate held by R-02 |
| M3: Stock operations (allocations, atomic split/merge/group, movement log) | <UNKNOWN> | <UNKNOWN> | Not started; concurrency risk R-03 |
| M4: Production readiness (100k stress tests, EN/ES/CA bundles, Docker image, ≥80% coverage) | <UNKNOWN> | <UNKNOWN> | Not started; performance risk R-04 |
| Production go-live approval | <UNKNOWN> | <UNKNOWN> | Not started; depends on M4 and Section 6 of Charter |

**Schedule basis:** The WBS critical path is listed as a sequence only (1.1.1 → 1.1.4 → 2.1.x … → 6.1.5). The sum of listed durations is approximately 130 working days, and 30 working days of schedule contingency is recommended (Risk Register Section 5.1). Neither figure has been validated against a populated schedule model.

## 6. Variance Commentary

| Variance | Current Value | Cause | Corrective Action | Owner (Role) |
| --- | --- | --- | --- | --- |
| No cost baseline (BAC = 0) | Not established | Cost estimates (Business Case Section 4) are <UNKNOWN>; no rate card or currency | Complete three-point cost estimate in WBS 1.1.4; sponsor approves BAC | Project Manager (cost); Project Sponsor (approval) |
| No schedule baseline (PV = 0, SV = 0) | Not established | Schedule Model has 0 activities; milestone dates <UNKNOWN> | Populate schedule model activities from WBS dictionary; set milestone dates before M2 | Project Manager |
| No actuals (AC = 0) | Not established | Project execution not yet reported | Establish time-recording and cost-capture process from the first working period | Project Manager |
| No progress (EV = 0) | Not established | No physical percent complete reported for any work package | Define percent-complete rules per WBS package and report at each status cycle | Project Manager; Technical Lead |
| Baseline not complete (R-02) | No current-state metrics | Records of discrepancy rate, reconciliation hours, write-off value do not exist yet | Business Analyst collects baseline from existing records for a defined sample; if absent, define proxy metrics and log as assumption | Business Owner (A-05); Project Manager (accountable) |

## 7. Top Risks

| Rank | ID | Score | Band | Risk (short name) | Response | Owner (Role) |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | R-01 | 20 | Critical | Governance and funding undefined | Escalate | Project Sponsor |
| 1 | R-02 | 20 | Critical | No current-state baseline for ROI | Mitigate | Business Analyst (accountable: Project Manager) |
| 1 | R-03 | 20 | Critical | Concurrency defects in stock operations | Mitigate | Solution Architect; Project Manager |
| 4 | R-04 | 16 | High | Performance failure at 100k records | Mitigate | Data Architect; Performance Engineer |
| 4 | R-05 | 16 | High | Late schema change | Mitigate | Data Architect |
| 4 | R-11 | 16 | High | Team below WBS role count | Mitigate | Project Manager |
| 4 | R-13 | 16 | High | Cost estimate exceeded | Mitigate | Project Manager; Project Sponsor |

Full register: Risk Register v1.0 (20 risks: 3 Critical, 9 High, 4 Medium, 2 Low, 1 Medium opportunity counted separately as shown in Section 3.4).

## 8. Open Changes and RFCs

| Item | Status | Detail |
| --- | --- | --- |
| RFC-001 | Not opened | No threshold breached; no change proposed |
| Change requests | 0 open | Change Log v1.0 records "No change" |
| Baseline version | 1.0 (unchanged) | No change to BAC or PV |
| Contingency draws | None | Schedule contingency: 30 working days recommended (held by PM); cost contingency: 20% of approved cost base, not yet sizable |

## 9. Recommended Reserves (for reference)

| Reserve | Recommended Value | Basis | Release Condition |
| --- | --- | --- | --- |
| Schedule contingency | 30 working days (approx. 6 weeks) | Risk Register Section 5.1 | Released only through change control |
| Cost contingency | 20% of approved cost base | Risk Register Section 5.2 | Sponsor approval per draw; revisit at M1 gate |

## 10. Assumptions and Evidence Limits

* BAC is not established. Business Case Section 4 cost estimates are <UNKNOWN>, and the currency is UNKNOWN. BAC is recorded as 0.
* The Schedule Model has an empty activities list, an empty critical path, and project duration of 0.0 days. PV is recorded as 0.
* No work package budgets exist. The WBS gives hours and durations without cost rates, so no per-package rows are produced.
* No actuals were reported (AC = 0) and no physical percent complete was reported (EV = 0).
* CPI, SPI, and TCPI are null because their denominators or numerators are zero. EAC, ETC, and VAC are placeholders.
* The S-curve has no data points.
* Milestone dates are <UNKNOWN>; project finish date cannot be stated.
* The baseline is unchanged at version 1.0; no RFC is open.

## 11. Next Reporting Date

**Next dashboard update: at the first reporting cycle after Action A-01 is closed, and no later than Day 5 (R-01 trigger).** Monthly thereafter, aligned to the Steering Committee (C-02) and Sponsor status report (C-01).

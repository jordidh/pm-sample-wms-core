# Risk Register: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 1.0
- **Date:** 2026-10-17

## Changelog
| Version | Date | Change Description |
|---|---|---|
| 1.0 | 2026-10-17 | Initial baseline risk assessment and risk register generation |

## 1. Complexity Drivers & Overall Project Risk Exposure

### Complexity Drivers
- **Technical Complexity:** High. The system requires complex recursive or nested hierarchical data structures (`warehouse` > `zone` > `aisle` > `rack` > `shelf` > `bin` and container nesting), rigorous concurrency control via optimistic locking (ETag/version headers), and sub-second query performance targets ($p95 < 300\text{ms}$ for single-item reads, $< 1\text{s}$ for list/aggregate queries over 100k records).
- **Organizational Complexity:** Low-to-Moderate. The project relies on external master data management (SKU definitions) and external clients properly adhering to RFC 7807 error formats and ETag headers, introducing integration dependencies.
- **Vendor & Infrastructure Complexity:** Low. Leverages standard containerization (Docker) and standard relational database engines (PostgreSQL).
- **Regulatory & Audit Complexity:** High. Mandatory 100% immutable audit log coverage for every stock and container movement, strict ACID transaction compliance, and absolute zero tolerance for negative inventory or orphaned containers.
- **People & Resource Complexity:** Moderate. Requires tight coordination between database architects, backend developers, security engineers, and DevOps specialists under tight milestone constraints.

### Overall Project Risk Exposure
Based on the initial threat landscape, the project faces a moderate-to-high residual risk exposure driven primarily by concurrency race conditions under high-frequency updates and database performance degradation at scale. Active mitigation through robust architectural design in Milestone 1 and optimistic locking mechanisms in Milestone 4 are mandatory to bring exposure within acceptable thresholds.

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

### Score Thresholds & Categories
- **Low (1 - 4):** Monitor; accept risk with standard operational management.
- **Medium (5 - 9):** Mitigate; assign owner and specific proactive response.
- **High (10 - 15):** Urgent; requires active contingency reserves, senior management escalation, and rigorous mitigation.
- **Critical (16 - 25):** Severe threat; immediate escalation, mandatory architectural redesign, or project halting until resolved.

## 3. Comprehensive Risk Table

| ID | Category | Threat/Opportunity | Risk Statement (Cause - Event - Effect) | Affected WBS / Backlog Items | Probability | Impact | Score | Ranking | Response Strategy | Mitigation / Response Steps | Trigger / Early Warning Signs | Risk Owner Role | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| RSK-01 | Technical | Threat | **Cause:** High-frequency concurrent stock updates and movements by multiple operators <br>**Event:** Concurrency race conditions occur across shared bin locations or nested containers <br>**Effect:** Data corruption, negative inventory levels, or lost stock allocations. | US-202, US-301, WBS 1.1.1 | 4 | 5 | 20 | 1 | Mitigate | Implement strict optimistic locking via ETag/version headers on all mutating resources (US-301); enforce database-level unique constraints and atomic multi-resource transactions. | Increased frequency of HTTP 409 Conflict responses or intermittent database deadlock logs during load testing. | Backend Developer | Open |
| RSK-02 | Technical | Threat | **Cause:** Large inventory volumes exceeding 100,000 records <br>**Event:** Database queries and aggregate searches degrade in performance under load <br>**Effect:** Failure to meet $p95$ latency targets ($< 300\text{ms}$ single, $< 1\text{s}$ list over 100k records), leading to operator bottlenecks. | US-302, WBS 1.1.1 | 4 | 4 | 16 | 2 | Mitigate | Design optimized indexing strategies for location hierarchies and stock batch tables during Milestone 1 (WBS 1.1.1); conduct rigorous query profiling and stress testing during Milestone 4. | Query execution times exceeding 200ms on test datasets during early integration milestones. | Database Architect | Open |
| RSK-03 | Technical | Threat | **Cause:** Complex hierarchical location structures and multi-level container nesting <br>**Event:** Orphaned containers or recursive query circular references are introduced <br>**Effect:** Data integrity violations and inability to accurately compute capacity or physical placement. | US-101, US-104, Epic 1.1, Epic 1.2 | 3 | 4 | 12 | 3 | Mitigate | Enforce strict parent-child foreign key validation, implement database check constraints for container weight/dimensions, and execute automated graph integrity unit tests. | Unexplained null parent references in staging database or recursive query timeouts. | Backend Developer | Open |
| RSK-04 | External | Threat | **Cause:** External clients or upstream product master data management (MDM) systems <br>**Event:** Upstream clients fail to handle RFC 7807 `problem+json` error responses correctly or submit invalid SKU references <br>**Effect:** Integration failures, unhandled client exceptions, and operational friction. | US-303, Assumptions, WBS 1.3.1 | 3 | 3 | 9 | 4 | Mitigate | Publish clear OpenAPI specifications, provide comprehensive integration guidelines, and build rigorous input validation middleware enforcing RFC 7807 standards. | High volume of support tickets regarding error payload parsing or schema mismatch reports from external integrators. | Technical Writer | Open |
| RSK-05 | Schedule | Threat | **Cause:** Strict automated CI test coverage gating ($≥ 80\%$) combined with tight Milestone 1 delivery timelines (10 days) <br>**Event:** Code coverage falls below threshold due to rapid prototyping <br>**Effect:** CI pipeline blocks deployment and delays milestone sign-off. | WBS 1.4.2, US-304 | 3 | 3 | 9 | 5 | Mitigate | Enforce test-driven development (TDD) practices from day one, mandate unit test creation alongside feature code, and monitor coverage metrics continuously in CI. | Daily CI build warnings showing test coverage hovering near the 80% threshold. | DevOps Engineer | Open |
| RSK-06 | Regulatory | Threat | **Cause:** Complex multi-step stock transformations (split, merge, group, ungroup) <br>**Event:** Audit log generation fails or records incomplete transaction metadata <br>**Effect:** Non-compliance with audit readiness requirements and loss of 100% traceability. | US-204, US-203 | 2 | 4 | 8 | 6 | Mitigate | Implement database-level triggers or immutable append-only event logging for every stock movement and adjustment prior to releasing Milestone 3. | Missing movement log entries during integration testing of split/merge operations. | Security Engineer | Open |
| RSK-07 | Resource | Opportunity | **Cause:** Highly modular REST architecture and standardized OpenAPI contracts <br>**Event:** External systems and internal developers rapidly onboard to WMS Core APIs <br>**Effect:** Accelerated integration timelines and reduced custom adapter development effort. | Strategic Drivers, Milestone 1-4 | 4 | 3 | 12 | 7 | Enhance | Publish polished SDK stubs and interactive Swagger documentation early in Milestone 1; establish dedicated developer feedback channels. | High download rates of API client SDKs or immediate positive feedback from integration partners. | Technical Writer | Open |

## 4. Top Risks & Contingency Plans

1. **RSK-01: Concurrency Race Conditions & Data Corruption (Score: 20)**
   - **Contingency Plan:** If optimistic locking failures spike under high load, immediately pause affected non-critical batch operations, enable pessimistic row-level locking (`SELECT ... FOR UPDATE`) on high-contention bin locations as an emergency fallback, and deploy hotfix patches within 24 hours.

2. **RSK-02: Query Performance Degradation at Scale (Score: 16)**
   - **Contingency Plan:** If stress testing in Milestone 4 reveals query execution times exceeding SLAs over 100k records, activate database read replicas, implement materialized views for aggregate inventory reports, and introduce Redis caching layers for frequently accessed location hierarchies.

3. **RSK-03: Hierarchical Data Integrity & Orphaned Containers (Score: 12)**
   - **Contingency Plan:** If data integrity violations occur during container nesting tests, deploy automated data-scrubbing migration scripts to identify and quarantine orphaned records, and temporarily disable nesting endpoints until schema constraints are reinforced.

## 5. Contingency Reserves Sized from Evidence

- **Schedule Contingency Reserve:** **3 Days**
  - *Basis:* Sized based on critical path float analysis (WBS 1.1.1 through 1.4.2 total duration is 10 days) and historical variance in resolving complex database indexing (RSK-02) and CI pipeline gating bottlenecks (RSK-05). A 30% buffer on the 10-day critical path provides adequate coverage for concurrency debugging and performance tuning.

- **Cost Contingency Reserve:** **15% of Total Engineering Effort**
  - *Basis:* Sized from technical complexity drivers (RSK-01 and RSK-02). Addressing concurrency race conditions and large-scale query optimization typically requires specialized senior engineering hours and iterative stress testing. The 15% reserve covers contingency remediation sprints if performance or concurrency targets require architectural refactoring.

## 6. Materialized Issues

*No issues have materialized as of the baseline assessment date (2026-10-17). All risks are currently in an active `open` status under proactive monitoring.*
# Business Case: Warehouse Inventory & Storage Management REST API (WMS Core)

| Field | Value |
| --- | --- |
| Document | Business Case |
| Project Name | Warehouse Inventory & Storage Management REST API (WMS Core) |
| Current Version | 1.0 |
| Date | 2026-10-20 |

## Changelog

| Version | Date | Change Description |
| --- | --- | --- |
| 1.0 | 2026-10-20 | Initial version |

## 1. Problem / Opportunity Statement

The organization requires a core backend engine that governs warehouse storage, container tracking, and stock inventory. The project brief identifies the need for:

* Hierarchical management of physical warehouse layouts (warehouse > zone > aisle > rack > shelf > bin).
* Control of nested physical containers (pallets, boxes, totes) with unique SSCC/barcode identifiers.
* Granular stock allocations with lot/batch, expiry, UoM, and status (`available`, `blocked`, `quarantine`).
* Full auditability of every stock creation, displacement, split, merge, and write-off.
* Prevention of negative inventory, over-capacity placements, orphaned containers, and concurrency race conditions.

**Current-state pain points:** The brief does not document the current-state process, systems, or incident data. This is recorded as an assumption (see Section 8).

## 2. Strategic Drivers & Alignment

| Strategic Driver | Project Alignment |
| --- | --- |
| Inventory traceability and compliance | Immutable movement log (REQ-020) and mandatory reason codes for adjustments and write-offs (REQ-011 – REQ-019) |
| Operational accuracy | Negative-inventory prevention, capacity guardrails, non-cyclic hierarchy, and optimistic locking (REQ-021, REQ-022) |
| Integration readiness | RESTful JSON API, OpenAPI 3 specification, RFC 7807 error handling (REQ-024, REQ-028) |
| Reliability and performance | ACID multi-resource transactions (REQ-021), p95 latency targets (REQ-026) |
| Security and governance | JWT/OAuth2 with RBAC roles `admin`, `supervisor`, `operator` (REQ-023) |

The project is positioned as the core engine that future ERP integration phases (purchase order receiving, sales order shipping) will depend on. Those integrations are explicitly out of scope for this phase.

## 3. Expected Benefits

Quantified benefits are **not provided** in the source brief. The following are qualitative benefits, with quantification targets marked as assumptions to be validated.

| Benefit | Type | Measure (Proposed) | Status |
| --- | --- | --- | --- |
| Complete end-to-end inventory traceability | Qualitative / Compliance | 100% of stock mutations recorded in the movement log | Target defined in brief |
| Reduction in inventory discrepancies | Quantitative (to be validated) | <UNKNOWN> baseline; target reduction to be agreed | Requires baseline data |
| Reduction in manual reconciliation effort | Quantitative (to be validated) | <UNKNOWN> hours/month baseline | Requires baseline data |
| Faster integration with downstream systems | Qualitative | Standardized REST and RFC 7807 error contracts | Target defined in brief |
| Query performance | Quantitative | p95 < 300 ms single-item reads; < 1 s list/aggregate queries over 100k records | Target defined in brief |
| Data consistency | Quantitative | Zero negative-inventory and orphaned-container records in acceptance testing | Target defined in brief |

## 4. Costs and ROI Estimate

No cost estimates, team sizing, budget, or infrastructure figures were provided. The ROI cannot be calculated from the available input.

| Cost Category | Estimate | Notes |
| --- | --- | --- |
| Engineering effort (4 milestones) | <UNKNOWN> person-months | Requires team composition and rates |
| Infrastructure (containerized, Kubernetes/cloud) | <UNKNOWN> per month | Requires hosting decision |
| Testing and CI/CD tooling | <UNKNOWN> | Includes ≥ 80% coverage pipeline and 100k-record stress testing |
| Training and change management | <UNKNOWN> | Operator, supervisor, and admin roles |
| Ongoing maintenance | <UNKNOWN> per year | Post-release support |

**ROI:** <UNKNOWN>. A payback period and net present value require the cost and benefit baselines listed above. Recommended next step: collect current-state baseline metrics (discrepancy rate, reconciliation hours, write-off value) before funding Milestone 2.

## 5. Options Considered

| Option | Description | Assessment |
| --- | --- | --- |
| A. Do nothing | Continue with current warehouse processes | Does not meet traceability or accuracy objectives; risk of growing discrepancy cost. |
| B. Adopt a commercial off-the-shelf WMS | License and configure an existing product | Options, fit, licensing cost, and customization needs are not evaluated in the brief. Assessment pending. |
| C. Build a custom WMS Core REST API (recommended in brief) | Build the scoped backend engine described in the brief | Matches the defined requirements (REQ-001 – REQ-038) and integration needs; requires full engineering investment. |
| D. Extend existing internal system | Add the required modules to a current system | Feasibility unknown; current systems are not described in the brief. |

Option C is the option specified by the project brief. Options B and D have not been analyzed due to missing information.

## 6. Recommended Development Approach

**Recommendation: Hybrid approach (predictive governance with adaptive delivery within milestones).**

**Justification:**

1. **Predictive elements (fixed):**
   * The data model, transactional rules, RBAC boundaries, and error contract (RFC 7807, error codes) are well-defined in the brief and carry compliance and integration risk. They benefit from upfront design and documented acceptance criteria.
   * The four milestones and their scope boundaries are defined. Budget and milestone gates support governance and funding decisions.
   * Non-functional targets (latency, coverage, 100k-record stress tests) are measurable and should be locked early.

2. **Adaptive elements (flexible):**
   * Complex stock operations (split, merge, group, ungroup, move) have interaction rules that are likely to surface edge cases during implementation. Iterative refinement with short feedback cycles reduces rework.
   * Localization (EN, ES, CA) and observability details can be refined as integration consumers provide feedback.
   * The brief does not specify end-user workflows or consumer systems, which are expected to evolve.

**Why not purely predictive:** Stock-movement semantics and concurrency behavior are complex and best validated through working increments and testing.

**Why not purely adaptive:** Auditability, RBAC, and error-contract requirements are compliance-driven and require stable, documented baselines before iteration.

## 7. Product Life Cycle Definition

| Phase | Purpose | Key Activities | Exit Criteria |
| --- | --- | --- | --- |
| 1. Initiation | Confirm business case and sponsorship | Approve business case and project charter; name sponsor and project manager | Charter approved |
| 2. Foundation (Milestone 1) | Establish architecture and governance baseline | DB schema with transactions and optimistic locking; auth/RBAC; RFC 7807 handler; OpenAPI skeleton; CI/CD | Foundation review passed; CI pipeline operational |
| 3. Core Build (Milestones 2–3) | Deliver functional modules | Location and container engines; stock operations; atomic movements; movement log | Functional acceptance tests passed for REQ-001 – REQ-020 |
| 4. Hardening & Readiness (Milestone 4) | Validate performance, localization, and deployment | 100k-record stress tests; EN/ES/CA bundles; Docker image; ≥ 80% coverage | NFR targets met; release approved |
| 5. Release & Deployment | Put the service into production | Production deployment; health checks; monitoring; handover | Production go-live accepted |
| 6. Operate & Support | Sustain the service | Incident handling, performance monitoring, defect fixes, minor enhancements | Ongoing; reviewed annually |
| 7. Retirement | Decommission when superseded | Data archival per retention policy; service shutdown; consumer migration | Retirement approved; data archived |

## 8. Risks and Assumptions

### 8.1 Assumptions

* The SKU master data is provided by an external, validated source (per brief, out of scope).
* Physical hardware drivers (printers, scanners) are handled outside this project.
* JWT/OAuth2 identity provider is available or will be provided by the organization.
* The target runtime is Docker-based, deployable to Kubernetes or a cloud environment.
* The 100k-record scale represents the expected data volume for the planning horizon (to be confirmed).
* Current-state process, system, and cost baseline data exist and can be collected.

### 8.2 Risks

| Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- |
| Concurrency defects in split/merge/group operations | Medium | High | Atomic transactions, optimistic locking, concurrency tests in CI |
| Performance targets not met at 100k records | Medium | High | Early performance testing; index strategy in Milestone 1 |
| Scope creep into ERP integration or hardware drivers | Medium | Medium | Enforce out-of-scope list through change control |
| Missing ROI baseline data delays funding | High | Medium | Collect baseline metrics before Milestone 2 |
| Localization quality for ES and CA | Low | Low | Professional translation review |
| Security gaps in RBAC or error masking | Low | High | Security review at Milestone 1 and 4 |

## 9. Recommendation

Proceed to Milestone 1 (Foundation) under the hybrid approach, subject to:

1. Sponsor confirmation of the business case and named project manager.
2. Collection of current-state baseline metrics and cost estimates.
3. A documented evaluation of Option B (commercial WMS) to confirm the build decision.

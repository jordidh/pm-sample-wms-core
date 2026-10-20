# Stakeholder Register: Warehouse Inventory & Storage Management REST API (WMS Core)

| Field | Value |
| --- | --- |
| Document | Stakeholder Register (including RACI Matrix and Communication Plan) |
| Project Name | Warehouse Inventory & Storage Management REST API (WMS Core) |
| Current Version | 1.0 |
| Date | 2026-10-20 |

## Changelog

| Version | Date | Change Description |
| --- | --- | --- |
| 1.0 | 2026-10-20 | Initial version |

## 1. Purpose and Basis

This register identifies the internal and external stakeholders of WMS Core, assesses their power, influence, and interest, defines the RACI governance model for key decisions and deliverables, and sets the communication plan. It is based on the Business Case v1.0 and Project Charter v1.0 dated 2026-10-20.

**Assumptions (no source data provided for these items):**
* Sponsor, Project Manager, Business Owner, and Technical Lead are named in the charter as <UNKNOWN>. Roles are used as identifiers until names are confirmed.
* Team composition, budget, and milestone dates are not provided (<UNKNOWN>).
* Current-state processes, warehouse operators, and existing systems are not described in the brief; stakeholder groups are inferred from the requirements (RBAC roles, integration goals, compliance and security scope).
* Identity provider owner (JWT/OAuth2) is assumed to be available or supplied by the organization (per charter assumption).
* Future ERP integration consumers are out of scope for this phase and are listed as external stakeholders for planning only.
* Current and desired engagement levels are assessed from the documents and should be validated with the sponsor.

## 2. Stakeholder Register

Engagement scale: **Unaware** < **Resistant** < **Neutral** < **Supportive** < **Leading**.
Power and Influence / Interest: H = High, M = Medium, L = Low.

| # | Stakeholder (Name / Group) | Type | Role in Project | Organization | Key Expectations | Power | Influence | Interest | Current Engagement | Desired Engagement | Classification |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| S-01 | Project Sponsor (<UNKNOWN>) | Internal | Approves business case, charter, strategic changes, funding, milestone gates, cross-functional escalations | Executive leadership | Traceability and compliance outcomes; controlled spend; on-time milestones; clear ROI | H | H | M | Neutral (assumed) | Leading | Manage Closely |
| S-02 | Project Manager (<UNKNOWN>) | Internal | Day-to-day delivery, schedule, resources, risk and issue logs, minor changes | Delivery / PMO | Approved scope and budget; realistic milestone dates; timely baselines; clear authority limits | M | H | H | Supportive (assumed) | Leading | Manage Closely |
| S-03 | Business Owner (<UNKNOWN>) | Internal | Owns business outcomes, approves operational rules and acceptance of functional behaviour | Warehouse Operations | Accurate inventory; reduced discrepancies and reconciliation effort; usable location and stock rules | H | H | H | Neutral (assumed) | Leading | Manage Closely |
| S-04 | Technical Lead (<UNKNOWN>) | Internal | Architecture decisions, data model, concurrency design, security design, technical risk | Engineering | Stable baselines before iteration; testable NFRs; feasible transactional design | M | H | H | Supportive (assumed) | Leading | Manage Closely |
| S-05 | Development Team (<UNKNOWN> members) | Internal | Builds modules, tests, CI/CD, documentation | Engineering | Clear acceptance criteria; stable requirements; sustainable pace; tooling | L | M | H | Supportive (assumed) | Leading | Keep Informed |
| S-06 | QA / Test Lead (<UNKNOWN>) | Internal | Functional, concurrency, stress (100k records), and coverage validation | Quality Assurance | Testable requirements; test environments with 100k data; defect triage access | L | M | H | Neutral (assumed) | Supportive | Keep Informed |
| S-07 | Security & Compliance Owner (<UNKNOWN>) | Internal | Reviews RBAC, error masking, audit trail at M1 and M4 gates | Information Security / Compliance | No stack traces or schema leaks; RBAC boundaries; immutable audit log | H | H | M | Unaware (assumed) | Supportive | Keep Satisfied |
| S-08 | DevOps / Platform Engineer (<UNKNOWN>) | Internal | CI/CD pipeline, Docker image, Kubernetes/cloud deployment, health checks, observability | Platform Engineering | Container standards; health endpoints; structured JSON logs; trace_id support | M | M | M | Neutral (assumed) | Supportive | Keep Informed |
| S-09 | Warehouse Supervisors (role: `supervisor`) | Internal (end user) | Approve adjustments, blocks, quarantines; oversee stock operations | Warehouse Operations | Fast, reliable split/merge/group; clear reason codes; visibility into movements | M | M | H | Unaware (assumed) | Supportive | Keep Informed |
| S-10 | Warehouse Operators (role: `operator`) | Internal (end user) | Perform moves, stock receipts, grouping, picking-related operations | Warehouse Operations | Simple workflows; clear RFC 7807 error messages in their language (EN/ES/CA); minimal friction | L | M | H | Unaware (assumed) | Supportive | Keep Informed |
| S-11 | Warehouse Administrators (role: `admin`) | Internal (end user) | Manage locations, containers, users and roles, configuration | Warehouse Operations / IT | Full control of location tree; reliable RBAC; audit visibility | M | H | H | Unaware (assumed) | Supportive | Manage Closely |
| S-12 | Inventory Control / Auditors | Internal | Review movement log, reconcile discrepancies, verify write-offs and reason codes | Finance / Internal Audit | Immutable, complete movement log; filterable audit data; traceable reason codes | M | M | M | Unaware (assumed) | Supportive | Keep Satisfied |
| S-13 | Identity Provider Operator | External or Internal (<UNKNOWN>) | Provides JWT/OAuth2 tokens and claims for roles | IT Identity / Vendor | Clear client and role-claim contract; integration timeline | M | M | L | Unaware (assumed) | Neutral | Monitor |
| S-14 | Localization / Translation Reviewers | External or Internal (<UNKNOWN>) | Validate EN, ES, and CA message bundles | Translation vendor / Linguistic review | Stable message keys; context for error messages; review time | L | L | M | Unaware (assumed) | Supportive | Monitor |
| S-15 | Downstream Integration Consumers (future ERP) | External | Consume REST API, OpenAPI spec, RFC 7807 errors; future PO receiving and sales shipping | Future ERP / consumer systems (<UNKNOWN>) | Stable contracts; versioning; standard errors; documentation | M | M | M | Unaware (assumed) | Supportive | Keep Satisfied |
| S-16 | SKU Master Data Provider | External | Supplies validated SKU string references (out of scope for WMS Core) | Product master data owner (<UNKNOWN>) | Clear SKU reference contract; no master data ownership by WMS Core | L | M | L | Unaware (assumed) | Neutral | Monitor |
| S-17 | Hardware / Device Vendors (printers, scanners) | External | Out of scope; interface assumed to be handled outside project | <UNKNOWN> | Not applicable within this phase | L | L | L | Unaware (assumed) | Neutral | Monitor |
| S-18 | Infrastructure / Cloud Hosting Provider | External | Hosts Docker and Kubernetes environments | <UNKNOWN> | Service quotas; security baseline; availability | M | L | L | Unaware (assumed) | Neutral | Monitor |
| S-19 | Operations / Support Team (post go-live) | Internal | Runs the service, incident handling, performance monitoring (Phase 6) | IT Operations | Runbooks; health checks; observability; handover | M | M | M | Unaware (assumed) | Supportive | Keep Satisfied |
| S-20 | Finance / Budget Approver | Internal | Releases funding; validates cost and ROI baselines | Finance | Cost estimates; ROI baseline; funding gates | H | M | L | Unaware (assumed) | Neutral | Keep Satisfied |

### 2.1 Power/Interest Classification Summary

| Quadrant | Strategy | Stakeholders |
| --- | --- | --- |
| High Power / High Interest | Manage Closely | S-01, S-02, S-03, S-04, S-07 (high power, medium interest), S-11 |
| High Power / Low Interest | Keep Satisfied | S-20 |
| Low Power / High Interest | Keep Informed | S-05, S-06, S-08, S-09, S-10 |
| Low Power / Low Interest | Monitor | S-13, S-14, S-16, S-17, S-18 |
| Medium Power/Influence, Medium Interest | Keep Satisfied or Keep Informed (by context) | S-12, S-15, S-19 |

Note: S-07 (Security & Compliance) is classified as Keep Satisfied because of high power and medium interest, but security gate reviews at M1 and M4 require active engagement; treat as Manage Closely during gate reviews.

## 3. RACI Governance Matrix

**Legend:** R = Responsible (does the work); A = Accountable (single owner who signs off); C = Consulted (two-way input before decision); I = Informed (one-way update after decision).

**Roles used as columns:** SPN = Project Sponsor; PM = Project Manager; BO = Business Owner; TL = Technical Lead; DEV = Development Team; QA = QA / Test Lead; SEC = Security & Compliance Owner; OPS = DevOps / Platform Engineer; SUP = Warehouse Supervisors; FIN = Finance / Budget Approver; CON = Downstream Integration Consumers.

### 3.1 Key Decisions

| Decision / Activity | SPN | PM | BO | TL | DEV | QA | SEC | OPS | SUP | FIN | CON |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Approve business case and project charter | A | C | C | C | I | I | I | I | I | C | I |
| Approve strategic objective changes (major version) | A | C | C | C | I | I | I | I | I | C | I |
| Approve minor scope refinements (minor version) | I | A | C | C | I | I | I | I | I | I | I |
| Approve funding releases and budget thresholds | A | R | C | C | I | I | I | I | I | R | I |
| Milestone gate decision M1 (Foundation) | A | R | C | R | C | C | C | C | I | I | I |
| Milestone gate decision M2 (Hierarchy engines) | A | R | C | R | C | C | I | I | C | I | I |
| Milestone gate decision M3 (Stock operations) | A | R | C | R | C | C | I | I | C | I | I |
| Milestone gate decision M4 (Production readiness) | A | R | R | R | C | R | R | R | C | I | C |
| Approve business rules for stock status, reason codes, and write-off policy | I | I | A | C | C | C | C | I | C | I | C |
| Approve architecture and data model (transactions, optimistic locking) | I | I | C | A | C | C | C | C | I | I | C |
| Define RBAC roles and permission boundaries | I | I | C | A | C | I | R | I | C | I | C |
| Approve error contract and error code catalog (RFC 7807) | I | I | C | A | C | C | C | I | I | I | C |
| Approve third-party or ERP integration interface contract | I | C | C | A | C | C | C | I | I | I | R |
| Change control for out-of-scope items (e.g., ERP, hardware drivers) | A | R | C | C | I | I | I | I | I | C | I |
| Risk and issue log maintenance | I | A | C | C | R | R | C | C | I | I | I |
| Security review at M1 and M4 | I | I | I | C | C | C | A | C | I | I | I |
| Production go-live approval | A | R | C | R | C | C | C | R | I | I | I |
| Choose hosting and deployment target (Kubernetes/cloud) | A | C | I | R | I | I | C | R | I | C | I |
| Collect current-state baseline metrics (discrepancy rate, reconciliation hours, write-off value) | C | A | R | C | I | I | I | I | C | C | I |
| Name and provide identity provider (JWT/OAuth2) | C | A | I | R | I | I | C | C | I | I | I |

### 3.2 Deliverables

| Deliverable | SPN | PM | BO | TL | DEV | QA | SEC | OPS | SUP | FIN | CON |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Business Case and Project Charter | A | R | C | C | I | I | I | I | I | C | I |
| Stakeholder Register and Team Charter | I | A | C | C | C | C | C | C | I | I | I |
| Milestone 1: DB schema, auth/RBAC middleware, RFC 7807 handler, OpenAPI skeleton, CI/CD | I | A | I | R | R | C | R | R | I | I | C |
| Milestone 2: Location and container engines (CRUD, nested moves, capacity) | I | A | C | R | R | R | I | I | C | I | I |
| Milestone 3: Stock allocations, atomic split/merge/group, movement log | I | A | C | R | R | R | I | I | C | I | I |
| Milestone 4: 100k stress tests, EN/ES/CA bundles, Docker image, ≥80% coverage | I | A | R | R | R | R | R | R | C | I | C |
| OpenAPI 3 specification and Swagger UI | I | A | I | R | R | C | I | I | I | I | C |
| Movement log and audit reports | I | A | R | C | R | C | C | I | C | I | I |
| Operational runbooks and handover | I | A | C | C | C | I | I | R | C | I | I |
| Test coverage report and CI pipeline results | I | A | I | C | R | R | I | R | I | I | I |
| Localized message bundles (EN, ES, CA) | I | A | C | C | R | C | I | I | C | I | I |

**RACI rule check:** Each row has exactly one A. Where multiple R entries exist, the accountable party must confirm completion. Gate decisions (M1–M4) are accountable to the Sponsor per the charter.

## 4. Communication Plan

| # | Audience | Information | Cadence | Channel | Owner (Responsible) |
| --- | --- | --- | --- | --- | --- |
| C-01 | Project Sponsor | Status summary, RAID highlights, funding and gate decisions, escalations | Monthly plus at each milestone gate | Steering committee meeting and written status report | Project Manager |
| C-02 | Steering Committee (SPN, PM, BO, TL, FIN) | Milestone progress, budget vs. plan, risks, change requests, gate readiness | Monthly (assumed) and at gates | Steering committee meeting; decision log | Project Manager |
| C-03 | Business Owner and Warehouse Ops representatives | Functional progress, business rule decisions, UAT scheduling | Bi-weekly (aligned to iteration review) | Iteration review / demo; written summary | Business Owner |
| C-04 | Development and QA team | Backlog, acceptance criteria, defects, CI status | Daily stand-up; per iteration planning | Team stand-up; issue tracker; CI dashboard | Technical Lead |
| C-05 | Security & Compliance Owner | Security design, RBAC matrix, error masking evidence | At M1 and M4 gates; ad hoc on architecture changes | Security review session; written findings | Technical Lead |
| C-06 | DevOps / Platform Engineering | Environment needs, deployment plan, health checks, observability | Weekly; before each release candidate | Platform sync; deployment checklist | Technical Lead |
| C-07 | Warehouse Supervisors and Operators | Demonstrations of stock operations, role changes, reason code usage, training notices | Each iteration demo; training before go-live | Live demo; release notes; training sessions | Business Owner |
| C-08 | Warehouse Administrators | Configuration changes, role provisioning, location tree changes | Per release; ad hoc on change | Release notes; admin briefing | Business Owner |
| C-09 | Finance / Budget Approver | Cost actuals, forecast, funding requests, ROI baseline status | Monthly | Finance report | Project Manager |
| C-10 | Downstream Integration Consumers | API changes, OpenAPI version, deprecations, error catalog updates | Per release; at least 30 days notice of breaking changes (assumed) | Developer portal / changelog; OpenAPI publication | Technical Lead |
| C-11 | Identity Provider Operator | Client registration, role claim mapping, token contract | Before M1 integration testing; ad hoc on change | Integration meeting; written contract | Technical Lead |
| C-12 | Localization Reviewers | Message keys, context, review schedule | Per M4 bundle cycle | Shared translation file; review session | QA / Test Lead |
| C-13 | Operations / Support (post go-live) | Runbooks, monitoring, incident process, handover | Before go-live; weekly during hypercare (assumed) | Handover session; runbook repository | Technical Lead |
| C-14 | Internal Audit / Inventory Control | Movement log access, audit reports, reason code review | Quarterly (assumed) and on request | Audit report; read access to movement log | Business Owner |
| C-15 | Project-wide | Changes to charter, register, or charter norms | As changes occur | Document repository; changelog | Project Manager |

## 5. Engagement Actions and Open Items

| # | Action | Owner | Due |
| --- | --- | --- | --- |
| A-01 | Confirm names for Sponsor, Project Manager, Business Owner, and Technical Lead | Project Sponsor | Before M1 start |
| A-02 | Confirm authority limits (budget thresholds, approval ceilings) | Project Sponsor | Before M1 start |
| A-03 | Identify consumer systems and integration partners (charter assumption) | Technical Lead | During M1 |
| A-04 | Confirm owner of JWT/OAuth2 identity provider | Project Manager | Before M1 integration testing |
| A-05 | Collect current-state baseline metrics (discrepancy rate, reconciliation hours, write-off value) | Business Owner | Before M2 funding |
| A-06 | Confirm steering committee membership and cadence | Project Manager | Before M1 start |
| A-07 | Confirm hosting target and infrastructure ownership | Technical Lead | During M1 |
| A-08 | Confirm localization reviewer for ES and CA | QA / Test Lead | Before M4 |
| A-09 | Confirm milestone target dates (currently <UNKNOWN>) | Project Manager | Before M2 |
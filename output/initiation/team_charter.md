# Team Charter: Warehouse Inventory & Storage Management REST API (WMS Core)

| Field | Value |
| --- | --- |
| Document | Team Charter |
| Project Name | Warehouse Inventory & Storage Management REST API (WMS Core) |
| Current Version | 1.0 |
| Date | 2026-10-20 |

## Changelog

| Version | Date | Change Description |
| --- | --- | --- |
| 1.0 | 2026-10-20 | Initial version |

## 1. Purpose

This charter defines how the WMS Core delivery team works together: values, working norms, meeting cadence, decision-making, communication, conflict resolution, quality expectations, and review. It operates within the Business Case and Project Charter v1.0 and the Stakeholder Register v1.0.

**Assumptions:**
* Team members and their names are not provided (<UNKNOWN>). Roles are referenced as in the Stakeholder Register.
* Team size and location/time-zone are not provided. Synchronous ceremonies assume a shared working window (assumed).
* Iteration length is assumed to be two weeks, per the Project Charter (to be confirmed by the Project Manager).

## 2. Team Values

1. **Traceability first:** Every stock change must be explainable, recorded, and reviewable.
2. **Accuracy over speed:** No shortcut that risks negative inventory, orphaned containers, or race conditions.
3. **Transparency:** Blockers, risks, and disagreements are raised early and in writing.
4. **Shared ownership:** Anyone can raise a defect or a design concern; the team owns quality, not only QA.
5. **Consumer empathy:** The API is a product for integrators and operators; clear contracts and clear errors matter.
6. **Respect and psychological safety:** Challenge ideas, not people.

## 3. Working Norms and Operating Agreements

### 3.1 Ways of Working
* Hybrid delivery: fixed contracts and milestone gates (M1–M4); adaptive delivery within each milestone.
* Work is tracked in a single backlog; every item links to a requirement ID (REQ-001 – REQ-038) or a documented change.
* Work-in-progress limits are agreed by the Technical Lead and reviewed each iteration (assumed).
* Definition of Ready is applied before an item enters an iteration (see Section 8).

### 3.2 Contracts and Baselines
* Data model, transactional rules, RBAC boundaries, and error contract are baselined at M1 and changed only through change control.
* Changes to the error code catalog (e.g., `LOCATION_NOT_EMPTY`, `STOCK_INSUFFICIENT_QUANTITY`) require Technical Lead approval and a changelog entry.
* No change to strategic objectives without a formal change request and Sponsor approval (major version).

### 3.3 Code and Collaboration
* Trunk-based development with short-lived branches (assumed); all changes via reviewed pull requests.
* At least one peer review per change; security-relevant changes (auth, RBAC, error masking, audit log) require Security & Compliance Owner review.
* No secrets, stack traces, or internal schema details in responses, logs visible externally, or commits.
* Pipeline must be green before merge.

### 3.4 Working Agreements
* Respect agreed working hours and time zones (to be confirmed).
* Protect focus time; use async channels by default unless the issue is blocking.
* Document decisions in the decision log, not only in chat.
* Leave the codebase and documentation in a better state than found.

## 4. Meeting Cadence and Ceremonies

| Ceremony | Frequency | Duration (assumed) | Participants | Purpose | Facilitator |
| --- | --- | --- | --- | --- | --- |
| Daily stand-up | Daily | 15 min | Development, QA, DevOps, Technical Lead | Progress, blockers, same-day plan | Technical Lead (or rotating developer) |
| Iteration planning | Start of each 2-week iteration | 2 hours | Team, Project Manager, Business Owner (for scope) | Commit to iteration goal and backlog items meeting Definition of Ready | Project Manager |
| Backlog refinement | Weekly | 60 min | Technical Lead, Development, QA, Business Owner (as needed) | Clarify acceptance criteria, split stories, analyze edge cases (split/merge/group) | Technical Lead |
| Iteration review / demo | End of each iteration | 60 min | Team, Business Owner, Warehouse Supervisors, Sponsor (optional) | Demonstrate working increments; collect feedback | Business Owner |
| Iteration retrospective | End of each iteration | 45 min | Team and Project Manager | Improve process; agree 1–2 improvement actions | Project Manager |
| Architecture and design review | As needed, minimum at M1 | 60 min | Technical Lead, Development, DevOps, Security | Review schema, concurrency design, error contract | Technical Lead |
| Milestone gate review | End of M1, M2, M3, M4 | 90 min | Steering committee, Team, Security (M1, M4) | Gate decision against exit criteria | Project Manager |
| Steering committee | Monthly (assumed) | 60 min | Sponsor, PM, Business Owner, Technical Lead, Finance | Status, risks, changes, funding | Project Manager |
| Risk and issue review | Weekly | 30 min | Project Manager, Technical Lead, Business Owner | Review RAID log | Project Manager |

## 5. Decision-Making Process

Decisions follow the RACI matrix in the Stakeholder Register (v1.0).

| Decision Type | Decision Owner (Accountable) | Process |
| --- | --- | --- |
| Technical design within a baselined contract (e.g., internal implementation) | Technical Lead | Propose in design review or PR; consult team; decide within 2 working days (assumed) |
| Business rule interpretation (stock status, reason codes, write-off policy) | Business Owner | Raise question in refinement; document decision; update acceptance criteria |
| Scope refinement (minor version) | Project Manager | Written proposal; consult Business Owner and Technical Lead; record in changelog |
| Strategic objective change (major version) | Project Sponsor | Formal change request; sponsor approval; charter re-baselined |
| Milestone gate | Project Sponsor | Gate review against exit criteria; go/no-go recorded |
| Security-relevant design | Technical Lead, with Security & Compliance Owner review | Security review before merge; findings must be closed or formally accepted |

**Escalation path:** Team member → Technical Lead or Business Owner (within 1 working day) → Project Manager (within 2 working days) → Project Sponsor (for scope, budget, or strategic conflict).

**Decision log:** Every decision is recorded with date, owner, rationale, and affected requirement IDs.

## 6. Communication Channels and Response Expectations

| Channel | Use For | Response Expectation (assumed) |
| --- | --- | --- |
| Team chat (channel) | Day-to-day questions, quick clarifications | Within 4 working hours during working window |
| Issue tracker | Work items, defects, acceptance criteria, decisions requiring tracking | Triage within 1 working day |
| Pull request review | Code and design changes | First review within 1 working day |
| Document repository | Charter, register, changelogs, specifications | Read before related meetings |
| Email | External stakeholders, formal notices | Within 2 working days |
| Incident channel | Production or CI-breaking issues | Acknowledge within 30 minutes during hours of operation (assumed) |
| Steering and gate reports | Sponsor and steering decisions | Per the cadence in the Stakeholder Register |

**Rules:**
* Blocking issues are raised in the stand-up and in the issue tracker the same day.
* Urgent production or security issues go to the incident channel, not to direct messages.
* Decisions made in chat are restated in the issue tracker within 1 working day.

## 7. Conflict Resolution

1. **Address directly and early:** Discuss the issue one-to-one, focusing on facts and the requirement.
2. **Structured discussion:** If unresolved, bring it to the Technical Lead or Business Owner in a short, documented discussion.
3. **Decision by the accountable owner:** Use the RACI owner for the decision type (Section 5).
4. **Escalation:** Unresolved conflicts go to the Project Manager, then the Project Sponsor for scope, budget, or strategic issues.
5. **Record:** Record the outcome in the decision log; review at the next retrospective if the pattern repeats.

Disagreements about design are settled by the Technical Lead after considering input; once decided, the team commits to the decision and may revisit it with new evidence at the next design review.

## 8. Definition of Ready and Definition of Done / Quality Expectations

### 8.1 Definition of Ready
* Requirement ID(s) linked and acceptance criteria written, including error codes and RFC 7807 responses where applicable.
* Business rules for edge cases (e.g., split, merge, group, ungroup, cyclic move, over-capacity) are defined.
* Dependencies identified (identity provider, test data, environments).
* Sized by the team and fits within the iteration.

### 8.2 Definition of Done
* Code is implemented, peer reviewed, and merged; CI pipeline is green.
* Automated tests cover the acceptance criteria, including negative and concurrency cases for stock operations.
* Overall automated test coverage stays at or above 80%.
* Optimistic locking (ETag/version) and transactional rollback are verified for multi-record operations.
* RFC 7807 responses are returned with machine-readable error codes; 5xx responses are masked and include `trace_id`.
* Movement log entries are created for every stock mutation and verified.
* RBAC enforced for `admin`, `supervisor`, and `operator` roles.
* OpenAPI 3 specification updated and published in Swagger UI.
* Structured JSON logs and health check endpoints available for the affected component.
* Localization keys added for EN, ES, and CA (where user-facing messages change).
* Documentation and changelog updated.
* Demonstrated at the iteration review.

### 8.3 Quality Expectations
* Performance: p95 < 300 ms single-item reads; < 1 s list and aggregate queries over 100k records (validated at M4 and tracked earlier).
* Zero negative-inventory, over-capacity, or orphaned-container records in acceptance testing.
* Security reviews at M1 and M4 are completed before gate approval.

## 9. Team Health and Continuous Improvement

* Retrospectives track one or two improvement actions per iteration and review previous actions.
* Workload concerns are raised at the retrospective or directly with the Project Manager.
* Knowledge sharing: pair or mob sessions for stock operation logic (split, merge, group) to reduce single-person dependency (assumed).

## 10. Charter Review and Maintenance

* **Owner:** Project Manager, with the Technical Lead as co-owner.
* **Review cadence:** At each milestone gate (M1–M4) and at least once per quarter (assumed).
* **Triggers for ad hoc review:** Repeated conflict pattern, missed commitments, new team members, change to the delivery approach, or a major change request.
* **Change process:** Proposed change is posted to the issue tracker; team consensus is sought; Project Manager approves minor refinements (minor version), and Sponsor approval is required if the charter changes strategic objectives (major version).
* **Versioning:** Changes are recorded in the changelog with version, date, and description.

## 11. Acceptance

| Role | Name | Signature | Date |
| --- | --- | --- | --- |
| Project Manager | <UNKNOWN> | | |
| Technical Lead | <UNKNOWN> | | |
| Business Owner | <UNKNOWN> | | |
| Development Team Representative | <UNKNOWN> | | |
| QA / Test Lead | <UNKNOWN> | | |
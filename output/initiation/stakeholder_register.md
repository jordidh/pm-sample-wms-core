# Stakeholder Register: Warehouse Inventory & Storage Management REST API (WMS Core)

## Document Control
- **Project Name:** Warehouse Inventory & Storage Management REST API (WMS Core)
- **Current Version:** 1.0
- **Date:** 2026-10-17

## Changelog
| Version | Date | Change Description |
|---|---|---||
| 1.0 | 2026-10-17 | Initial version |

## 1. Stakeholder Analysis
| Stakeholder Group / Name | Role / Title | Organization | Expectations | Power | Influence | Interest | Current Engagement | Desired Engagement |
|---|---|---|---|---|---|---|---|---|
| Executive Sponsor | VP of Supply Chain / Engineering | Internal Operations | On-time delivery within scope, high ROI, operational risk reduction. | High | High | High | Neutral | Supportive |
| Warehouse Operations Lead | Director of Warehouse Operations | Internal Business | Accurate stock tracking, no negative inventory, reliable capacity limits. | Medium | High | High | Resistant | Supportive |
| Lead Software Engineer / Architect | Technical Lead | Engineering Team | Clean architecture, high test coverage, robust ACID transactions. | Medium | High | High | Supportive | Leading |
| External Integration Partners | External Systems Leads | External Clients | Clear OpenAPI specs, stable REST endpoints, predictable RFC 7807 errors. | Low | Medium | High | Unaware | Informed |
| Compliance & Security Officer | Security & Audit Lead | Governance | Strict RBAC enforcement, immutable audit trails, secure error masking. | High | Medium | Medium | Neutral | Supportive |

## 2. Power/Interest Classification
- **Manage Closely (High Power, High Interest):** Executive Sponsor, Warehouse Operations Lead, Lead Software Engineer / Architect.
- **Keep Satisfied (High Power, Low Interest):** Compliance & Security Officer.
- **Keep Informed (Low Power, High Interest):** External Integration Partners.
- **Monitor (Low Power, Low Interest):** General IT Support and Infrastructure Teams.

## 3. RACI Governance Matrix
| Project Deliverable / Decision | Executive Sponsor | Warehouse Operations Lead | Lead Software Engineer | Security & Audit Officer | External Integration Partners |
|---|---|---|---|---|---|
| Project Charter & Scope Approval | A / Approver | C / Consulted | C / Consulted | I / Informed | I / Informed |
| Architectural Foundation & Database Schema | I / Informed | I / Informed | A / Approver | C / Consulted | I / Informed |
| Core Engine Implementation (Locations & Containers) | I / Informed | C / Consulted | A / Approver | I / Informed | I / Informed |
| Stock Management & Advanced Movements | I / Informed | C / Consulted | A / Approver | C / Consulted | I / Informed |
| Performance & Production Readiness Sign-off | A / Approver | C / Consulted | R / Responsible | C / Consulted | I / Informed |
| Milestone Deployment & Release | A / Approver | I / Informed | R / Responsible | I / Informed | I / Informed |

## 4. Communication Plan
| Audience | Information Provided | Cadence | Channel | Owner |
|---|---|---|---|---|
| Executive Sponsor | Milestone status, budget, key risks, ROI metrics | Bi-weekly / Milestone completion | Executive Summary Report / Meeting | Project Manager |
| Warehouse Operations Lead | Functional review, business rule validation, demo | Weekly | Working Session / Demo | Project Manager / Tech Lead |
| Engineering Team | Sprint backlog, technical hurdles, CI/CD metrics | Daily / Iterative | Standup / Jira / GitHub | Lead Software Engineer |
| External Integration Partners | OpenAPI specification updates, migration guides | Major version releases | Developer Portal / Email Broadcast | Technical Writer / API Lead |
| Compliance & Security Officer | Security audit reports, RBAC reviews, error masking | Monthly / Pre-release | Compliance Review Meeting | Security Officer / Tech Lead |
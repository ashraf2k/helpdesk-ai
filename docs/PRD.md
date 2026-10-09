# HelpDesk AI — Product Requirements Document (PRD)

| | |
|---|---|
| **Owner** | Ashraf Elbialy (AI Engineer) |
| **Status** | Draft v0.1 |
| **Last updated** | October 2026 |
| **Target releases** | v0.1 (Oct 2026) → v2.0 (May 2027) |

---

## 1. Problem

Employees in a Saudi organisation raise IT and policy questions every day. Today:

- **Slow answers:** employees wait hours for simple answers ("How do I reset my VPN?", "Can I share customer data with a vendor?").
- **Manual routing:** help-desk agents read every ticket to decide its priority and which team should handle it.
- **Inconsistent policy answers:** answers about data-protection (PDPL) and cybersecurity (NCA ECC) rules depend on who replies, and are rarely backed by a source.
- **No visibility:** IT managers can't easily see ticket volumes, bottlenecks or cost.

## 2. Goal

Build an AI service desk that answers routine questions with cited sources, triages and routes the rest automatically, and gives managers clear metrics — while respecting Saudi data-protection and cybersecurity rules.

## 3. Users (personas)

| Persona | Who | Needs | Pain today |
|---|---|---|---|
| **Sara — Employee** | Finance analyst, writes in Arabic and English | Fast, correct answers she can trust | Waits hours; answers have no source |
| **Khalid — Help-desk agent** | Handles 60+ tickets a day | Tickets already prioritised and routed; draft replies | Spends time sorting tickets by hand |
| **Noura — IT manager** | Owns the help-desk budget and compliance | Dashboards, costs, audit trail, PDPL compliance | No single view of volume, quality or cost |

## 4. User stories and acceptance criteria

| ID | User story | Accepted when | Module / release |
|---|---|---|---|
| US-01 | As **Noura**, I want a dashboard of tickets by team, priority, type and language, so I can see where the load is. | Dashboard shows the 4 breakdowns with filters; loads in < 3 s; data comes from the database. | Data + dashboard / v0.1 |
| US-02 | As **Khalid**, I want every new ticket to get a predicted priority, so I can handle urgent ones first. | Each ticket shows a priority and confidence; macro-F1 on the test set meets the KPI target. | Triage / v0.2 |
| US-03 | As **Khalid**, I want tickets routed to the right team automatically, so I stop sorting them by hand. | Each ticket shows a predicted team; routing accuracy meets the KPI target; Arabic tickets are handled. | Triage / v0.3 |
| US-04 | As **Sara**, I want to ask a policy question in Arabic or English and get an answer with its source, so I can trust it. | Every answer cites document + article/page; same quality in Arabic and English. | Assistant / v1.0 |
| US-05 | As **Sara**, I want the assistant to say "I don't know" when it has no reliable source, so I'm never misled. | Below the confidence threshold, the assistant refuses and offers to open a ticket. | Assistant / v1.0 |
| US-06 | As **Sara**, I want the assistant to open a ticket for me when it can't solve my problem, so I don't have to fill in a form. | Ticket created only after I confirm; it has the right priority and team; it appears on the dashboard. | Agent + MCP / v1.0 |
| US-07 | As **Noura**, I want personal data (IDs, phone numbers, emails) masked before AI processing, so we comply with PDPL. | Saudi ID/Iqama, phone and email patterns are masked in logs, prompts and stored answers. | Assistant / v1.0 |
| US-08 | As **Noura**, I want an audit log of every question, tool call and answer, so we can investigate issues. | Each request is logged with user role, sources used, tool calls and answer. | Assistant / v1.0 |
| US-09 | As **Khalid**, I want a suggested reply drafted in Arabic or English, so I answer faster. | Draft reply generated in the ticket's language; agent can edit before sending. | LLM / v1.5 |
| US-10 | As **Noura**, I want each team to see only the knowledge it is allowed to see, and staff to sign in with company accounts. | Sign-in via Microsoft Entra ID; retrieval filtered by team group. | Platform / v2.0 |
| US-11 | As **Noura**, I want monitoring of uptime, speed, cost per answer and answer quality, so I can run the service reliably. | Dashboards and alerts for the SLOs below; cost per answer tracked. | Platform / v2.0 |

## 5. Success metrics (KPIs)

| KPI | Target | Measured from |
|---|---|---|
| Routing accuracy (team) | ≥ 85% | Week 11–12 test set |
| Priority prediction (macro-F1) | ≥ 0.70 | Week 8–9 test set |
| Hallucination rate (unsupported claims) | ≤ 5% | Week 18 evaluation on the gold set |
| Self-service resolution (answered without a ticket) | ≥ 30% of questions | Week 22 user test |
| p95 answer time | < 3 seconds | Week 31 load test |
| Cost per answer | Tracked; target set after Week 31 | LLM tracing |
| User satisfaction (UAT rating) | ≥ 4 / 5 | Week 22 user acceptance test |

*Targets are first estimates. Week 34's post-launch review compares actual results with these targets.*

## 6. Non-functional requirements

| Area | Requirement |
|---|---|
| **Language** | Arabic and English, including right-to-left layout for Arabic. |
| **Data protection (PDPL)** | Collect only the data needed; mask personal data; define retention (e.g. logs kept 90 days); host in a Saudi region (Azure Saudi Arabia East) or document a transfer risk assessment. |
| **Cybersecurity (NCA ECC)** | Role-based access, audit logging, vulnerability scanning in CI, protected branches and code review. |
| **Responsible AI (SDAIA principles)** | Answers cite sources; users are told they're talking to AI; a human confirms before a ticket is created; model and data cards published. |
| **Performance** | p95 answer time < 3 s; dashboard loads < 3 s. |
| **Availability** | 99.5% monthly (production). |
| **Cost** | Azure budget alerts; token cost per answer tracked. |

## 7. Out of scope (for v1.0–v2.0)

- Voice input and mobile apps
- Integration with a real company ticketing system (a mock ticket store is used)
- Languages other than Arabic and English
- Real employee or customer data (public and synthetic data only)

## 8. Data and knowledge sources

- **Tickets:** public customer-support tickets dataset (61.8k tickets; Hugging Face, non-commercial licence)
- **Policies:** SDAIA Personal Data Protection Law + guides; NCA Essential Cybersecurity Controls (Arabic + English)
- **Second knowledge base (v2.0):** NIST AI Risk Management Framework

## 9. Milestones

| Release | Week | What ships |
|---|---|---|
| v0.1 | 4 | Clean ticket data + ops dashboard, Docker, CI, Azure deploy |
| v0.2 | 9 | Priority prediction API |
| v0.3 | 12 | Team router (transformer) in the triage API |
| v1.0 | 22 | RAG assistant with citations, agent, MCP server, user acceptance test |
| v1.5 | 28 | Fine-tuned Arabic reply model |
| v2.0 | 31 | Whole system on Kubernetes with monitoring, SSO and per-team access |

## 10. Risks and assumptions

| Risk | Mitigation |
|---|---|
| Ticket dataset is English/German, not Arabic | Arabic comes from policy documents and a translated test set |
| LLM answers may hallucinate | Citations, refusal threshold, self-check, eval gate in CI |
| Cloud cost exceeds free credits | Budget alerts; stop AKS when not in use; small models where possible |
| 8 hours a week is limited | Fixed scope per release; out-of-scope list above |

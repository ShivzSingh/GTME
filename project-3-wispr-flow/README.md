# Project 3 — Wispr Flow Outbound Using Claude Code

**Company:** Wispr Flow
**Type:** End-to-end AI-orchestrated outbound pipeline
**Tools:** Deepline (Crustdata V3 + FullEnrich), Lemlist MCP, Claude Code, Python

---

## Overview

Built a complete end-to-end outbound pipeline for Wispr Flow — a $81M-funded AI voice dictation startup — targeting CTOs at AI-native, YC-backed US companies. The entire workflow was orchestrated using **Claude Code** with zero manual data work. Sourcing, enrichment, ICP scoring, QA, and campaign activation all ran programmatically.

---

## What Wispr Flow Does

Wispr Flow is a voice-to-text layer that works across every app. You speak; it types — cleaned up, formatted, polished. No filler words, proper punctuation, structured prose. Works in Gmail, Slack, Notion, VS Code, Claude, anywhere you can type.

- **Free tier:** 2,000 words/week
- **Pro:** $15/month
- **Teams:** $12/seat/month
- **Enterprise:** Custom (SSO, audit logs, admin controls)
- Certifications: SOC 2 Type II, HIPAA, ISO 27001

---

## ICP

| Dimension | Target |
|-----------|--------|
| Geography | United States (primary) |
| Industry | Any — qualifier is high daily written output volume |
| Company | AI-native tech companies, 10–5,000 employees |
| Economic Buyer | CTO, Co-Founder & CTO, VP of Engineering |
| Buying Trigger | Writing constantly and feeling the friction — 50+ Slack messages, emails, docs per day |

---

## Tools Used

| Tool | Purpose |
|------|---------|
| Deepline (Crustdata V3) | Person search — CTO-level leads by title + country |
| Deepline (FullEnrich waterfall) | Email enrichment from LinkedIn URLs + company domains |
| Lemlist MCP | Campaign creation, sequence building, lead import |
| Claude Code | Orchestrated the full pipeline end-to-end |
| Python scripts | Combine+dedupe, ICP scoring, QA, filtering |

---

## How Deepline Sourced and Enriched the Data

### Person Search via Crustdata V3

Deepline executed a `crustdata_v3_person_search` with a structured filter group:
- **Title filter (OR):** CTO, Chief Technology Officer, VP of Engineering, VP Engineering, Technical Co-founder, Co-Founder & CTO
- **Country filter:** United States

Returned profiles with: name, current title, company, company size, LinkedIn URL, location, and email availability flag.

### Email Enrichment via FullEnrich Waterfall

Profiles without a verified email were sent to FullEnrich via Deepline's `fullenrich_bulk_enrich` tool. Each contact submitted with LinkedIn URL, first name, last name, and company domain. FullEnrich ran an async waterfall querying its provider network and returned work emails alongside enriched profile data.

---

## Pipeline Steps

1. **Source leads** — Deepline (Crustdata V3) person search, title + US country filters
2. **Enrich emails** — Deepline (FullEnrich waterfall), LinkedIn URL + domain
3. **Combine + Dedupe** — Python script, normalized columns, deduped by LinkedIn URL
4. **ICP Research** — Claude Code researched product, pricing, customers, positioning
5. **ICP Scoring** — Scored all leads for title fit, company type, geography, headcount
6. **Final QA** — Automated checks: no duplicates, no personal emails, all URLs valid → GO verdict
7. **Filter to US-only** — Removed non-US leads
8. **Build Lemlist campaign** — Created via MCP, 2-step email sequence, leads imported

---

## Sample Lead List

See [`wispr_cto_leads.csv`](./wispr_cto_leads.csv) for the full list.

| Name | Title | Company | Email | Status |
|------|-------|---------|-------|--------|
| Connor Heggie | Co-Founder, CTO | Unify | connor@unifygtm.com | HIGH_PROB |
| Joel Johnson | Technical Cofounder | LangChain | joel.johnson@langchain.dev | DELIVERABLE |
| Autumn Moulder | SVP Engineering | Cohere | autumn@cohere.com | CATCH_ALL |
| Chenlin Meng | Co-Founder & CTO | Pika Labs | chenlin@pika.art | DELIVERABLE |
| Sridhar Reddy | VP of Engineering | Harvey AI | sridhar@harvey.ai | DELIVERABLE |
| Siva G. | CTO | Harvey AI | siva@harvey.ai | DELIVERABLE |
| Ayush Pathak | Co-Founder / CTO | Induced AI | ayush@induced.ai | DELIVERABLE |

---

## QA Report

See [`wispr_icp_qa_report.txt`](./wispr_icp_qa_report.txt) for the full automated QA output.

**Verdict: GO** — 0 failures, 1 warning (3 LinkedIn-only leads with no email).

---

## Outreach Copy

### Email 1 — Dinner Invite (Day 0)

```
Subject: Dinner in NYC, Sept 5

{{firstName}},

Tanay and the Wispr Flow team are hosting a small dinner in NYC on September 5th
for a handful of technical founders and CTOs building in AI.

You're building at {{companyName}} — the kind of company we'd love to have in the room.
About 10 people, good conversation, no agenda.

Would you want to join?

{{signature}}
```

### Email 2 — Follow-up (Day 3, threads on original)

```
{{firstName}}, just bumping this up.

Dinner is Sept 5 in NYC — small group, technical founders and CTOs.
Tanay's hosting. A few seats still open.

Worth joining?

{{signature}}
```

---

## Skills Used

The `skills/` folder contains the Deepline skill files used to power the sourcing and enrichment steps. See [`skills/deepline-gtm/SKILL.md`](./skills/deepline-gtm/SKILL.md) for the full GTM skill configuration.

---

## Key Learning

> Claude Code can orchestrate an entire GTM pipeline end-to-end — sourcing, enrichment, scoring, QA, and campaign activation — with no manual data work and no Clay or n8n required.

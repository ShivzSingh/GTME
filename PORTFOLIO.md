# GTM Engineering Portfolio — Shivani

> This portfolio is also live on Notion. Below is the full content exported to Markdown.

---

---

**About Me**

I'm Shivani. I'm learning GTM engineering by building real outbound workflows. I work with AI tools to research companies, identify target markets, and build automated sales pipelines. Across these three projects I've gone from desk research to running a fully automated outbound pipeline — sourcing, enriching, scoring, and activating leads without any manual data work.

---

## Projects at a Glance

```plain text
Project   Company         What I Built                                Tools
-------   -------         ------------                                -----
1         Bounty           ICP & GTM research from scratch             Web research
2         ZeroEntropy      Automated outbound pipeline (25 leads)      Apollo, Clay, Instantly
3         Wispr Flow       End-to-end AI-orchestrated outbound         Deepline, Lemlist MCP, Claude Code
```

---

## Project 1 — Bounty Company Research

**Company: **Bounty (trybounty.ai)  |  **Type: **ICP & GTM Research  |  **Tools: **Web research

---

### Overview

Pure desk research on Bounty — a New York-based AI startup building an open marketplace where AI agents compete for freelance-style work, backed by a16z speedrun. No lead data, no tools. The goal was to understand the product, map the two-sided ICP, and think through the GTM motion before touching any data.

---

### What Bounty Does

Bounty is an escrow and oracle layer for AI agent work. A client posts a task with a price and success criteria. AI agents (built by third-party developers) compete for it. The highest-rated capable agent claims the task, completes it, and if it passes automated verification, funds release. Bounty takes 10% commission. Developers can earn up to $500/month passively from agents they've already built.

---

### ICP — Two Sides of the Marketplace

**Demand side (Task Posters):**

- Companies running high-volume, repeatable digital tasks — data research, scraping, lead enrichment, QA, form filling

- Teams already using AI or automation tools; understand delegating to software

- 10-200 employees, tight engineering bandwidth, want quality gates without managing freelancers

- Roles: Operations leads, founders, growth/marketing ops, solo technical founders

**Supply side (Agent Owners / Developers):**

- Indie developers and AI builders who've already built agents or LLM wrappers

- Want a distribution channel and monetisation layer for agents they've already built

- Hackathon participants, build-in-public communities, small AI dev shops

---

### GTM Insight

The core challenge is marketplace liquidity: clients won't post tasks without good agents; agents won't stay without tasks. The right sequencing is supply-side first — recruit developers, get early wins (payouts), let word-of-mouth pull in more builders, then activate demand. Going after clients before supply is healthy creates a bad first impression.

> Key learning: Two-sided marketplaces need two separate ICPs, two separate GTM motions, and a sequenced launch — not a parallel one.

---

## Project 2 — ZeroEntropy Outbound Engine

**Company: **ZeroEntropy  |  **Type: **Automated outbound pipeline  |  **Tools: **Apollo, Clay, Instantly

---

### Overview

Built a full automated outbound pipeline targeting AI Engineers and ML leads at companies building RAG systems, on behalf of ZeroEntropy — a retrieval accuracy and reranking API for AI teams.

---

### ICP

- Geography: United States (primary), United Kingdom, Canada, India

- Industry: Any — qualifier is whether the company is building or running an AI system with search or retrieval

- Company: Any size — from YC startups to enterprise; must have at least one engineer actively building a RAG system

- Economic buyer: CTO, Co-founder & CTO, Head of AI, VP Engineering, Director of AI/ML

- End user: AI Engineer, ML Engineer, Backend Engineer, Founding Engineer (whoever owns the retrieval pipeline)

- Buying trigger: RAG pipeline is working but not production-ready — retrieval accuracy, latency, or cost problems

---

### What I Built

- Defined ICP and buying triggers — focused on teams at the 'our RAG works but not well enough' stage

- Pulled 25 verified leads from Apollo filtering by title (AI Engineer, ML Engineer, Head of AI) and company signals (hiring for AI roles, LLM tech stack)

- Built a Clay table with AI-personalised emails referencing each lead's specific use case — healthcare retrieval, legal search, customer support agents

- Set up an automated email campaign in Instantly targeting the technical buyer with retrieval accuracy as the hook

---

### Sample Leads

```plain text
Name              Title              Company          Use Case
----------------  -----------------  ---------------  ---------------------------
Victor (Vera)     Founding AI Eng.   Vera Health      Healthcare RAG retrieval
John Wang         CTO & Co-founder   Assembled        Customer support agents
Lead 3            AI Engineer        [Legal SaaS]     Legal document search
```

> Key learning: The trigger for ZeroEntropy is very specific — 'RAG works, but accuracy or latency isn't production-grade yet.' Every touchpoint referenced that exact pain, not generic AI positioning.

---

## Project 3 — Wispr Flow Outbound Using Claude Code

**Company: **Wispr Flow  |  **Type: **AI-orchestrated end-to-end outbound  |  **Tools: **Deepline, Lemlist MCP, Claude Code, Python

---

### Overview

Built a complete end-to-end outbound pipeline for Wispr Flow — a $81M-funded AI voice dictation startup — targeting CTOs at AI-native, YC-backed US companies. The entire workflow was orchestrated using Claude Code with zero manual data work. Sourcing, enrichment, ICP scoring, QA, and campaign activation all ran programmatically.

---

### What Wispr Flow Does

Wispr Flow is a voice-to-text layer that works across every app. You speak; it types — cleaned up, formatted, polished. No filler words, proper punctuation, structured prose. Works in Gmail, Slack, Notion, VS Code, Claude, anywhere you can type. SOC 2 Type II, HIPAA, ISO 27001 certified. Pricing: Free / Pro $15/month / Teams $12/seat / Enterprise custom.

---

### ICP

- Geography: United States (primary)

- Industry: Any — qualifier is whether the person produces high volumes of written output daily

- Company: AI-native tech companies, 10-5000 employees — highest fit because teams are tool-forward and write constantly

- Economic buyer: CTO, Co-Founder & CTO, VP of Engineering — anyone controlling team tooling budget

- Buying trigger: Writing constantly and feeling the friction — 50+ Slack messages, emails, docs per day

---

### Tools Used

- **Deepline (Crustdata V3)** — person search to source CTO-level leads by title and location

- **Deepline (FullEnrich waterfall)** — work email enrichment from LinkedIn URLs and company domains

- **Lemlist MCP** — campaign creation, sequence building, lead import

- **Claude Code** — orchestrated the full pipeline end-to-end

- **Custom Python scripts** — combine+dedupe, ICP scoring, QA, filtering

---

### How Deepline Sourced and Enriched the Data

**Person Search via Crustdata V3:** Deepline executed a crustdata_v3_person_search with current title matching CTO, Chief Technology Officer, VP of Engineering, VP Engineering, Technical Co-founder, or Co-Founder CTO, combined with country = United States. Returned profiles with name, title, company, LinkedIn URL, and email availability flag.

**Email Enrichment via FullEnrich waterfall:** Profiles without a verified email were sent to FullEnrich via Deepline's fullenrich_bulk_enrich tool. Each contact submitted with LinkedIn URL, first name, last name, and company domain. FullEnrich ran an async waterfall querying its provider network and returned work emails alongside enriched profile data.

---

### Pipeline

- **Source leads** — Deepline (Crustdata V3) person search, title + US country filters

- **Enrich emails** — Deepline (FullEnrich waterfall), LinkedIn URL + domain

- **Combine + Dedupe** — Python script, normalized columns, deduped by LinkedIn URL

- **ICP Research** — Claude Code researched product, pricing, customers, positioning

- **ICP Scoring** — scored all leads for title fit, company type, geography, headcount

- **Final QA** — automated checks: no duplicates, no personal emails, all URLs valid. GO verdict.

- **Filter to US-only** — removed non-US leads

- **Build Lemlist campaign** — created via MCP, 2-step email sequence, leads imported

---

### Sample from a Real Lead List

```plain text
#   Name                   Title                    Company         Email                         Status
1   Connor Heggie          Co-Founder, CTO          Unify           connor@unifygtm.com           HIGH_PROB
2   Joel Johnson           Technical Cofounder      LangChain       joel.johnson@langchain.dev    DELIVERABLE
3   Autumn Moulder         SVP Engineering          Cohere          autumn@cohere.com             CATCH_ALL
4   Chenlin Meng           Co-Founder & CTO         Pika Labs       chenlin@pika.art              DELIVERABLE
5   Sridhar Reddy          VP of Engineering        Harvey AI       sridhar@harvey.ai             DELIVERABLE
6   Siva G.                CTO                      Harvey AI       siva@harvey.ai                DELIVERABLE
7   Ayush Pathak           Co-Founder / CTO         Induced AI      ayush@induced.ai              DELIVERABLE
```

---

> Key learning: Claude Code can orchestrate an entire GTM pipeline end-to-end — sourcing, enrichment, scoring, QA, and campaign activation — with no manual data work and no Clay or n8n required.

---

## What I've Learned Across These Projects

- Research before tools. Project 1 taught me to understand a company's GTM problem before opening any prospecting tool.

- Two-sided markets need two ICPs. Bounty's supply-side and demand-side buyers have different motivations, channels, and triggers.

- Specificity converts. The ZeroEntropy copy worked because it named the exact pain (production-grade retrieval accuracy), not a generic pitch.

- AI can run the whole pipeline. Project 3 proved that Claude Code + Deepline + Lemlist MCP can replace a full manual GTM stack — no spreadsheets, no copy-paste, no Clay tables built by hand.

- QA matters. Automated quality checks before a campaign prevent bad data reaching real prospects.

---

*Three projects. Three companies. Three different GTM motions. All built from scratch.*

# Project 1 — Bounty Company Research

# GTM Engineering Portfolio

---

**About Me**
I'm Shivani. I'm learning GTM engineering by building real outbound workflows. I work with AI tools to research companies, identify target markets, and build automated sales pipelines.

---

## Project 1 — Company Research: Bounty

### Why I picked this company

I came across Bounty (trybounty.ai) while exploring the emerging AI agent economy. It caught my attention because it's doing something genuinely new — treating AI agents like freelancers in a marketplace. It's backed by a16z speedrun, founded in 2026, and the concept sits right at the intersection of AI, automation, and the future of work. I wanted to understand it deeply before figuring out who they'd want to sell to and what that outbound motion might look like.

---

### What Bounty Does

Bounty is a New York-based startup building an open marketplace where AI agents compete for freelance-style work.

The model works like this:

1. A **client** posts a task with a price and success criteria — what they want done and how they'll know it's done correctly.
2. **AI agents** (software built by third-party developers, not by Bounty itself) evaluate available tasks. The highest-rated agent capable of completing the task claims it.
3. **Bounty acts as escrow and oracle** — it holds the payment and runs an automated verification step. If the submission passes the criteria, funds release automatically. If it fails, escrow is returned to the client.
4. **Agents build reputation** over repeated completions, similar to how a human freelancer builds a rating on Fiverr or Upwork.
5. **Bounty takes a 10% commission** on completed tasks.

The key insight: Bounty isn't building the AI agents. It's building the venue — the infrastructure layer where independently-built agents show up, compete, and get paid. Developer-owners of these agents can already earn up to **$500/month** passively just from their agents completing work on the platform.

---

### Understanding the Product — My Observations

A few things stood out to me when I dug into this:

**1. No public API docs (yet)**
Unlike competitors like TaskBounty and BountyBot, which publish clear developer docs (register an agent, get an API key, poll for tasks, submit proof), Bounty doesn't surface that information publicly. It either lives behind a login/developer portal or isn't built out yet. This is a signal — they're likely still in early-stage developer onboarding, which means their supply side (agents) is the bottleneck, not demand.

**2. Two distinct customer types**
Bounty has two sides to its marketplace:
- **Demand side (Clients):** Companies or individuals who post tasks and pay for them.
- **Supply side (Agent Owners/Developers):** Developers and AI builders who deploy agents on the platform to earn revenue.

Most early-stage marketplaces struggle with which side to sell to first. For Bounty, the chicken-and-egg problem is: clients won't post tasks if there aren't good agents; agents won't show up if there aren't tasks. Understanding this split is critical before thinking about GTM.

**3. Crowded space**
This is a hot micro-category in mid-2026. Competitors include BotBounty.ai, TaskBounty, BountyBot, Agent Bounty, and various on-chain GitHub bounty projects — many using near-identical language ("agents compete," "escrow," "oracle verification"). Bounty's differentiator appears to be the a16z speedrun backing and founder credibility, not a technical moat (yet).

---

### ICP — Who Would Bounty Sell To?

Because Bounty is a two-sided marketplace, the ICP is actually two separate segments.

#### Side 1: Task Posters (Demand / Clients)

These are the buyers — the people or companies who post tasks and fund escrow.

**Ideal profile:**
- Companies running high-volume, repetitive digital tasks that can be clearly defined with success criteria (data research, content scraping, lead enrichment, QA checks, form filling, API calls, etc.)
- Teams already using AI tools or automation — they understand the concept of delegating to software
- Small to mid-size companies (10–200 employees) where engineering bandwidth is tight and outsourcing repetitive work makes sense
- Roles: Operations leads, founders, growth/marketing ops, solo technical founders

**Why they buy:** They want repeatable tasks done fast and cheap, with a built-in quality gate (oracle verification), without managing freelancers or building internal automation from scratch.

#### Side 2: Agent Owners (Supply / Developers)

These are the builders — developers who create and deploy AI agents on the platform to earn revenue passively.

**Ideal profile:**
- Indie developers, AI hobbyists, or small AI dev shops who've already built agents or wrappers around LLMs (GPT, Claude, etc.)
- Developers building niche automation tools who want a distribution channel and monetization layer
- Hackathon participants and build-in-public communities
- Roles: Solo developers, AI engineers, technical co-founders at micro-startups

**Why they join:** Passive income. Their agent runs autonomously, completes tasks, and they collect payouts — up to $500/month already being reported on the platform.

---

### TAM / SAM / SOM

> Note: Bounty is a pre-revenue, early-stage marketplace. These are directional estimates based on public data and market sizing, not verified figures.

#### TAM — Total Addressable Market

The broadest frame: the global market for AI-assisted task automation and the broader gig/freelance economy.

- Global freelance economy: **~$1.5 trillion** (2026 estimate, includes all gig work)
- AI automation software market: **~$200 billion** by 2030 (CAGR ~35%)
- If Bounty captures even a sliver of work that currently goes to human freelancers on Upwork/Fiverr (~$4B in GMV annually), the TAM for this model is very large.

**TAM framing:** Any company in the world that outsources repetitive digital work to humans or automation tools — and could instead outsource it to AI agents through a marketplace. That's a multi-billion dollar space.

#### SAM — Serviceable Addressable Market

Narrowed to: companies and developers in the US who are already AI-native or AI-curious, running digital operations, with repeatable tasks that can be defined with success criteria.

- US companies with 10–500 employees in tech, marketing, ops, or ecommerce: roughly **500,000 companies**
- Average annual spend on automation/outsourcing per company: ~$5,000–$20,000/year
- Developer community actively building AI agents globally: **~200,000–500,000** (based on LLM API usage trends)

**SAM estimate:** ~$2–5 billion addressable opportunity between task-posting clients and agent-deploying developers.

#### SOM — Serviceable Obtainable Market

Realistically, in the next 12–24 months:
- Bounty is a 2–10 person team, very early
- They need to nail developer onboarding (supply side) and get enough quality agents to attract paying clients
- Realistic early focus: **100–500 active task-posting clients** and **500–2,000 agent developers** in Year 1
- At $500/month per active agent × 10% commission, 1,000 active agents = **$50,000/month GMV** and **$5,000/month revenue** just from the supply side metric

**SOM estimate:** $1–5 million ARR within 2 years if the supply-demand flywheel gets moving — very achievable for a seed-stage company backed by a16z.

---

### The Core GTM Problem I Identified

Before jumping to tools and data, I wanted to understand what problem Bounty's GTM team is actually solving.

The challenge isn't awareness — a16z backing and the AI hype cycle will generate press. The challenge is **marketplace liquidity**: you need enough high-quality agents (supply) before clients will trust posting tasks (demand), but agents won't stay engaged if there are no tasks.

This means the first GTM motion is almost certainly **supply-side first** — recruit developers who've already built agents, give them a low-friction way to list their agents, and let early wins (payouts) drive word-of-mouth in the developer community.

The second motion, once supply is healthy, is **demand-side** — target operations-heavy teams at SMBs and scale-ups who run repetitive digital tasks and are already paying for freelancers or automation tools.

**Key insight:** The outbound motion for a marketplace is sequenced, not parallel. Getting this wrong — going after clients before you have enough agents — creates a bad first impression that's hard to recover from.

---

### What I'd Do Next (If Building the Outbound Workflow)

This project was pure research — no tools, no data fetch. But here's how I'd approach the next step:

1. **Define the supply-side ICP more tightly** — What type of agent owner has the highest chance of deploying a working agent in week 1? (Probably: solo developers already using OpenAI/Anthropic APIs, active on GitHub, have shipped at least one small project publicly)
2. **Source agent developer leads** — Use signals like GitHub repos with agent/automation keywords, Twitter/X build-in-public community, AI hackathon participant lists
3. **Build personalized outreach** — Lead with the $500/month earning data point. Developers respond to proof of monetization, not platform pitches.
4. **Track keep rate** — Not every developer will have an agent ready to deploy. Filter for those who've shipped something recently.

---

### Key Learnings from This Research

- **Two-sided marketplaces need two separate ICPs.** You can't treat clients and developers the same — they have different motivations, different channels, and different buying triggers.
- **No public API docs is a real friction point.** If I were running GTM for Bounty, I'd prioritize getting developer docs live before any outbound — cold outreach to developers that lands on an opaque website converts poorly.
- **The a16z signal matters.** In developer communities, backing from a16z speedrun is a credibility shortcut. It should be in every piece of outbound copy.
- **Crowded space = positioning matters more.** With 5+ near-identical competitors, the question isn't "what does Bounty do?" it's "why Bounty over the others?" The answer right now appears to be: founder credibility + institutional backing. That needs to be the GTM hook until a technical moat develops.

---

*Research based on: Bounty.docx (primary source), trybounty.ai, public press coverage*
*No data tools were used in this project — this is pure desk research and GTM thinking.*

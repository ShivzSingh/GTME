---
name: ICP_Research
disable-model-invocation: false
description: "Research a company's Ideal Customer Profile (ICP) purely from its name using web research — no spreadsheet or lead data required. Investigates the company's product, target market, customers, and positioning to produce a structured ICP with geography, industry, company type, headcount, seniority, and target functions. Triggers on phrases like 'find ICP for', 'what is the ICP of', 'research ICP', 'identify ICP from company name', 'who does X sell to', or any request to discover a company's target customer profile from just a company name."
---

# ICP Research

Identify a company's Ideal Customer Profile (ICP) using only its name. No spreadsheet, no lead list, no prior data required. Pure desk research and reasoning.

## Quick Start

1. Ask for the company name if not already provided.
2. Research the company — product, customers, positioning, use cases.
3. Reason through who their ideal buyer is based on what you find.
4. Output a structured ICP report.
5. Optionally save the ICP to `clients/<company-name>.md` for use with the `icp-identification` skill.

---

## Step 1 — Get the Company Name

If a company name was not provided, ask:

> "Which company would you like me to research the ICP for?"

Normalize the name to lowercase-kebab-case for saving: e.g., `zero-entropy`, `wispr-flow`, `bounty`.

---

## Step 2 — Research the Company

Use available tools (web search, web fetch, any MCP tools) to investigate the company. Work through the following research layers in order.

### Layer 1 — Basic Facts
Find:
- Full company name and website
- Founded year and HQ location
- Company size (employees)
- Funding stage and investors (if public)
- Industry / category

### Layer 2 — Product Understanding
Find:
- What does the product actually do? (Be specific — not just the tagline)
- What problem does it solve?
- How is it delivered? (API, SaaS, self-hosted, marketplace, etc.)
- What integrations or tech stack does it sit on top of?
- Is it a developer tool, business tool, or end-user product?

### Layer 3 — Customer Evidence
Find:
- Named customers, case studies, or testimonials on their website
- Industries mentioned in use cases
- Company sizes referenced ("enterprise", "SMB", "startups", "Fortune 500")
- Job titles referenced as users or buyers ("Head of Engineering", "VP of Data", etc.)
- Press coverage quoting customer types
- LinkedIn posts or founder interviews describing who they work with

### Layer 4 — Pricing and Positioning
Find:
- Pricing page (free tier, per-seat, usage-based, enterprise-only?)
- Who is the pricing aimed at? (individual devs, teams, enterprises?)
- What tier of company can realistically afford this?
- How do they position vs. competitors? (cheaper, more accurate, easier to use?)

### Layer 5 — Competitor and Category Context
Find:
- Who are their top 2–3 competitors?
- What does the competitive landscape tell you about the buyer?
- Are they going upmarket (enterprise) or downmarket (SMB/self-serve)?

---

## Step 3 — Reason Through the ICP

Before writing the output, explicitly reason through each ICP dimension. Think out loud — state what evidence supports each conclusion and flag anything that's unclear or inferred rather than confirmed.

Work through:

| Dimension | Question to answer |
|---|---|
| Geography | Which countries/regions appear in customer evidence, pricing, and company focus? |
| Industry | Is this a horizontal product (any industry) or vertical (specific sectors)? What industries are mentioned in use cases? |
| Company type | B2B SaaS? Enterprise? SMB? Marketplace? Is there a minimum sophistication requirement? |
| Headcount | What company size is realistic given the pricing, complexity, and use case? |
| Revenue / ARR | Is there a minimum scale the buyer needs to have? |
| Buyer persona | Who writes the check? (Economic buyer) |
| User persona | Who uses it day-to-day? (End user) |
| Trigger / timing | What event makes them look for this solution? (Hiring AI engineers, scaling RAG pipeline, getting hallucination complaints, etc.) |

---

## Step 4 — Output the ICP Report

Print a structured ICP report in the following format:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
ICP RESEARCH REPORT — {Company Name}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

COMPANY SNAPSHOT
  Product:     {one-line description}
  Category:    {e.g., RAG infrastructure, AI agent marketplace, voice dictation}
  Stage:       {Seed / Series A / Series B / etc.}
  HQ:          {City, Country}
  Size:        {employee count}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
IDEAL CUSTOMER PROFILE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

GEOGRAPHY
  In-ICP:      {list of countries/regions}
  Out-of-ICP:  {if any exclusions are clear}
  Confidence:  {High / Medium / Low} — {one sentence why}

INDUSTRY
  In-ICP:      {list, or "any (no restriction)"}
  Notes:       {any sector nuance}
  Confidence:  {High / Medium / Low}

COMPANY TYPE
  In-ICP:      {e.g., B2B SaaS, enterprise, scale-up with AI team}
  Confidence:  {High / Medium / Low}

HEADCOUNT
  Range:       {e.g., 50–5000 employees, or "any"}
  Confidence:  {High / Medium / Low}

TARGET PEOPLE
  Economic Buyer (who approves the purchase):
    Titles:    {e.g., VP of Engineering, Head of AI, CTO}
    Function:  {e.g., Engineering, AI/ML, Product}
    Seniority: {e.g., Director and above}

  End User (who uses it day-to-day):
    Titles:    {e.g., ML Engineer, AI Engineer, Backend Developer}
    Function:  {e.g., AI/ML team, Data team}
    Seniority: {e.g., IC to Senior}

BUYING TRIGGER
  {What event/pain causes them to look for this? e.g., "Scaling RAG system and hitting hallucination rate above acceptable threshold"}

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
RESEARCH NOTES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Sources used:     {list of URLs or sources checked}
  Inferred fields:  {list any dimensions that were reasoned rather than directly evidenced}
  Gaps:             {anything you couldn't find and would want to verify}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Step 5 — Save to Client File (Optional)

After delivering the report, offer to save:

> "Want me to save this ICP as `clients/{company-name}.md` so it can be used directly with the `icp-identification` skill to evaluate lead lists?"

If yes, save using the standard client file format:

```markdown
# ICP: {Company Name}

## Geography
allowed:
  - {list of countries}
notes: "{any notes}"

## Industry
allowed: "{list or 'any (no restriction)'}"
notes: "{any notes}"

## Company Type
allowed: "{description}"

## Headcount
min: {number or null}
max: {number or null}

## Revenue / ARR
min: null
max: null

## Target People

### Functions (departments/teams that are in-ICP)
allowed:
  - {list}
notes: "{any notes}"

### Seniority Levels (in-ICP)
allowed:
  - {list: C-level, VP, Head, Director, etc.}

### Title Keywords

#### Seniority signals
include_seniority:
  - {list}

#### Function signals
include_function:
  - {list}

#### Explicit exclusions
exclude_function:
  - {list}

## Buying Trigger
{description of what event causes them to buy}

## Economic Buyer
{title(s) and function of who approves purchase}

## End User
{title(s) and function of who uses the product}

## Notes
{additional nuance, confidence flags, gaps}

## Research Sources
{URLs and sources used to build this ICP}
```

Save to `clients/{company-name}.md`.

---

## Confidence Levels

Use these definitions consistently:

- **High** — Directly evidenced. Named customers, pricing page language, or founder quotes confirm this dimension.
- **Medium** — Reasonably inferred. Product type, category norms, or indirect signals support it but it's not directly confirmed.
- **Low** — Guessed or assumed from thin evidence. Flag clearly — do not present as fact.

---

## Edge Cases

- **No website or very sparse web presence:** Use LinkedIn company page, Crunchbase, press coverage, and founder LinkedIn posts. State clearly what sources were available.
- **Very early-stage company (pre-launch):** ICP may be based entirely on founder background, investor thesis, and product description. Mark all fields as Medium or Low confidence.
- **Two-sided marketplace:** Identify both sides separately — demand side (who posts tasks / pays) and supply side (who provides the service / builds on the platform). Each side has its own ICP.
- **Horizontal product (sells to everyone):** Don't force a narrow ICP. State "any industry, any company type" where true — but still narrow on seniority, function, and geography based on evidence.
- **Company pivoted recently:** Check founding date vs. current product. If there's a gap, note that early customer evidence may not reflect the current ICP.

---

## Research Quality Rules

1. **Read before you claim.** Visit the website, pricing page, and at least one case study before drawing conclusions. Don't assume from the company name alone.
2. **Separate evidence from inference.** Always distinguish what you found vs. what you reasoned.
3. **Be specific about titles.** "Technical buyer" is vague. "Director of ML Engineering at a Series B SaaS company" is useful.
4. **Flag gaps honestly.** A gap you flag is more useful than a confident wrong answer.
5. **Prioritize customer evidence over product description.** What customers say they use it for beats what the marketing page claims.

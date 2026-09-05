---
name: final-qa
description: "Run the last quality check on a lead list or a draft outbound campaign before it goes live. Use whenever the user says 'QA this', 'final check before launch', 'is this ready to send', 'review before I turn it on', 'check this campaign', or hands over a combine-and-dedupe / icp-identification output or a HeyReach or Lemlist draft and asks whether it's good to go. Produces a PASS / WARN / FAIL checklist and a GO / NO-GO verdict; offers fixes for anything that fails but never edits data or launches on its own."
metadata:
  author: user
  version: "1.0.0"
---

# Final QA

The gate between "built" and "live". This skill inspects the thing the user is
about to launch — a lead list or a draft campaign — against every parameter
that matters, reports what it finds, and gives a clear **GO / NO-GO**. It is
the last check, so it errs toward surfacing issues: a false alarm costs a
minute, a bad send costs a domain reputation or a client relationship.

## Boundaries

- **Never launches anything.** Not a campaign, not a send. If everything
  passes, the verdict is GO and the user launches manually.
- **Never edits data silently.** When a check fails, explain it, then *offer*
  the specific fix and apply it only after the user says yes. Re-run QA after
  any fix.
- **Reports inline.** No report file — a structured checklist in chat, then the
  verdict.

## Step 1 — Identify what you're QA'ing

Ask if it isn't obvious from the request:

- A **lead list** — a `combine-and-dedupe` master sheet, an
  `icp-identification` split workbook, or any people/company sheet.
- A **HeyReach campaign** (LinkedIn) — needs the campaign name or id.
- A **Lemlist campaign** (email) — needs the campaign name or id.
- **A list + the campaign it feeds** — QA both; the list first.

Also confirm the **client** and, for a list, where the client's ICP criteria
live (so ICP-fit spot checks are possible). For a campaign, confirm the
intended **sender** and **launch day** so QA can check them rather than guess.

## Step 2 — Run the checks

Read the reference file(s) for what's in scope and work through every check:

| Scope | Reference | How |
| --- | --- | --- |
| Lead list | `references/lead-list-checks.md` | Run `scripts/qa_lead_list.py` for the deterministic checks, then do the judgment checks yourself. |
| HeyReach campaign | `references/heyreach-checks.md` | Pull the live draft with HeyReach tools and check each parameter. |
| Lemlist campaign | `references/lemlist-checks.md` | Pull the live draft with Lemlist tools and check each parameter. |

For the lead list, run the script first:

```bash
python3 "<skill dir>/scripts/qa_lead_list.py" --input <master_or_icp_file.xlsx>
```

Pass `--input` twice to check a master sheet and its ICP-split workbook
together. The script emits JSON findings tagged `FAIL` / `WARN` / `INFO` and a
`verdict_hint`. It covers dupes, blank/malformed/personal emails, missing
columns, LinkedIn URL sanity, non-fit rows leaking into fit tabs, and missing
non-fit reasons — but it does **not** judge whether the ICP calls were
*correct*. Do that yourself on a sample (Step 3 in the reference file).

## Step 3 — Report and give the verdict

Render one checklist. Group by scope (List / HeyReach / Lemlist). For each
check: `PASS`, `WARN`, or `FAIL`, one line of detail, and the offending rows or
fields when there are any.

Then the verdict:

- **GO** — no FAILs and no WARNs, or only WARNs the user has explicitly
  accepted. Say it's clear to launch and remind them the skill did not launch it.
- **NO-GO** — one or more FAILs. List them, then for each offer the concrete
  fix (re-run dedupe, drop the N no-email rows, patch the missing placeholder
  fallback, fix the sequence delay, connect a mailbox, …). Apply only what the
  user approves, then re-run the relevant checks and re-issue the verdict.

Keep WARN honest: it means "a human should look", not "probably fine". A list
that is 40% personal emails is a WARN the user needs to see, not an INFO.

## What each scope checks (summary)

- **Lead list** — no duplicate people or companies; every row has a usable
  work email (or the user has accepted the gap); no malformed emails or
  LinkedIn URLs; required columns present; row counts sane vs. what the user
  expected; for ICP output — fit/non-fit tabs don't overlap, every non-fit row
  has a reason, and a sampled handful of fit *and* non-fit calls actually match
  the client's criteria.
- **HeyReach** — sequence matches the intended structure and delays;
  connection request within the character limit for the account type; every
  placeholder used has a confirmed fallback; sender is the confirmed account;
  schedule (days, hours, timezone, start date) is right; exclusions set;
  status is `DRAFT`.
- **Lemlist** — 6 email steps; follow-ups thread onto the prior subject; each
  subject under the word limit and each body within the length limit; gaps
  between steps are natural; schedule right; a sending mailbox is connected;
  status is `draft`.

The full per-check detail is in the three reference files — read the ones in
scope before reporting.

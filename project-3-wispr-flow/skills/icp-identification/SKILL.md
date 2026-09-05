---
name: icp-identification
disable-model-invocation: false
description: "Check a spreadsheet of accounts and/or people against a client's Ideal Customer Profile (ICP) criteria and split them into fit vs. non-fit tabs. Triggers on phrases like 'evaluate ICP', 'check ICP fit', 'filter accounts by ICP', 'separate ICP fits', 'score against ICP', or any request to classify a list of companies or people against a target customer profile."
---

# ICP Identification

Evaluate a sheet of accounts and/or people against a client's ICP criteria. Output four clean tabs: ICP-fit people, ICP-fit companies, non-ICP people (with reasons), non-ICP companies (with reasons). Original data is never deleted — only reclassified.

## Quick Start

1. Ask the user which client this run is for (if not already stated).
2. Load `clients/<client-name>.md` from this skill folder. If the file doesn't exist, ask the user for their ICP criteria and offer to save a new client file.
3. Ask for the sheet/file to evaluate if not provided (accepts `.xlsx`, `.csv`, or Google Sheets link).
4. Evaluate each row against the client's ICP criteria.
5. Write four output tabs back to the same file.

---

## Step 1 — Identify the Client

If the client was not named in the invocation prompt, ask:

> "Which client is this ICP run for? (e.g., wispr-flow, acme-corp)"

Normalize the name to lowercase-kebab-case to match the filename: `clients/<client-name>.md`.

---

## Step 2 — Load Client ICP Criteria

Read `clients/<client-name>.md` from this skill's directory.

**If the file does not exist:**

> "I don't have an ICP profile for **{client-name}** yet. Please tell me their ICP criteria and I'll run the evaluation and optionally save a new client file for future runs."

Collect at minimum:
- Geography (which regions/countries are in-ICP)
- Company type / industry (any restriction, or open)
- Target people: which functions and seniority levels are in-ICP

Once the user provides the criteria, offer to save them:
> "Want me to save this as `clients/{client-name}.md` so you don't have to re-enter it next time?"

---

## Step 3 — Get the Sheet

If no file was provided, ask:
> "Please share the file to evaluate — .xlsx, .csv, or a Google Sheets link."

Accepted inputs:
- `.xlsx` — read all rows from the first/main sheet
- `.csv` — read all rows
- Google Sheets URL — export as CSV via `https://docs.google.com/spreadsheets/d/{id}/export?format=csv` and read

Identify which columns are present. Common column names to detect:
- Company/Account: `company`, `account`, `organization`, `company_name`
- Person: `first_name`, `last_name`, `full_name`, `name`, `contact`
- Title/Role: `title`, `job_title`, `role`, `position`
- Geography: `country`, `region`, `location`, `hq_country`, `hq_location`
- Industry: `industry`, `vertical`, `sector`
- Company type: `company_type`, `type`, `segment`
- Seniority: `seniority`, `level`

If critical columns are missing or ambiguous, ask the user to confirm the mapping before proceeding.

---

## Step 4 — Evaluate Each Row

Apply the client's ICP criteria from their `clients/<client-name>.md` file. For every row, determine:

1. **Is this a person row, a company row, or both?**
   - A row has a person if `first_name`, `last_name`, or `full_name` is populated.
   - A row has a company if `company` or `account` is populated.
   - Many rows will have both — evaluate the person AND the company independently.

2. **Run each ICP dimension check** as defined in the client file (see "ICP Dimension Checklist" below).

3. **Assign fit status:**
   - `ICP-FIT` — passes all applicable dimensions
   - `NON-ICP` — fails one or more dimensions; record a short reason (see "Reason Formatting" below)

### ICP Dimension Checklist (generic — client file overrides specifics)

| Dimension | What to check | Client file field |
|---|---|---|
| Geography | Country / region vs. allowed list | `geography.allowed` |
| Industry | Industry vs. allowed list (if restricted) | `industry.allowed` |
| Company type | B2B / B2C / enterprise / SMB / etc. | `company_type.allowed` |
| Headcount | Employee range | `headcount.min` / `headcount.max` |
| Revenue | ARR / revenue range | `revenue.min` / `revenue.max` |
| Function | Department / team of the person | `people.functions.allowed` |
| Seniority | Title-based level | `people.seniority.allowed` |
| Title keywords | Specific title signals (include/exclude) | `people.title_keywords` |

Skip any dimension not defined in the client file.

### Reason Formatting

Keep reasons concise — one short phrase, 5–10 words max. Examples:

- `"Outside ICP geography: India"`
- `"Title below Director level: Analyst"`
- `"Non-tech function: Sales"`
- `"Industry not in ICP: Agriculture"`
- `"Seniority not matched: Intern"`

If a row fails multiple dimensions, list the primary (most disqualifying) reason first, then append secondary reasons separated by ` | `.

Example: `"Outside ICP geography: Brazil | Non-tech function: Finance"`

---

## Step 5 — Split Into Four Output Tabs

Write four tabs/sheets back to the original file. Never delete or modify the source rows.

| Tab name | Contents |
|---|---|
| `ICP - People` | Person rows where the person is ICP-fit |
| `ICP - Companies` | Company rows where the company is ICP-fit |
| `NON-ICP - People` | Person rows where the person is non-ICP, plus a `Non-ICP Reason` column |
| `NON-ICP - Companies` | Company rows where the company is non-ICP, plus a `Non-ICP Reason` column |

**Rules:**
- Keep all original columns in every tab.
- Add a `Non-ICP Reason` column only to the two NON-ICP tabs.
- If a row has both a person and a company, the person goes to the People tabs and the company goes to the Company tabs — they are evaluated independently.
- A person can be ICP-fit while their company is non-ICP, and vice versa. Classify each independently.
- Rows with missing data in a required dimension: mark as `NON-ICP` with reason `"Missing required field: {field}"`.

**For `.xlsx` files:** Use `openpyxl` to write new sheets. Do not overwrite the original sheet — add the four tabs alongside it.

**For `.csv` files:** Cannot add tabs natively. Write four separate files:
- `{original-name}_icp_people.csv`
- `{original-name}_icp_companies.csv`
- `{original-name}_nonicp_people.csv`
- `{original-name}_nonicp_companies.csv`

**For Google Sheets:** Use the Sheets API (if available) to add new sheets to the same spreadsheet. If API access is unavailable, fall back to CSV output and share the file paths.

---

## Step 6 — Summary Report

After writing outputs, print a brief summary:

```
ICP Evaluation Complete — {client-name}
─────────────────────────────────────────
Input rows:          {total}
ICP-fit people:      {n}
ICP-fit companies:   {n}
NON-ICP people:      {n}  (top reasons: ...)
NON-ICP companies:   {n}  (top reasons: ...)
─────────────────────────────────────────
Output: {file path or sheet URL}
```

List the top 2–3 non-ICP reasons by frequency so the user can quickly see the main disqualifiers.

---

## Adding a New Client

When saving a new client file, use this template and fill in the client's criteria:

```markdown
# ICP: {Client Name}

## Geography
allowed: [list of countries/regions, or "any"]

## Industry
allowed: [list, or "any (no restriction)"]

## Company Type
allowed: [e.g., B2B SaaS, enterprise, SMB, or "any"]

## Headcount
min: null   # or a number
max: null   # or a number

## Revenue / ARR
min: null
max: null

## Target People
### Functions (departments/teams that are in-ICP)
allowed: [e.g., Engineering, Product, IT — or "any"]

### Seniority Levels (in-ICP)
allowed: [e.g., Director, Head, VP, C-level]

### Title Keywords
include: []   # title must contain at least one of these (optional)
exclude: []   # title must NOT contain any of these (optional)

## Notes
[Any additional nuance or edge cases for this client]
```

Save to `clients/{client-name}.md` (lowercase-kebab-case filename).

---

## Python Helper (for .xlsx output)

When working with `.xlsx` files, use this pattern:

```python
import openpyxl

wb = openpyxl.load_workbook("input.xlsx")
ws_source = wb.active  # preserve original sheet

# Create four output sheets
sheets = {
    "ICP - People":         wb.create_sheet("ICP - People"),
    "ICP - Companies":      wb.create_sheet("ICP - Companies"),
    "NON-ICP - People":     wb.create_sheet("NON-ICP - People"),
    "NON-ICP - Companies":  wb.create_sheet("NON-ICP - Companies"),
}

# Write headers (copy from source + add reason col to NON-ICP sheets)
headers = [cell.value for cell in ws_source[1]]
for name, ws in sheets.items():
    if "NON-ICP" in name:
        ws.append(headers + ["Non-ICP Reason"])
    else:
        ws.append(headers)

# ... row classification logic ...

wb.save("input.xlsx")
```

---

## Edge Cases

- **Blank rows:** Skip entirely; do not classify.
- **Headers-only sheet:** Return an error — nothing to evaluate.
- **Unknown geography value** (e.g., "EMEA" when criteria list countries): Expand "EMEA" to constituent countries and check against the allowed list. If still ambiguous, flag as `"Geography unclear: {value}"` and mark NON-ICP.
- **Ambiguous seniority** (title is unclear): Use title keyword matching first; if still unclear, mark as `"Seniority unclear: {title}"` and treat as NON-ICP rather than guessing ICP-fit.
- **Missing company on a person row:** Evaluate the person only; skip company classification for that row.
- **Duplicate rows:** Preserve all duplicates in their classified tabs — do not deduplicate.

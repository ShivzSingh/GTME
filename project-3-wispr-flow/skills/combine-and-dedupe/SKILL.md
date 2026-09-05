---
name: combine-and-dedupe
disable-model-invocation: false
description: "Combine multiple spreadsheet files (CSV or XLSX) into one master Excel file, deduplicate records by LinkedIn URL → Email → Full Name + Company, keep the best available work email, and output two sheets: a People sheet and a Company sheet with standardized columns. Triggers on phrases like 'combine files', 'merge spreadsheets', 'dedupe contacts', 'dedupe leads', 'merge and dedupe', 'consolidate files', or any request to combine multiple contact or account lists into one clean master file."
---

# Combine and Dedupe

Merge multiple contact/account files into one clean master Excel workbook. Deduplicate in priority order: LinkedIn URL → Email → Full Name + Company. Output two sheets — **People** and **Company** — with standardized columns.

---

## Quick Start

1. Ask for the files to combine if not already provided.
2. Load and normalize all input files.
3. Separate rows into people records and company records.
4. Deduplicate people by: LinkedIn URL → Email → Full Name + Company.
5. Deduplicate companies by: Company LinkedIn URL → Company Domain → Company Name.
6. Pick the best email for each merged person record.
7. Write `master_output.xlsx` with two sheets: **People** and **Company**.
8. Print a summary.

Run the full pipeline via:

```bash
python3 scripts/combine_dedupe.py --inputs file1.csv file2.xlsx file3.csv --output master_output.xlsx
```

---

## Step 1 — Collect Input Files

If no files were provided in the request, ask:

> "Please share the files you want to combine — .csv or .xlsx. You can list multiple files."

Accepted formats:
- `.csv` — any delimiter (auto-detect comma vs. tab vs. semicolon)
- `.xlsx` — read from the first sheet by default; if the file has multiple sheets, ask which sheet(s) to include

Minimum: 2 files. If only one file is provided, confirm whether the user still wants normalization + output reformatting applied to a single file.

---

## Step 2 — Load and Normalize All Files

For each input file:

1. Read all rows into a unified in-memory table.
2. Tag each row with its source filename (`_source` column) for traceability.
3. **Normalize column names** — map any detected column to a canonical name using the alias table below. Column matching is case-insensitive and ignores extra spaces.

### Column Alias Table

#### People fields

| Canonical name | Common aliases to detect |
|---|---|
| `first_name` | First Name, firstname, fname, first |
| `last_name` | Last Name, lastname, lname, last, surname |
| `full_name` | Full Name, fullname, name, contact name, person name |
| `job_title` | Job Title, title, position, role, designation |
| `seniority` | Seniority, seniority level, level |
| `linkedin_url` | LinkedIn URL, linkedin, linkedin profile, profile url, li url |
| `city` | City, town, locality |
| `state` | State, province, region |
| `country` | Country, country name, nation, hq country |
| `company_name` | Company, company name, account, organization, org, employer |
| `company_domain` | Company Domain, domain, website, company website, web |
| `company_linkedin_url` | Company LinkedIn, company linkedin url, org linkedin, co linkedin |
| `email` | Email, email address, work email, business email, e-mail |

#### Company fields

| Canonical name | Common aliases to detect |
|---|---|
| `company_name` | Company, company name, account, organization |
| `company_domain` | Domain, website, company domain, company website |
| `company_linkedin_url` | Company LinkedIn URL, company linkedin, org linkedin |
| `employee_count` | Employee Count, employees, headcount, company size, team size, num employees |

If a column cannot be mapped to any alias, keep it as-is in a `_extra` carry-along and do not discard it. This prevents data loss from unusual column names.

### Derive Missing Fields

- If `full_name` is missing but `first_name` + `last_name` exist: `full_name = first_name.strip() + " " + last_name.strip()`
- If `first_name` + `last_name` are missing but `full_name` exists: split on the first space — `first_name = parts[0]`, `last_name = " ".join(parts[1:])` (empty string if single-token name)
- If `company_domain` is missing but an email is present: extract domain from email — `company_domain = email.split("@")[1]` (only if email is not a free provider — see Email Scoring below)

---

## Step 3 — Separate People vs. Company Records

A row is a **people record** if it has any of: `first_name`, `last_name`, `full_name`, `job_title`, `linkedin_url`, `email`.

A row is a **company record** if it has any of: `company_name`, `company_domain`, `company_linkedin_url`, `employee_count` — AND none of the people fields above are populated.

Most rows will be people records (they carry both person AND company fields). Extract the company portion of every people record and feed it into the company deduplication pipeline separately. A row can contribute to both outputs.

---

## Step 4 — Deduplicate People Records

Apply deduplication in strict priority order. Process the full record list once per pass.

### Pass 1 — LinkedIn URL match

Normalize LinkedIn URLs before comparing:
- Strip trailing slashes
- Lowercase the entire URL
- Remove query strings (`?...`)
- Collapse `http://` → `https://`
- Remove `/in/` locale prefix variants (`/in/en/` → `/in/`)
- Extract just the handle: `linkedin.com/in/<handle>` → compare `<handle>` only

Group records with the same normalized LinkedIn handle. Merge the group into one record (see "Merge Strategy" below). Mark the rest as duplicates.

### Pass 2 — Email match (on remaining unmatched records)

Normalize emails:
- Lowercase
- Strip leading/trailing whitespace
- Treat `firstname.lastname+tag@domain.com` → strip `+tag` for matching only (keep original in output)

Group records with the same normalized email. Merge groups.

### Pass 3 — Full Name + Company Name match (on remaining unmatched records)

Normalize for matching (do NOT overwrite stored values):
- `norm_name = full_name.lower().strip()` — remove punctuation, collapse spaces
- `norm_company = company_name.lower().strip()` — remove punctuation, collapse spaces, strip legal suffixes (`inc`, `llc`, `ltd`, `corp`, `co`, `gmbh`, `sas`, `bv`, `ag`, `plc`)

Group records where `norm_name + "|" + norm_company` are identical. Merge groups.

Records that remain unmatched after all three passes are unique — include them as-is.

### Merge Strategy

When collapsing a group of duplicate records into one canonical record:

1. **LinkedIn URL** — use the first non-empty value
2. **Email** — apply Email Scoring (see Step 5) and keep the best-scoring email; store all others in `email_alternatives` (not output but used during merge)
3. **First Name / Last Name / Full Name** — prefer the most-complete version (most non-null fields); if equal, use the first-encountered record
4. **Job Title** — prefer the most specific (longest non-empty value)
5. **Seniority** — prefer non-empty; if multiple non-empty values differ, keep the first-encountered
6. **City / State / Country** — prefer non-empty; if multiple non-empty values differ, keep the first-encountered
7. **Company Name** — prefer non-empty; longest wins if multiple
8. **Company Domain** — prefer non-empty; if multiple differ, pick the one that looks like a root domain (fewest path segments)
9. **Company LinkedIn URL** — prefer non-empty; first-encountered if multiple differ
10. **Source** — concatenate all source filenames: `"file1.csv, file2.xlsx"`

---

## Step 5 — Email Scoring (Pick Best Work Email)

Score each email in a merged group and keep the highest-scoring one in the `email` output column.

| Score | Condition |
|---|---|
| 10 | Domain matches `company_domain` exactly |
| 8 | Domain matches `company_domain` (apex only, ignoring subdomains) |
| 5 | Non-free-provider domain, domain not in free/personal list |
| 2 | Free provider / personal email (see list below) |
| 0 | Malformed, empty, or placeholder value |

**Free / personal provider list** (treat as score 2):
`gmail.com`, `yahoo.com`, `hotmail.com`, `outlook.com`, `icloud.com`, `me.com`, `mac.com`, `aol.com`, `protonmail.com`, `proton.me`, `zoho.com`, `yandex.com`, `mail.com`, `gmx.com`, `live.com`, `msn.com`

Always keep the highest-scoring email in the `email` column. If two emails tie, prefer the one whose domain matches `company_domain`.

---

## Step 6 — Deduplicate Company Records

Collect company records from two sources:
1. Rows that were company-only records (Step 3)
2. The company-portion of every people record (extract `company_name`, `company_domain`, `company_linkedin_url`, `employee_count`)

Deduplicate companies in priority order:

### Pass 1 — Company LinkedIn URL match

Normalize: lowercase, strip trailing slash, extract handle from `linkedin.com/company/<handle>`.

### Pass 2 — Company Domain match

Normalize: lowercase, strip `www.`, strip trailing slash, extract apex domain (e.g., `blog.acme.co.uk` → `acme.co.uk`).

### Pass 3 — Company Name match

Normalize: lowercase, strip punctuation, strip legal suffixes (`inc`, `llc`, `ltd`, `corp`, `co`, `gmbh`, `sas`, `bv`, `ag`, `plc`), collapse spaces.

### Company Merge Strategy

For each merged company group, keep:
- `company_name` — prefer longest non-empty value
- `company_domain` — prefer the cleanest root domain (fewest path segments, no `www.`)
- `company_linkedin_url` — prefer non-empty; first-encountered if multiple differ
- `employee_count` — prefer the most recent non-empty value (last-encountered wins, as later files tend to be fresher)

---

## Step 7 — Write Master Output

Output file: `master_output.xlsx` (or a name the user specifies).

### People Sheet — columns in this exact order

| # | Column | Notes |
|---|---|---|
| 1 | First Name | |
| 2 | Last Name | |
| 3 | Full Name | |
| 4 | Job Title | |
| 5 | Seniority | |
| 6 | LinkedIn URL | Normalized (https, no trailing slash) |
| 7 | City | |
| 8 | State | |
| 9 | Country | |
| 10 | Company Name | |
| 11 | Company Domain | |
| 12 | Company LinkedIn URL | |
| 13 | Email ID | Best work email (from Step 5) |

### Company Sheet — columns in this exact order

| # | Column | Notes |
|---|---|---|
| 1 | Company Name | |
| 2 | Company Domain | |
| 3 | Company LinkedIn URL | |
| 4 | Employee Count | |

### Output rules

- Sheet names must be exactly: **`People`** and **`Company`**
- No extra columns beyond those listed above (unless user explicitly requests them)
- No index column
- Header row is row 1; data starts at row 2
- Empty cells for missing values — do not write "N/A", "Unknown", or placeholder strings
- LinkedIn URLs: always output as `https://www.linkedin.com/in/<handle>` (people) and `https://www.linkedin.com/company/<handle>` (companies) — normalize on write
- Email ID: single best email only; no alternatives in output

---

## Step 8 — Summary Report

After writing the file, print:

```
Combine & Dedupe Complete
─────────────────────────────────────────────
Input files:          {n} files
Total rows ingested:  {n}

People
  Before dedupe:      {n}
  Duplicates removed: {n}  (LinkedIn: {n}, Email: {n}, Name+Co: {n})
  After dedupe:       {n}
  With work email:    {n}
  Without email:      {n}

Companies
  Before dedupe:      {n}
  Duplicates removed: {n}
  After dedupe:       {n}

Output: {filepath}
─────────────────────────────────────────────
```

---

## Edge Cases

- **All files have different column names**: use the alias table (Step 2). If a column genuinely can't be mapped, flag it and ask the user before discarding.
- **Duplicate LinkedIn URLs that point to different people** (data error): log a warning — `"Conflicting names for LinkedIn URL {url}: '{name1}' vs '{name2}' — keeping first-encountered"` — and proceed.
- **Email domain = free provider but no company domain available**: keep the email at score 2 rather than discarding it.
- **Empty files or files with headers only**: skip with a warning — `"Skipping {filename}: no data rows found"`.
- **Single-name people** (only first name, no last name, no full name): keep them; do not discard. `last_name` will be empty.
- **company_domain looks like an email** (data entry error): strip the `@` prefix and treat the remainder as the domain.
- **Excel files with merged cells**: unmerge before reading — fill down the merged value into each row.
- **Mixed people + company-only rows in the same file**: handle both types in one pass; separate them in Step 3.

---

## References

- **`scripts/combine_dedupe.py`** — full Python implementation. Run with `--help` for all flags.
  - `--inputs` — one or more input file paths
  - `--output` — output file path (default: `master_output.xlsx`)
  - `--sheet` — for multi-sheet XLSX inputs, specify sheet name (default: first sheet)
  - `--no-company` — skip Company sheet output
  - `--dry-run` — print summary counts without writing output file

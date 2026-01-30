# Research Todo Scanner

Scan recent git commits and report unresolved research items only.

**Argument**: $ARGUMENTS (optional — `[days] [folder_prefix]`, e.g., `7 12_` or just `11_`)

## Instructions

You are scanning recent changes to surface unresolved **research work** only. Follow these steps precisely. **Do NOT create any files** — print all results directly in the terminal as plain text.

### Step 1: Parse Arguments

Parse **$ARGUMENTS** to extract:
- **Days**: If a number is provided, use it as days to look back. Default: **3**
- **Folder prefix**: If a pattern like `11_` or `12_` is provided, filter to only that folder. Default: `11_` and `12_` (excludes `10_ResearchIdea`)

Examples:
- `` (empty) → 3 days, folders `11_*` and `12_*`
- `7` → 7 days, folders `11_*` and `12_*`
- `11_` → 3 days, only `11_*` folder
- `5 12_` → 5 days, only `12_*` folder

### Step 2: Determine Date Range

- Today's date: check the `<env>` context for "Today's date"
- Calculate start date = today minus N days

### Step 3: Gather Changes from Git

Run these commands to find what changed in the date range:

**Committed changes:**
```bash
git log --since="<start-date-ISO>" --diff-filter=ACDMR --name-only --pretty=format:"" -- "*.md"
```

**Unstaged and uncommitted changes (IMPORTANT — always include these):**
```bash
git diff --name-only -- "*.md"
git diff --cached --name-only -- "*.md"
```

Then read the actual diffs to understand the content:

```bash
git diff $(git log --since="<start-date-ISO>" --format="%H" --reverse -- "*.md" | tail -1)^..HEAD -- "*.md"
git diff -- "*.md"
git diff --cached -- "*.md"
```

If git log returns no commits, fall back to:

```bash
git diff HEAD~5..HEAD -- "*.md"
```

**Combine all sources**: Merge file lists from committed, staged, and unstaged changes, removing duplicates.

**Exclude** files matching these patterns:
- `.obsidian/**`
- `.claude/**`
- `00_random/**`
- `01_WeeklyLog/**`
- `2*_*/**` (administrative folders like 20_*, 21_*, 22_*)
- `10_*/**` (research ideas — excluded by default unless explicitly requested)

**Include only** research folders based on parsed folder prefix:
- If no prefix specified: `11_*/**` and `12_*/**`
- If prefix specified (e.g., `12_`): only that folder pattern

### Step 3: Read Changed Files

For each changed `.md` file found in Step 2, read the **current file content** (not just the diff).

### Step 4: Semantic Analysis

Analyze the content using semantic understanding — **NOT keyword matching**. Only flag **research-critical** items:

1. **Paper deadlines**: Camera-ready, submission, revision deadlines within 14 days
2. **Experiment blockers**: Failed runs, missing data, broken pipelines, resource issues
3. **Code/implementation issues**: Bugs, missing features blocking research progress
4. **Collaboration blockers**: Waiting on co-authors, advisors for research decisions (not admin)
5. **Reproducibility issues**: Missing checkpoints, unclear experiment configs

**Do NOT flag**:
- Administrative tasks (funding, registration, travel, reimbursement)
- Exam prep, coursework, or academic admin
- Conference attendance logistics (only flag paper-related deadlines)
- General project ideas or future research directions
- Deferred experiments or "nice to have" improvements

### Step 5: Report in Terminal

Print results directly as plain text. Do NOT create any files.

Output a **flat list** — no folder headers, no grouping. Each line:
```
- [file] brief description (reason: paper deadline / experiment blocker / code issue / waiting on X)
```

Rules:
- Korean content stays in Korean, English stays in English
- Maximum ~10 items. If more, keep only the most urgent
- If nothing critical is found, just print "No critical research items."

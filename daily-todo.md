# Daily Todo Scanner

Scan recent git commits and report unresolved, problematic, or TODO-like items.

**Argument**: $ARGUMENTS (optional — number of days to look back, defaults to 3)

## Instructions

You are scanning recent changes to surface unresolved work, open questions, and implicit next steps. Follow these steps precisely. **Do NOT create any files** — print all results directly in the terminal as plain text.

### Step 1: Determine Date Range

- Days to look back: **$ARGUMENTS** (if empty, default to **3**)
- Today's date: check the `<env>` context for "Today's date"
- Calculate start date = today minus N days

### Step 2: Gather Changes from Git

Run these commands to find what changed in the date range:

```bash
git log --since="<start-date-ISO>" --diff-filter=ACDMR --name-only --pretty=format:"" -- "*.md"
```

Then read the actual diffs to understand the content:

```bash
git diff $(git log --since="<start-date-ISO>" --format="%H" --reverse -- "*.md" | tail -1)^..HEAD -- "*.md"
```

If git log returns no commits, fall back to:

```bash
git diff HEAD~5..HEAD -- "*.md"
```

**Exclude** files matching these patterns:
- `.obsidian/**`
- `.claude/**`
- `02_WeeklyMeeting/**`

### Step 3: Read Changed Files

For each changed `.md` file found in Step 2, read the **current file content** (not just the diff). Group by project folder:

- `00_random/` — Miscellaneous notes
- `01_WeeklyLog/` — Weekly logs
- `10_ResearchIdea/` — Research ideas
- `11_ExoPlore/` — ExoPlore project
- `12_CP/` — CP (Contraction Parameter) project
- `21_CourseWork/` — Course work
- `22_GradAdmin/` — Graduate administration
- `99_Archive/` — Archived items

### Step 4: Semantic Analysis

Analyze the content using semantic understanding — **NOT keyword matching**. Only flag **critical** items:

1. **Deadlines at risk**: Items with explicit deadlines approaching within 7 days, or overdue
2. **Blockers**: Problems actively preventing progress on current work
3. **Action items requiring someone else**: Things waiting on a specific person (교수님, collaborators, etc.) that haven't been resolved

**Do NOT flag**:
- Vague future plans, research ideas, or "maybe later" notes
- Open questions that are exploratory, not blocking
- Exam prep materials or reference content
- Deferred items the author explicitly chose to postpone
- Minor administrative tasks with no deadline pressure

### Step 5: Report in Terminal

Print results directly as plain text. Do NOT create any files.

Output a **flat list** — no folder headers, no grouping. Each line:
```
- [file] brief description (reason: deadline / blocker / waiting on X)
```

Rules:
- Korean content stays in Korean, English stays in English
- Maximum ~10 items. If more, keep only the most urgent
- If nothing critical is found, just print "No critical items."

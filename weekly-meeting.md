# Weekly Meeting Slide Generator

Generate Reveal.js slides for the weekly lab meeting.

**Argument**: $ARGUMENTS (optional — meeting day name, defaults to "Tuesday")

## Instructions

You are generating a weekly meeting slide deck. Follow these steps precisely.

### Step 1: Determine Date Range

- The meeting day is: **$ARGUMENTS** (if empty, default to **Tuesday**)
- Today's date: check the `<env>` context for "Today's date"
- Calculate the **most recent past occurrence** of the meeting day (if today IS that day, use today)
- Calculate the **previous occurrence before that** (7 days before the meeting date) as the start of the range
- The date range for git queries is: `[previous meeting day, meeting date)`

### Step 2: Discover Output Directory

Find an existing folder used for meeting slides by looking for a top-level directory whose name suggests meetings or presentations (e.g., contains "meeting", "weekly", "presentation", or similar). If none is found, create a `WeeklyMeeting/` folder at the vault root.

### Step 3: Compute File Name

The output file goes in the meeting directory found in Step 2, with this naming format:

```
DD, Mon, Nth.md
```

Where:
- `DD` = day of month of the meeting date (e.g., `29`)
- `Mon` = 3-letter English month abbreviation (e.g., `Jan`)
- `Nth` = week-of-month ordinal, calculated as `ceil(DD / 7)` → `1st`, `2nd`, `3rd`, `4th`, `5th`

Examples: `29, Jan, 5th.md`, `07, Feb, 1st.md`, `14, Jan, 2nd.md`

### Step 4: Gather Changes from Git

Run these commands to find what changed since the last meeting day:

**Committed changes:**
```bash
git log --since="<previous-meeting-day-ISO-date>" --until="<meeting-day-ISO-date-plus-1>" --diff-filter=ACDMR --name-only --pretty=format:"" -- "*.md"
```

**Unstaged and uncommitted changes (IMPORTANT — always include these):**
```bash
git diff --name-only -- "*.md"
git diff --cached --name-only -- "*.md"
```

Then read the actual diffs to understand the content:

```bash
git diff $(git log --since="<previous-meeting-day-ISO-date>" --format="%H" --reverse -- "*.md" | head -1)^..HEAD -- "*.md"
git diff -- "*.md"
git diff --cached -- "*.md"
```

If git log returns no commits, fall back to:

```bash
git diff HEAD~10..HEAD -- "*.md"
```

**Combine all sources**: Merge file lists from committed, staged, and unstaged changes, removing duplicates.

**Exclude** files matching these patterns:
- `.obsidian/**`
- `.claude/**`
- The meeting output directory itself

### Step 5: Read Changed Files

For each changed `.md` file found in Step 4, read the file content to understand what work was done. Group the changes by top-level folder, inferring each folder's purpose from its name.

### Step 6: Present Agenda for Approval (IMPORTANT)

Before generating the final file, you MUST present an agenda outline to the user and wait for approval. Show:

1. The proposed meeting date and file name
2. A categorized list of work items extracted from the diffs, grouped by folder
3. Proposed "다음 주 할 일" (todo for next week) items inferred from the work

Ask the user to confirm, modify, or add items before proceeding.

### Step 7: Generate the Slide File

After user approval, create the file at `<meeting-directory>/<computed-filename>` using this Reveal.js template (Obsidian Slides plugin uses `---` as horizontal slide separator):

```markdown
### YYYY.MM.DD

---

### 이번 주 한 일

- item 1
- item 2
- ...

---

### Topic 1
- detail 1
- detail 2

---

### Topic 2
- detail 1
- detail 2

---

### Todo

- todo 1
- todo 2
- ...
```

**Formatting rules:**
- Use `---` (three dashes on its own line) as the ONLY slide separator — this is what Obsidian's Slides core plugin (Reveal.js) requires
- The title slide uses `###` with the date only — no "주간 미팅" text
- Date format on title slide: `YYYY.MM.DD` (e.g., `2026.01.27`)
- "이번 주 한 일" is a high-level summary list (one bullet per major work area)
- Each detail topic gets its own `###` slide separated by `---` — do NOT prefix with "세부 내용"
- All content should be in **Korean** to match the existing lab notes
- Only include **paper & research** related content — exclude chores, admin, coursework, and graduate administration tasks
- Keep bullets concise — this is a presentation, not a document
- Do NOT add any YAML frontmatter or extra metadata

### Step 8: Confirm Output

After creating the file, tell the user:
- The file path created
- How to present it: Open in Obsidian → `Ctrl+P` → "Start presentation" (or "슬라이드 시작")

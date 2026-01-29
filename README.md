# Obsidian Claude Code Commands

Custom [Claude Code](https://claude.ai/claude-code) slash commands for managing an Obsidian-based lab notebook.

## Commands

### `/daily-todo`

Scans recent git commits and surfaces critical unresolved items: approaching deadlines, blockers, and action items waiting on others. Output is printed directly in the terminal.

**Usage**: `/daily-todo [days]` — number of days to look back (default: 3)

### `/weekly-meeting`

Generates a [Reveal.js](https://revealjs.com/) slide deck for the weekly lab meeting. Gathers changes from git, presents an agenda for approval, then writes a `.md` file compatible with Obsidian's Slides core plugin.

**Usage**: `/weekly-meeting [day]` — meeting day name (default: Tuesday)

## Installation

Copy the command files into your project's `.claude/commands/` directory:

```bash
cp daily-todo.md weekly-meeting.md <vault>/.claude/commands/
```

Or clone and symlink:

```bash
git clone https://github.com/DaebangStn/obsidian-cc-command.git
ln -s obsidian-cc-command/daily-todo.md <vault>/.claude/commands/daily-todo.md
ln -s obsidian-cc-command/weekly-meeting.md <vault>/.claude/commands/weekly-meeting.md
```

## Vault Structure

These commands assume the following folder layout:

| Folder | Content |
|---|---|
| `00_random/` | Miscellaneous notes |
| `01_WeeklyLog/` | Weekly logs |
| `02_WeeklyMeeting/` | Meeting slides (output) |
| `10_ResearchIdea/` | Research ideas |
| `11_ExoPlore/` | ExoPlore project |
| `12_CP/` | CP project |
| `21_CourseWork/` | Course work |
| `22_GradAdmin/` | Graduate administration |
| `99_Archive/` | Archived items |

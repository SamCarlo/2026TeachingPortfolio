# NWEA Explorer
## Terminal Data Viewer for MAP Growth Results | Spring 2026

---

## Overview

A terminal UI for browsing anonymized NWEA MAP Growth data by grade level and teacher.
Built with [Textual](https://textual.textualize.io/), it runs entirely in the terminal
with no browser, no server, and no network connection required.

The screen is divided into three panels: a grade selector, a teacher selector, and two
live data panels that update as you navigate. One panel shows average RIT scores with
sub-goal breakdowns; the other shows Fall-to-Winter growth compared against projected
and typical growth norms.

---

## What It Shows

**RIT Panel,** for the selected grade and teacher:
- Average RIT score per subject (Mathematics, Language Arts, Science)
- Sub-goal scores broken out by goal area (Operations, Literary Text, etc.)
- A bar chart scaled to the RIT range (130–240) so relative performance is visible at a glance
- Student count

**Growth Panel,** for the selected grade and teacher:
- Observed Fall-to-Winter growth
- Projected growth (what was expected for this cohort)
- Typical growth (national norm)
- Bar charts scaled to a common growth axis so the three values can be compared visually

Navigating the grade list immediately repopulates the teacher list with only the teachers
who have students in that grade. Selecting a teacher filters both panels to that teacher's
students. Selecting "All Teachers" returns to the full grade view.

---

## How to Run

```bash
pip install textual
python3 explorer.py
```

Press `q` to quit.

The database (`anon.db`) must be in the same directory as `explorer.py`. Student
identifiers in the database have been fully anonymized.

---

## Context

This tool was built for personal use, as a quick way to browse the data before writing
SQL queries or before a data conversation with an administrator. It is the lower-tech
counterpart to the NWEA Agent (`portfolios/2026/nwea-agent`): where the agent answers
specific questions, the explorer lets you scan the full picture and notice things worth
asking about.

In terms of the portfolio philosophy, this is a case where computation stays entirely on
the teacher's side of the desk. It is a tool the teacher uses; it never faces students
or administrators.

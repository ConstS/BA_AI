# PROJECT-RULES.md

Reference for Claude and the team: workspace structure, conventions, and document formats. Linked from [CLAUDE.md](CLAUDE.md).

Everything here is generic. Replace `<project>` and `<PROJ>` with your own project name and Jira key. Your personal specifics (repo names, Jira keys, vault paths) live in your own private repo and in `.claude/rules/local/`, not here.

---

## Distribution boundary (shared core vs personal)

The workspace is a clone of the shared core (`dataforest-ai/BA-WorkSpace-Claude`). What is shared and what stays yours:

Two remotes: your working repo's `origin` is your OWN private git (NDA-safe, holds everything you work on); the shared CORE is a separate exchange remote that receives only the shared subset via selective PRs. So "not in core" is enforced at sync/PR time, NOT by gitignore in your private repo.

- **Public (shared subset, goes to core):** `.claude/skills/` (skills, `ba-<bucket>-*` + imported utilities), `.claude/rules/*.md` (shared rules), `.claude/settings.json` (hooks only, no permissions), `CLAUDE.md`, `PROJECT-RULES.md`, `BABOK/`, `docs/`, `for-claude/`, `*.example` templates.
- **Personal (tracked in your OWN private `origin`, never in core):** `CLAUDE.local.md`, every file with the `local` suffix (e.g. `.claude/skills/ba-track-tasks/tasks.local.md`), `.claude/rules/local/` (per-project bindings), `logging/` (the log), `.mcp.json` at the kit root (your MCP servers). These are not git-ignored.
- **Git-ignored (only secrets and machine files, never in ANY git):** secrets: `.claude/credentials/` (e.g. `notion.env`), `.claude/skills/ba-track-jira/jira.env`, other `*.env`, `mempalace.yaml`, `_migration/`; machine files: `.claude/settings.local.json`, `.claude/setup-state.json` (ba-setup state), `logging/.log.lock`.
- **Permissions are personal:** each person sets their own in `settings.local.json` (auto / allow-all / custom). Not distributed.
- **No local skills:** `~/.claude/skills/` stays empty; every skill lives in the shared core (kaizen, improvements flow up via PR).
- **Project content:** each `<project>-vault/` is its own private repo (NDA isolation), a git-ignored neighbor, not part of the core.

---

## Shell and Python

Kit commands in skills, docs and rules are written for Bash and run in the Bash tool; on Windows through Git Bash, never through PowerShell. Python runs only through the kit launcher: `bash .claude/hooks/kit-python.sh <script.py> [args]` (it picks `python3`, `python` or `py -3`, version 3.10 or newer).

---

## Repository model

Your workspace is a clone of the core. You point `origin` at your own private git and keep the core as a second `core` remote for exchange. Each work project is a vault: a separate private repo in `<project>-vault/` at the workspace root, git-ignored by the workspace, set up with the prompt in [getting-started.md](docs/01_Установка/getting-started.md). `workspace/` holds only the course sandbox and drafts.

```
<workspace>/                     ← clone of core; origin = your private git
├── CLAUDE.md                    Shared machine rules (read at session start)
├── CLAUDE.local.md              Your persona/context (personal, @import'd into CLAUDE.md)
├── PROJECT-RULES.md             ← this file
├── BABOK/                       DataForest BA knowledge base (reference only, not edited)
├── docs/                        Human documentation (guides, prompts, examples)
├── for-claude/                  Runbooks for Claude (setup, verify-skills)
├── logging/                     Work logs: log-001.md, log-002.md, … (rotate at 300 lines)
├── workspace/                   Course sandbox and drafts (no work projects)
├── <project>-vault/             ← separate private repo (nested, git-ignored, NDA-isolated)
└── .claude/
    ├── skills/                  Skill definitions (ba-<bucket>-*, plus pdf/docx/xlsx utilities)
    ├── rules/                   Machine rules; those with paths: load only for matching folders
    │   └── local/               Your per-project bindings (personal, tracked in your origin, never in core)
    ├── hooks/                   Hook scripts and the Python launcher kit-python.sh
    ├── credentials/             Git-ignored secrets (notion.env, …); the Jira access file is skills/ba-track-jira/jira.env
    └── settings.json            Hooks only (local time, memory, kit updates, project map)
```

---

## Project vault structure

Each project vault uses numbered folders so order is stable and obvious. A typical layout:

```
<project>-vault/
├── 000 todo/                    Action lists & worklist (<PROJ>-ACTION-LIST.md, MY-WORKLIST.md)
├── 001 info/                    Static reference (INFO.md, PRODUCT.md, GLOSSARY.md, QUESTIONS.md, Decision/Risk registers)
├── 002 moms/                    Meeting minutes, one file per meeting (YYYY-MM-DD Title.md)
├── 003 requirements/            Deliverables (FRDs, user flows, roadmap, WBS, walkthroughs)
│   └── Archive/                 Superseded versions
├── 004 dev-guides/              Dev/QA handoff guides (README index)
└── 005 working/                 Work-in-progress (research, drafts, harvests) not yet published; working notes beside requirements (code names, references, estimates)
```

**Published-vs-working rule:** the requirements folder holds only finished/published docs; work-in-progress (research, drafts, harvests) lives under the working folder. When a doc is published (e.g. to Notion, first line `> Notion: …`), move the clean version into the requirements folder. Do not scatter research into the published folder.

---

## Language

| Content type | Language |
|---|---|
| All `.md` documents | Project standard (set per project in `.claude/rules/local/`) |
| Log entries (`logging/`) | Same as project standard |
| Jira issue titles | **English** (all projects) |
| Jira descriptions | May be the team's working language |
| Meeting notes (`002 moms/`) | As recorded, preserve original language |
| Chat replies | Follows the user, respond in the language the user writes in |
| File and folder names | **English**, kebab-case, no spaces |
| Code and scripts | **English** |

---

## File Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Walkthrough docs | `NN-DESCRIPTIVE-NAME.md` | `03-PRICING-PAGE.md` |
| Walkthrough subfolders | Same as `.md` without extension | `03-PRICING-PAGE/` |
| Draw.io diagrams | `<base-name>-flow.drawio` or `<base-name>-sketch.drawio` | `02-PAGE-SETUP-flow.drawio` |
| Meeting notes | `YYYY-MM-DD Title.md` | `2026-05-20 Kick-off.md` |
| Work logs | `log-NNN.md` | `log-001.md` |
| All other files | kebab-case | `ba-wbs.md` |

---

## Screenshot Conventions

**Where raw pastes land:** the workspace root (Obsidian default attachment folder).

**Processing:** use the `ba-docs-screenshots` skill, it proposes a rename table, you confirm, then it renames, moves to a subfolder, updates links, and formats pairs/grids.

**Naming prefixes** (examples, extend per project):

| Prefix | Usage |
|--------|-------|
| `s01-`, `s02-`, … | Wizard / onboarding steps in order |
| `a01-`, `a02-`, … | Authentication steps |
| `configure-` | Page list / configuration screen |
| `editor-` | Editor views |
| `preview-` | Preview mode screens |
| `flow-diagram` | Exported draw.io flow diagram |

**State suffixes:** `-empty`, `-filled`, `-selected`, `-declined`, `-toast`, `-loading`, `-modal`, `-error`

**Numbered grids** (pricing tiers, galleries): `s05-pricing-01-free.png`, `s05-pricing-02-pro.png`, …

---

## BA Walkthrough Document Format

Every walkthrough document follows this structure:

```
# Title

> Flow: [one-line breadcrumb showing the path through the UI]

---

## Flow
[Text diagram: ASCII arrows showing entry → steps → exit]

---

## Known Issues

| # | Location | Issue | Severity |
|---|----------|-------|----------|
| 1 | Step X   | Description | High / Medium / Low |

---

## Section Name (one section per major screen or step group)

**States:**

| State | Trigger | Behaviour |
|-------|---------|-----------|
| ...   | ...     | ...       |

**Requirements:**
- REQ-01: …
- REQ-02: …

[Screenshots: single / 2-col pair / 3-col grid]
```

**Known Issues** are consolidated at the top, all bugs and gaps from the entire document in one table. Issues are also noted inline in their sections.

---

## Jira Structure

Hierarchy: **Epic → Story** (Tasks created ad hoc when needed). Your project's actual epics, stories, and keys live in `.claude/skills/ba-track-tasks/tasks.local.md`, not here.

```
Epic  <PROJ>-N   (e.g. Discovery, Project Launch)
└── Story  <PROJ>-M   (e.g. BA Kickoff, Onboarding Flow)
```

Time tracking via Tempo, log against Story keys. Use the `ba-track-tempo` skill; the task registry is read from `ba-track-tasks`.

---

## Draw.io Conventions

- **Canvas:** A4 landscape, 1654 × 827 px
- **Layout:** horizontal swimlanes (`horizontal=0`, `startSize=120`)
- **Cross-lane edges:** `parent="1"` (attached to root, not to a lane)
- **Files:** live next to the related `.md` in the same folder
- **Full style guide:** see the `ba-docs-drawio` skill

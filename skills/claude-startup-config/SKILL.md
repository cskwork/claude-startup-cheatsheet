---
name: claude-startup-config
description: Configure companyAnnouncements in settings.json with workflow cheat sheets. Auto-detects your harness (ECC, OMCC, vanilla) and displays workflow reminders at session start.
origin: claude-startup-cheatsheet
---

# Company Announcements

## When to Activate

- User asks to set up or configure session-start workflow reminders
- User mentions `companyAnnouncements` or `settings.json` workflow configuration
- User wants to see available slash commands as banners
- User installs a new harness (ECC, OMCC) and needs workflow cheat sheets
- User runs `/claude-startup-config`

Configure `companyAnnouncements` in `~/.claude/settings.json` with workflow cheat sheets tailored to your installed harness and commands.

## What It Does

1. **Detects** installed slash commands in `~/.claude/commands/`, `~/.claude/skills/*/`, and project `.claude/commands/`
2. **Categorizes** them into workflow chains (dev, debug, docs, learning, etc.)
3. **Generates** a `companyAnnouncements` block for `settings.json`
4. Cross-platform: macOS, Windows, Linux compatible

## Usage

```
/claude-startup-config                      # Auto-detect installed commands
/claude-startup-config --harness ecc        # ECC (Everything Claude Code) preset
/claude-startup-config --harness omcc       # Oh My Claude Code preset
/claude-startup-config --harness minimal    # Vanilla Claude Code preset
/claude-startup-config --harness custom     # Interactive custom selection
```

## Supported Harnesses

| Harness | Command Style | Key Workflows | Preset ID |
|---------|--------------|---------------|-----------|
| Everything Claude Code (ECC) | Standard slash: `/plan`, `/tdd`, `/verify` | orchestrate, TDD, multi-model, eval | `ecc` |
| Oh My Claude Code (OMCC) | Namespaced: `/oh-my-claudecode:autopilot` + magic keywords: `autopilot:`, `ralph:`, `ulw` | autopilot, team, ralph, ultrawork | `omcc` |
| Vanilla Claude Code | Built-in only: `/plan`, `/code-review` | plan, review | `minimal` |
| Custom | User-selected | Any combination | `custom` |

## Harness-Specific Workflow Patterns

### ECC (Everything Claude Code)

Standard slash commands without namespace prefix.

```
Dev/Bug:    /orchestrate feature|bugfix "desc" -> /e2e
Manual:     /plan -> /tdd -> /e2e -> /code-review -> /verify
Reproduce:  /e2e (as-is) -> /orchestrate bugfix -> /e2e (to-be)
Build:      /build-fix -> /verify
Quality:    /code-review -> /refactor-clean -> /verify | /quality-gate
Docs:       /update-docs, /update-codemaps | /docs "lib"
Multi:      /multi-plan -> /multi-execute | /devfleet "task"
Learn:      /learn -> /learn-eval -> /skill-create
Session:    /save-session, /resume-session
Meta:       /harness-audit, /skill-health, /context-budget
Instincts:  /instinct-status -> /evolve -> /promote | /prune
Lang:       /{lang}-review, /{lang}-build, /{lang}-test
```

### Oh My Claude Code (OMCC)

Namespaced commands (`/oh-my-claudecode:*`) plus magic keywords for quick access.

```
Autonomous: /oh-my-claudecode:autopilot "desc" (or keyword: autopilot: desc)
Persistent: /oh-my-claudecode:ralph "desc" (or keyword: ralph: desc)
Team:       /oh-my-claudecode:team 3:executor "task"
Parallel:   /oh-my-claudecode:ultrawork "tasks" (or keyword: ulw tasks)
Plan:       /oh-my-claudecode:omc-plan (or keyword: ralplan)
Clarify:    /oh-my-claudecode:deep-interview "vague idea"
Investigate:/oh-my-claudecode:trace "ambiguous problem"
QA:         /oh-my-claudecode:ultraqa "goal"
Visual QA:  /oh-my-claudecode:visual-verdict
Tri-Model:  /oh-my-claudecode:ccg "query" (Codex+Gemini+Claude)
Cleanup:    /oh-my-claudecode:ai-slop-cleaner (or keyword: deslop)
Skills:     /oh-my-claudecode:skill list|add|remove|search
Learn:      /oh-my-claudecode:learner
Session:    /oh-my-claudecode:psm (project session manager)
Release:    /oh-my-claudecode:release
Setup:      /oh-my-claudecode:setup | /oh-my-claudecode:omc-doctor

Pipeline:   deep-interview -> omc-plan --consensus -> autopilot
```

### Vanilla Claude Code (Minimal)

Built-in commands only, no harness required.

```
Dev:        /plan -> /code-review
Docs:       /docs "lib"
Session:    /save-session, /resume-session
```

## Output Format

The skill generates a JSON array for `companyAnnouncements` in settings.json. Each entry is one workflow category line:
- Prefixed with `[Workflows]` (ECC/minimal) or `[OMC]` (OMCC)
- Arrow `->` for sequential steps
- Comma `,` for alternatives
- Pipe `|` to separate sub-categories
- Max ~120 chars per line for readability

## Instructions

You are configuring the `companyAnnouncements` field in `~/.claude/settings.json`. This field displays workflow reminders to users at session start.

### Step 1: Determine Mode

Parse the argument `$ARGUMENTS`:
- If `--harness ecc` -> Use ECC template from `~/.claude/skills/claude-startup-config/templates/ecc.json`
- If `--harness omcc` -> Use OMCC template from `~/.claude/skills/claude-startup-config/templates/omcc.json`
- If `--harness minimal` -> Use minimal template from `~/.claude/skills/claude-startup-config/templates/minimal.json`
- If `--harness custom` -> Go to Step 3 (interactive)
- If no argument -> Go to Step 2 (auto-detect)

### Step 2: Auto-Detect Mode

Scan for installed commands and detect which harness is active:

```bash
# Global commands
ls ~/.claude/commands/*.md 2>/dev/null | xargs -I{} basename {} .md | sort

# Global skills (also exposed as slash commands)
ls -d ~/.claude/skills/*/SKILL.md 2>/dev/null | xargs -I{} dirname {} | xargs -I{} basename {} | sort

# Project commands (if in git repo)
ls .claude/commands/*.md 2>/dev/null | xargs -I{} basename {} .md | sort

# Check for OMCC plugin
ls ~/.claude/plugins/cache/oh-my-claudecode* 2>/dev/null
npm list -g oh-my-claude-sisyphus 2>/dev/null
```

**Harness Detection Logic:**
1. If OMCC plugin found -> use OMCC command mapping (namespaced `oh-my-claudecode:*` + magic keywords)
2. If ECC commands found (orchestrate, tdd, e2e, verify, learn, harness-audit) -> use ECC command mapping
3. Otherwise -> use standard command mapping

**ECC Command Categories:**

| Category | Required Commands | Chain |
|----------|------------------|-------|
| Dev/Bug | orchestrate OR (plan + tdd) | `/orchestrate feature\|bugfix "desc" -> /e2e` OR `/plan -> /tdd -> /code-review -> /verify` |
| Reproduce-First | e2e + orchestrate | `/e2e (as-is) -> /orchestrate bugfix -> /e2e (to-be)` |
| Build Error | build-fix | `/build-fix -> /verify` |
| Quality | code-review | `/code-review -> /refactor-clean -> /verify` OR `/quality-gate` |
| Eval | eval | `/eval define -> /eval check -> /eval report` |
| Docs | update-docs OR docs | `/update-docs, /update-codemaps \| Ref: /docs "lib"` |
| Learning | learn | `/learn -> /learn-eval -> /skill-create` |
| Instincts | instinct-status | `/instinct-status -> /evolve -> /promote \| /prune` |
| Session | save-session | `/save-session, /resume-session` |
| Multi-Model | multi-plan OR devfleet | `/devfleet "task"` OR `/multi-plan -> /multi-execute` |
| Loop | loop-start | `/loop-start sequential\|continuous-pr \| /loop-status` |
| Lang Review | {lang}-review | `/python-review, /go-review, ...` (list detected) |
| Lang Build | {lang}-build | `/go-build, /rust-build, ...` (list detected) |
| Lang Test | {lang}-test OR tdd | `/tdd` OR `/go-test, /rust-test, ...` (list detected) |
| Meta | harness-audit OR skill-health | `/harness-audit, /skill-health, /context-budget` |

**OMCC Command Categories:**

| Category | Commands/Keywords | Chain |
|----------|------------------|-------|
| Autonomous | autopilot | `autopilot: desc` (magic keyword) |
| Persistent | ralph | `ralph: desc` (magic keyword) |
| Team | team | `/oh-my-claudecode:team 3:executor "task"` |
| Parallel | ultrawork | `ulw tasks` (magic keyword) |
| Planning | omc-plan, ralplan | `ralplan` (magic keyword) OR `/oh-my-claudecode:omc-plan --consensus` |
| Clarify | deep-interview | `deep-interview "vague idea"` -> `omc-plan` -> `autopilot:` |
| Investigate | trace | `/oh-my-claudecode:trace "problem"` |
| QA | ultraqa | `/oh-my-claudecode:ultraqa "goal"` |
| Visual QA | visual-verdict | `/oh-my-claudecode:visual-verdict` |
| Tri-Model | ccg | `ccg "query"` (Codex+Gemini+Claude) |
| Cleanup | ai-slop-cleaner | `deslop` (magic keyword) |
| Skills | skill | `/oh-my-claudecode:skill list\|add\|remove` |
| Learn | learner | `/oh-my-claudecode:learner` |
| Session | psm | `/oh-my-claudecode:psm` (project session manager) |
| Release | release | `/oh-my-claudecode:release` |
| Diagnostics | omc-doctor | `/oh-my-claudecode:omc-doctor` |

Only include categories where at least one required command/plugin exists.

### Step 3: Interactive Custom Mode

Present the full category list from Step 2 and ask the user to select which categories to include. Then build announcements from selected categories only.

### Step 4: Generate Announcements Array

Build a JSON array where each entry is one workflow category line:
- Prefix with `[Workflows]` for ECC/standard, `[OMC]` for OMCC
- Arrow `->` for sequential steps
- Comma `,` for alternatives within a step
- Pipe `|` to separate sub-categories on the same line
- Combine related small categories into single lines (max ~120 chars per line)
- Keep compact -- users scan these quickly

### Step 5: Apply to settings.json

1. Read `~/.claude/settings.json`
2. Replace the `companyAnnouncements` array (or add it if missing)
3. Write back with proper JSON formatting (2-space indent)
4. Show the user the before (AS-IS) and after (TO-BE) diff

### Step 6: Show Project Commands (if any)

If project-level commands were detected in `.claude/commands/`, append a separate announcement line prefixed with `[Project]` showing project-specific commands grouped by purpose.

## Cross-Platform Notes

- All commands use forward slashes (Claude Code convention, not OS paths)
- No shell-specific syntax in announcement strings
- JSON uses escaped quotes for inner quotes: `\"desc\"`
- Magic keywords (OMCC) are Claude Code prompts, not shell commands -- work on all platforms
- Works identically on macOS (zsh/bash), Windows (PowerShell/cmd), Linux (bash/zsh)

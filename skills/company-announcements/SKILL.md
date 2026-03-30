---
name: company-announcements
description: Configure companyAnnouncements in settings.json with workflow cheat sheets. Auto-detects your harness (ECC, OMCC, vanilla) and displays workflow reminders at session start.
origin: claude-startup-cheatsheet
---

# Company Announcements

## When to Activate

- User asks to set up or configure session-start workflow reminders
- User mentions `companyAnnouncements` or `settings.json` workflow configuration
- User wants to see available slash commands as banners
- User installs a new harness (ECC, OMCC) and needs workflow cheat sheets
- User runs `/setup-announcements`

Configure `companyAnnouncements` in `~/.claude/settings.json` with workflow cheat sheets tailored to your installed harness and commands.

## What It Does

1. **Detects** installed slash commands in `~/.claude/commands/` and project `.claude/commands/`
2. **Categorizes** them into workflow chains (dev, debug, docs, learning, etc.)
3. **Generates** a `companyAnnouncements` block for `settings.json`
4. Cross-platform: macOS, Windows, Linux compatible

## Usage

```
/setup-announcements                      # Auto-detect installed commands
/setup-announcements --harness ecc        # ECC (Everything Claude Code) preset
/setup-announcements --harness omcc       # Oh My Claude Code preset
/setup-announcements --harness minimal    # Vanilla Claude Code preset
/setup-announcements --harness custom     # Interactive custom selection
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

## Cross-Platform Notes

- All commands use forward slashes (Claude Code convention, not OS paths)
- No shell-specific syntax in announcement strings
- JSON uses escaped quotes for inner quotes: `\"desc\"`
- Magic keywords (OMCC) work on all platforms -- they are Claude Code prompts, not shell commands
- Works identically on macOS (zsh/bash), Windows (PowerShell/cmd), Linux (bash/zsh)

<div align="center">

# claude-startup-cheatsheet

**See your workflows every time you start Claude Code.**

Never forget which slash command to use. Auto-detects your harness (ECC, Oh My Claude Code, vanilla) and displays workflow cheat sheets as session-start banners.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/cskwork/claude-startup-cheatsheet/pulls)
[![Claude Code](https://img.shields.io/badge/Claude_Code-skill-blueviolet)](https://docs.anthropic.com/en/docs/claude-code)

</div>

---

## The Problem

You installed 60+ slash commands from ECC, Oh My Claude Code, or other harnesses. But every session you blank on which command to use and in what order. You end up typing `/` and scrolling, or worse -- doing things manually that a command chain could automate.

## The Solution

This skill writes compact workflow cheat sheets into your `companyAnnouncements` setting. They show up **automatically at every session start** -- zero effort, always visible.

```
[Workflows] Dev: /orchestrate feature "desc" -> /e2e | Manual: /plan -> /tdd -> /code-review -> /verify
[Workflows] Build: /build-fix -> /verify | Quality: /quality-gate
[Workflows] Learn: /learn -> /learn-eval -> /skill-create | Session: /save-session, /resume-session
```

## Install

**One-liner (recommended):**

```bash
git clone https://github.com/cskwork/claude-startup-cheatsheet.git /tmp/cc-cheatsheet && \
  cp -r /tmp/cc-cheatsheet/skills/company-announcements ~/.claude/skills/ && \
  cp /tmp/cc-cheatsheet/commands/setup-announcements.md ~/.claude/commands/ && \
  rm -rf /tmp/cc-cheatsheet && \
  echo "Done. Run /setup-announcements in Claude Code."
```

**Manual:**

```bash
# 1. Clone
git clone https://github.com/cskwork/claude-startup-cheatsheet.git

# 2. Copy skill + command
cp -r claude-startup-cheatsheet/skills/company-announcements ~/.claude/skills/
cp claude-startup-cheatsheet/commands/setup-announcements.md ~/.claude/commands/

# 3. Run in Claude Code
/setup-announcements
```

## Usage

```
/setup-announcements                      # Auto-detect your harness
/setup-announcements --harness ecc        # Everything Claude Code preset
/setup-announcements --harness omcc       # Oh My Claude Code preset
/setup-announcements --harness minimal    # Vanilla Claude Code
/setup-announcements --harness custom     # Pick your own categories
```

The command scans your installed commands, detects which harness you're running, and writes the appropriate `companyAnnouncements` to `~/.claude/settings.json`. It shows you a before/after diff so you know exactly what changed.

## Supported Harnesses

| Harness | Commands | Preset |
|---------|----------|--------|
| [Everything Claude Code](https://github.com/affaan-m/everything-claude-code) | `/plan`, `/tdd`, `/orchestrate`, `/verify`, `/e2e`, `/learn` | `ecc` |
| [Oh My Claude Code](https://github.com/yeachan-heo/oh-my-claudecode) | `autopilot:`, `ralph:`, `ulw`, `/oh-my-claudecode:team` | `omcc` |
| Vanilla Claude Code | `/plan`, `/code-review` | `minimal` |
| Custom mix | Any combination | `custom` |

## What Gets Generated

### ECC Preset

```json
{
  "companyAnnouncements": [
    "[Workflows] Dev: /orchestrate feature|bugfix \"desc\" -> /e2e | Manual: /plan -> /tdd -> /code-review -> /verify",
    "[Workflows] Reproduce: /e2e (as-is) -> /orchestrate bugfix -> /e2e (to-be) | Build: /build-fix -> /verify",
    "[Workflows] Quality: /code-review -> /refactor-clean -> /verify | /quality-gate | /eval define|check|report",
    "[Workflows] Multi: /multi-plan -> /multi-execute | /devfleet \"task\" | /model-route \"task\" --budget low|med|high",
    "[Workflows] Docs: /update-docs, /update-codemaps | Ref: /docs \"lib\" | Loop: /loop-start sequential|continuous-pr",
    "[Workflows] Learn: /learn -> /learn-eval -> /skill-create | Instincts: /instinct-status -> /evolve -> /promote",
    "[Workflows] Session: /save-session, /resume-session | Meta: /harness-audit, /skill-health, /context-budget"
  ]
}
```

### Oh My Claude Code Preset

```json
{
  "companyAnnouncements": [
    "[OMC] Auto: autopilot: desc | Persist: ralph: desc | Parallel: ulw tasks",
    "[OMC] Team: /oh-my-claudecode:team 3:executor \"task\" | Plan: ralplan | QA: /oh-my-claudecode:ultraqa",
    "[OMC] Clarify: deep-interview \"idea\" -> omc-plan --consensus -> autopilot: desc",
    "[OMC] Investigate: /oh-my-claudecode:trace | Tri-Model: ccg \"query\" | Visual: /oh-my-claudecode:visual-verdict",
    "[OMC] Cleanup: deslop | Skills: /oh-my-claudecode:skill list | Learn: /oh-my-claudecode:learner",
    "[OMC] Session: /oh-my-claudecode:psm | Release: /oh-my-claudecode:release | Diag: /oh-my-claudecode:omc-doctor"
  ]
}
```

### Minimal Preset

```json
{
  "companyAnnouncements": [
    "[Workflows] Dev: /plan -> /code-review | Bug: /build-fix",
    "[Workflows] Docs: /docs \"lib\" | Session: /save-session, /resume-session"
  ]
}
```

## How It Works

```
/setup-announcements
        |
        v
  Scan ~/.claude/commands/
  Scan .claude/commands/
  Check for OMCC plugin
        |
        v
  Detect harness (ECC / OMCC / vanilla)
        |
        v
  Map commands -> workflow categories
        |
        v
  Generate companyAnnouncements JSON
        |
        v
  Write to ~/.claude/settings.json
  Show AS-IS vs TO-BE diff
```

## Workflow Categories

| Category | When to Use | Example Chain |
|----------|------------|---------------|
| **Dev/Bug** | New feature or bug fix | `/orchestrate feature "auth" -> /e2e` |
| **Reproduce-First** | Bug needs evidence | `/e2e (as-is) -> /orchestrate bugfix -> /e2e (to-be)` |
| **Build Error** | Compilation fails | `/build-fix -> /verify` |
| **Quality** | Pre-commit check | `/code-review -> /refactor-clean -> /verify` |
| **Docs** | After code changes | `/update-docs, /update-codemaps` |
| **Learning** | End of session | `/learn -> /learn-eval -> /skill-create` |
| **Session** | Save/resume work | `/save-session, /resume-session` |
| **Multi-Model** | Complex tasks | `/devfleet "task"` or `/multi-plan -> /multi-execute` |
| **Lang-Specific** | Language review | `/{lang}-review`, `/{lang}-build`, `/{lang}-test` |
| **Meta** | Harness health | `/harness-audit, /skill-health, /context-budget` |

## Adding Your Own Harness

Create a template file at `skills/company-announcements/templates/{name}.json`:

```json
{
  "_comment": "Your harness description",
  "_source": "https://github.com/your/repo",
  "companyAnnouncements": [
    "[Prefix] Category: /command1 -> /command2 | Alt: /command3"
  ]
}
```

Then submit a PR. We welcome templates for any Claude Code harness or custom workflow.

## Cross-Platform

Works identically on macOS, Windows, and Linux. All slash commands use forward slashes (Claude Code convention, not OS paths). Magic keywords (OMCC) are Claude Code prompts, not shell commands.

## FAQ

**Q: Will this overwrite my existing companyAnnouncements?**
A: The command shows you a before/after diff and asks for confirmation before writing. Your old announcements are displayed as AS-IS so you can compare.

**Q: What if I use both ECC and OMCC?**
A: Use `--harness custom` to pick categories from both, or run auto-detect which will prefer the harness with more installed commands.

**Q: Can I add custom lines alongside the generated ones?**
A: Yes. After running `/setup-announcements`, manually edit `~/.claude/settings.json` to add your own lines to the array. Re-running the command will replace the generated lines but you can use `--harness custom` to include your additions.

**Q: How do I update after installing new commands?**
A: Just run `/setup-announcements` again. It re-scans your installed commands each time.

## Contributing

PRs welcome! Especially:
- New harness templates (submit a JSON file)
- Workflow category improvements
- Documentation and translations

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

[MIT](LICENSE)

---

<div align="center">

**If this saves you from scrolling through slash commands, give it a star.**

</div>

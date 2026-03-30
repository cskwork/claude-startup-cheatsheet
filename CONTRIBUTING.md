# Contributing

Thanks for your interest in improving claude-startup-cheatsheet!

## How to Contribute

### Adding a Harness Template

1. Create `skills/claude-startup-config/templates/{harness-name}.json`
2. Follow this format:

```json
{
  "_comment": "Description of the harness",
  "_source": "https://github.com/org/repo",
  "companyAnnouncements": [
    "[Prefix] Category: /command -> /command | Category: /command"
  ]
}
```

3. Keep lines under ~120 characters
4. Use `->` for sequential steps, `,` for alternatives, `|` for sub-categories
5. Add the harness to the detection logic in `skills/claude-startup-config/SKILL.md`
6. Submit a PR with a brief description of the harness

### Improving Existing Templates

- Fix incorrect command names or syntax
- Add missing workflow categories
- Improve grouping or ordering for scannability

### Documentation

- Translations (README in other languages)
- Better examples or explanations
- Screenshots of announcements in action

## Guidelines

- Keep announcements compact -- users scan, not read
- Test your template by pasting the JSON into `settings.json` manually
- One PR per harness template
- Include the source repo URL in `_source` field

## Reporting Issues

Open an issue if:
- A command name is wrong for a specific harness version
- Auto-detection misidentifies your harness
- You found a settings.json formatting bug

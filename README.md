# Security audit prompt

A copy-paste prompt for Claude Code that runs a read-only security audit of any project and reports how many vulnerabilities it has, rated Critical / High / Medium / Low.

## Use it

1. Open Claude Code in the project folder you want to audit.
2. Paste the contents of [`security-audit-prompt.md`](security-audit-prompt.md).

Nothing needs editing: it detects the project, its stack and the kind of data it handles by itself.

## What you get

Reports are written outside the project, to:

```
<home>/security-audit-reports/<project-folder-name>/run-1, run-2, ...
```

Each run contains:

- `REPORT.md`: findings count by severity, urgent owner actions, each confirmed finding (file:line, exploit scenario, impact, fix, how it was verified), leads that need validation, and a comparison with the previous run.
- `attack-surface.md`: every entry point with its access rule, and where sensitive data goes.

## Safety

The prompt is read-only: it never changes the project, never touches production or real data, never prints or uses secrets, and only runs code inside an offline sandbox.

Keep reports out of git: they describe how to attack your app. This repository only tracks the prompt and this README.

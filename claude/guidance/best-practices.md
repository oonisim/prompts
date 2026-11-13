# Claude Code best practices

Choose Claude Code controls by purpose. For instruction writing, see [the shared guide][general].

## Supply project knowledge

- Use `/init` for `CLAUDE.md`; retain knowledge Claude cannot readily discover.
- Put file-specific rules in `.claude/rules/` with `paths:` filters.
- Save repeatable procedures in `.claude/skills/<name>/SKILL.md`. Set
  `disable-model-invocation: true` when only the user should start a skill.
- Disable unused external tool connections through `/mcp`.

## Verify and enforce

- Use `/code-review` for a fresh review and `/verify` to run and inspect the application.
- Hooks run actions at defined events. A `Stop` hook can enforce checks, subject to documented
  limits; test failure behavior.
- Combine permission rules with `/sandbox` to restrict access. Instructions alone cannot enforce
  security; check each control's coverage.

## Diagnose and automate

- Use `/context all` for loaded guidance, `/usage` for costs, `/permissions` for approvals.
- Use `claude -p` for scripted runs; bound unattended work with turn or spending limits.
- Isolate concurrent editing sessions in separate Git worktrees, or working directories.

## Source

Source: Anthropic's [maintained best-practices guide][source]. Check installed-version support.

[general]: ../../general/guidance/write-effective-prompt.md
[source]: https://code.claude.com/docs/en/best-practices

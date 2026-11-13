# Codex best practices

Use Codex's controls to plan, review, and organize work. For writing instructions, see
[Write effective instructions](../../general/guidance/write-effective-prompt.md).

## Before implementation

- **Plan mode:** use `/plan` for complex work to clarify requirements before editing.
- **Project rules:** use `/init` to create an initial `AGENTS.md`, then check its accuracy.
- **Configuration:** keep personal defaults separate from project settings. Use temporary command
  overrides for one-off needs; start with restricted permissions.

## During execution

- **External tools:** MCP, a standard for connecting tools and data, supplies context outside the
  repository. Add connections only for a real need.
- **Parallel work:** Git worktrees provide separate working directories for concurrent changes.
  Subagents handle bounded tasks, such as investigation or review.
- **Conversation controls:** `/status` shows session state; `/compact` condenses long context;
  `/resume` returns to saved work; `/fork` starts a separate conversation from existing context.

## Review and reuse

- **Code review:** `/review` can examine uncommitted changes, a commit, or changes against a branch.
- **Skills:** package proven procedures in `SKILL.md`; start with `$skill-creator`.
- **Scheduled tasks:** automate a reliable skill on a schedule, not an unfinished workflow.

## Source

Condensed from OpenAI's [Codex best-practices guide][source]. Check current documentation and your
installed version for command availability.

[source]: https://learn.chatgpt.com/guides/best-practices

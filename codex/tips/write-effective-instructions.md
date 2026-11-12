# Write effective Codex instructions

## Principles

**Source owners are responsible for clarifying intended meaning and supplying complete,
authoritative information. AI must not invent either.**

- **Semantic fidelity** means the source accurately expresses its owner's intended meaning.
  AI must not resolve ambiguous or contradictory statements by guessing that intention.
- **Data sufficiency** means all information needed for the task is available, explicit, and
  authoritative. AI must not fill missing information with invented facts or unsupported
  assumptions.
- **Maintain the responsibility boundary.** AI identifies gaps and asks the source owner to clarify
  or correct them. Apply the confirmed requirements; do not silently reinterpret or repair the
  source.

## General guidance

Define the result and boundaries clearly, then give Codex room to complete the work.

## Put instructions in the right place

- **Task prompt:** the outcome, inputs, constraints, and completion checks for one request.
- **AGENTS.md:** standing project rules, conventions, and required checks.
- **SKILL.md:** instructions for a skill, a reusable workflow used for matching requests.

## Seven general tips

These principles apply to all four use cases below.

1. **Define the result.** State what a successful answer or completed task should provide.
2. **Give relevant context.** Supply the facts, source material, and constraints needed for the
   work.
3. **Set boundaries.** Say what must remain unchanged and which decisions require your input.
4. **Use precise language.** Define necessary terms and resolve conflicting or ambiguous directions.
5. **Keep guidance focused.** Include requirements that affect the result; remove unrelated detail.
6. **Check what matters.** Specify how to judge correctness, completeness, and suitability.
7. **Improve from evidence.** Revise instructions when a concrete problem appears, then check
   whether the change helps without losing useful safeguards.

## Optimize for Use Cases

These four practical categories help you choose what to emphasize. They can overlap.

An agent uses tools to perform work. A skill defines a reusable procedure.

| Category | Use | Emphasize |
| --- | --- | --- |
| Conversation prompts | Explain, summarize, draft | Audience, context, format, examples |
| Agent task prompts | Carry out work with tools | Output, permissions, checks, stopping rules |
| Standing instructions | Project rules in `AGENTS.md` | Conventions, scope, checks |
| Reusable workflows | Repeat a procedure | When to use it, inputs, outputs, checks |

### Conversation prompts

- Name the audience and desired length or format.
- Request examples or sources when they help explain or support the answer.

### Agent task prompts

- Identify permitted tools and actions, the deliverable, and completion checks.
- Specify when to continue independently and when a blocked step requires a decision.

### Standing agent instructions

- Keep lasting project conventions and required checks in `AGENTS.md`.
- State when each rule applies; keep temporary task details in the task prompt.

### Reusable workflows

- Define which requests should use the skill, its required inputs, and expected output.
- Load applicable references and verify the output before reporting completion.

## Sources

This page adapts Vladislav Guzey's [Seven ways to improve Codex instructions][article],
published September 20, 2026, and OpenAI's [prompting guidance][prompting]. The general tips are
a synthesis, not the article's original seven-item list.

The use-case categories organize this page; they are not a formal product classification.
The specific tips also draw on OpenAI's [project instructions][agents] and
[skill authoring][skills] guidance. These recommendations are not measured performance guarantees.

[prompting]: https://learn.chatgpt.com/docs/prompting
[agents]: https://learn.chatgpt.com/docs/agent-configuration/agents-md
[skills]: https://learn.chatgpt.com/docs/build-skills
[article]: https://proflead.dev/posts/gpt-6-astra-codex-prompts-skills-agents-md/

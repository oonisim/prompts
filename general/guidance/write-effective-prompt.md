# Write effective prompt

## Principles

**Source owners are responsible for clarifying intended meaning and supplying complete,
authoritative information. AI must not invent but request clarification or correction.** 
The following principles guide the interaction between source owners and AI:

- **Semantic fidelity** means the source accurately expresses its owner's intended meaning.
  AI must not resolve ambiguous or contradictory statements by guessing that intention.
- **Data sufficiency** means all information needed for the task is available, explicit, and
  authoritative. AI must not fill missing information with invented facts or unsupported
  assumptions.
- **Responsibility boundary.** AI identifies gaps and asks the source authority to clarify
  or correct them. Apply the confirmed; do not silently interpret or repair the source.

## General guidance

Define the result and boundaries clearly, then give the AI room to complete the work.

### Put instructions in the right place

- **Task prompt:** the outcome, inputs, constraints, and completion checks for one request.
- **AGENTS.md:** standing project rules, conventions, and required checks.
- **SKILL.md:** instructions for a skill, a reusable workflow used for matching requests.

### Seven general guidelines

These guidelines apply to all four use cases below.

1. **Definition of done**: 
   State the acceptance criteria of what a successful result should provide.
2. **Context**: Provide relevant context to help the AI understand the situation.
3. **Focus**: State only what affects the correct result; remove unrelated information.
4. **Precise language**: Define necessary terms. Have only the facts that the source owner confirmed.
5. **Criteria**: Specify how to judge correctness, completeness, and suitability.
6. **Demarcation**: State what AI cannot decide, execute, or use.
7. **Adaptation** Revise instructions when a concrete problem appears, then check
   whether the change helps without losing useful safeguards.

## Optimize for use cases

These four practical categories help you choose what to emphasize. They may overlap.

AI uses tools to perform work. A skill defines a reusable procedure.

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
- Specify when to stop, and under what conditions.

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

Further checks against official guidance support clear instructions, relevant context, and testing
against defined success criteria:

- [OpenAI: Skills, project instructions, and task boundaries][openai-review].
- [Anthropic: Define success criteria and evaluate results][evaluations].
- [Google: Clear instructions, context, and iterative prompt improvement][google].

The source-owner responsibility boundary is this guide's policy, not a universal vendor rule.
Alignment with these references does not establish measured effectiveness for every task or model.

[openai-review]: https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra
[evaluations]: https://platform.claude.com/docs/en/test-and-evaluate/develop-tests
[google]: https://ai.google.dev/gemini-api/docs/prompting-strategies
[prompting]: https://learn.chatgpt.com/docs/prompting
[agents]: https://learn.chatgpt.com/docs/agent-configuration/agents-md
[skills]: https://learn.chatgpt.com/docs/build-skills
[article]: https://proflead.dev/posts/gpt-6-astra-codex-prompts-skills-agents-md/

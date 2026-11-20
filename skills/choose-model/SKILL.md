---
name: choose-model
description: Study the task before starting and pick the right Claude model (haiku, sonnet, opus or fable) and effort; if it is not the session's model, delegate that part to a subagent running that model. Use it when starting any non-trivial task, when a task changes nature midway (from researching to implementing, from implementing to reviewing), and when the user asks which model to use.
---

# Choose the model by task

First study the task, then choose the model, and only then work. The goal is twofold: a capable model does the hard part, and the mechanical part does not burn an expensive one.

## What can and cannot be changed

Claude Code fixes this, not the skill:

- **The session does not switch model by itself.** Only the user changes the conversation's model, with `/model`. Claude can recommend it, not do it.
- **A subagent can run another model.** The `Agent` tool accepts `model: haiku | sonnet | opus | fable` on each call. That is the real lever.
- **A subagent starts cold:** it does not see the conversation. Whatever it needs goes whole into its prompt.
- **Agents with a fixed model** already carry theirs; respect it.

## Step 1: study the task

Before touching anything, answer five questions:

1. **Is it clear what to do?** An approved plan or spec, or it still has to be decided.
2. **What happens if it goes wrong?** Undone with one click, or it touches data, security, money, production or legal text.
3. **How big is it?** One file, several in one module, or several modules.
4. **Is it mechanical or does it need judgment?** Repeating a known pattern, or choosing between paths.
5. **Is the cause known?** For a failure: located, or intermittent with no lead.

## Step 2: choose model and effort

| Model | When | Effort |
|---|---|---|
| **haiku** | Mechanical and low risk: searching and reading a lot of code, summarising logs, extracting data, renaming, formatting, checking versions, generating test data. | `low` |
| **sonnet** | Straightforward code with a clear path: implementing a planned task that does not touch security, customer data or migrations; writing tests; fixing an already located bug; refactoring to the project's style; components with the design already decided; documentation. | `medium` |
| **opus** | Security, both writing and reviewing: login, sessions, passwords, permissions, payments, customer data, database migrations. Review: requirement by requirement, security gate, final review before showing the client. Judgment: specs and plans, architecture, a failure with no clear cause, visual direction from scratch, public text with legal risk. | `high` |
| **fable** | Only when the user asks: a large cross-module refactor, deep research, hours-long tasks with many chained decisions. | `high` or `xhigh` |

Tie-break rules:

- **When in doubt, the cheaper model only if the mistake is reversible and a test would catch it.** If it touches security, data, money or production, the one above.
- **Risk beats size.** One line in authentication is `opus`; a hundred renames are `haiku`.
- **A mixed task is split.** Plan in `opus`, run the steps in `sonnet`, search in `haiku`.
- **Reviewing is `opus`; writing basic code is `sonnet`.** Do not go up "just in case": if the code does not touch security, data or migrations, it goes in `sonnet`, and the `opus` review is what watches it.
- **Subagents cost.** Gates and reviewers once per increment and only the ones that match what changed, never on every edit.

## Step 3: act

1. **Say it in one line before starting:** `Model: sonnet · effort medium · planned task, low risk.`
2. **If the chosen model is the session's own,** work here.
3. **If it is another one and the part can be isolated,** delegate with `Agent`:
   - Do not delegate just because the task is big if the chosen model is the session's: the subagent starts cold and repeating the context costs more than working here.
   - `model`: the chosen one.
   - Agent type: `Explore` to search without editing; the project's own agent if it fits; `general-purpose` otherwise.
   - A complete prompt: goal, files, the project rules that apply, and what it must return.
   - Check what comes back before accepting it.
4. **If the whole session is of another level,** recommend `/model` in one line, without blocking.
5. **If the result fails verification** (tests, lint, review), repeat that part once with the model above before trying again.

## When not to delegate

- **Short tasks:** if it fits in a few tool calls, doing it here costs less than explaining it.
- **Work that depends on the conversation:** if the subagent would need to read half the conversation, it stays here.
- **Changes the user is watching live:** let them see it happen.

## Without a skill

`/model opusplan` already does a fixed split: `opus` in plan mode and `sonnet` when executing. This skill refines it: it chooses per task and also uses `haiku`.

Model names and their strengths change; update the table when they do.

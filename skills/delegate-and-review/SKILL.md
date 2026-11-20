---
name: delegate-and-review
description: Have another coding agent write the code while Claude prepares the brief and reviews the result. Use it when the user wants a different agent or model to implement, when a quota runs out, or when they ask "let the other agent write it and you review".
---

# Delegate and review: one agent writes, Claude reviews

Another agent implements; Claude prepares the brief and reviews what it leaves. Nobody corrects their own work: the author and the reviewer are different.

This skill does not install or configure the other agent. If the user's setup is not ready, say what is missing and stop.

## Flow

### 1. Claude writes the brief

A file outside the repository (the session's scratch folder), with:

- The goal in one sentence and the expected result.
- The files it may touch and the files it may not.
- The project rules that apply, cited by section name, not copied whole.
- Acceptance criteria: which test or command must pass.
- What not to do: no `git add`, `commit` or `push`; do not touch secrets; do not change dependencies without asking.

Keep the brief small: one task from the plan, not a whole module. If the diff would pass about 100 lines, split it.

### 2. The other agent implements

The user launches it in the project folder and gives it the brief. Claude does not run an interactive terminal session it cannot follow. A non-interactive command is run by Claude only if the user authorises that exact command, because it executes project code and sends it to an external model.

### 3. Claude reviews, without trusting the agent's summary

1. `git status` and `git diff` to see what really changed.
2. Run the acceptance criteria (tests, lint, build if the frontend changed) and show the output, not a summary from memory.
3. Run the project's reviewer for conventions and, if backend or security is touched, the security reviewer.
4. Read texts, tests and documentation first. If text was removed or a test was weakened, it is a failure even if it compiles.
5. Check that no file outside the brief was touched.

Verdict: approved, or a list of concrete changes for a second round. Claude does not fix the other agent's code on its own unless the user asks.

### 4. When the quota or the model fails

Report the literal error message. Do not invent a change of provider; propose checking limits or adding a fallback and let the user decide.

## Known limits

- A cheaper or free model can be weaker than Claude. The review is the control, not a formality.
- The other agent does not see this conversation: everything it needs goes in the brief.
- No review replaces a test. If no test covers the change, record it as debt instead of approving it.

## Rules that always apply

- Git is the user's: Claude gives the commands and never runs `add`, `commit` or `push`.
- No secret in project files or in the chat.
- One change per brief.
- If you cannot explain a change in two sentences, it does not go in.

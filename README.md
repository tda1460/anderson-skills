# anderson-skills

Global development skills for all anderson-apps.com projects.

These are process standards used by Claude during development sessions — fetched on demand, not at session start. Each skill governs a specific moment in the development lifecycle.

---

## Skills

| Skill | File | Purpose |
|---|---|---|
| Developer | [developer.md](developer.md) | Coding discipline — diagnosis-first, evidence-based fixes, no self-certification |
| QA | [qa.md](qa.md) | Verification standards — what "done" actually means, evidence requirements |
| PM | [pm.md](pm.md) | Session and scope discipline — requirements clarity, one-change-at-a-time, budget awareness |
| Code Review | [code-review.md](code-review.md) | Pre-commit gate — verification, correctness, scope, regression, documentation |
| Jigsaw Code Review | [jigsaw-code-review.md](jigsaw-code-review.md) | Project-specific additions for the jigsaw puzzle app |

---

## Design Principles

**As-needed, not upfront.** Skills are fetched via `web_fetch` at the moment they are relevant — before a bug fix, before a commit, at task intake. They are not read at session start on every project.

**Process, not state.** Project state (what exists, fragile areas, deploy commands) lives in each project's `PROJECT-CONTEXT.md`. These skills define *how* to work, not *what* the project is.

**Evidence over reasoning.** The core principle across all skills: showing that something works is different from reasoning that it should work. These skills enforce the distinction.

---

## Usage

In any project's `PROJECT-CONTEXT.md`, skills are referenced by raw URL and fetched on demand:

```
web_fetch https://raw.githubusercontent.com/tda1460/anderson-skills/main/developer.md
```

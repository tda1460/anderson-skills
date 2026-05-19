---
name: code-review
description: >
  Global pre-commit code review skill. Run this before every commit on every project.
  Triggers on: "ready to commit", "let's commit", "push this", "commit and push",
  or any time git commit is about to be called. Covers: correctness, verification status,
  documentation, regression risk, and platform safety. Project-specific checks are
  defined in each project's PROJECT-CONTEXT.md under the Skills section.
  Never skip this — even for small changes.
---

# Global Code Review Skill

## How to Use This Skill

1. Read this file
2. Read the project-specific checklist from PROJECT-CONTEXT.md → Skills section
3. Run through every item below
4. If any ❌ blockers: fix them, then re-review from the top
5. Only commit when all items are ✅ or explicitly ⚠️ (noted but accepted)

Report format:
```
PRE-COMMIT REVIEW — [project] [version if applicable]

[section]: ✅ / ⚠️ / ❌ [note]
...

VERDICT: [Ready to commit | N blockers — fix first]
```

---

## Section 1: Verification Status

This is the most important section. Code that hasn't been verified shouldn't be committed.

- [ ] Was the verification plan (from the QA skill) executed?
- [ ] Is there actual evidence (output, logs, response) that the change works?
- [ ] Was the specific changed behavior exercised and confirmed?
- [ ] Was the original bug scenario re-tested (for bug fixes)?
- [ ] Did Tom confirm the behavior, or is this self-certified?

**If any item is "no": do not commit. Verify first.**

Self-certified "it looks right" is a ❌ blocker.

---

## Section 2: Correctness

- [ ] Does the code do what was approved and discussed?
- [ ] Are there any obvious logic errors on inspection?
- [ ] Are error cases handled — or at minimum, not silently swallowed?
- [ ] If async: are errors caught?
- [ ] If database: are queries safe (no injection risk from unsanitized input)?
- [ ] If touching fragile areas (see PLATFORM-CONTEXT.md): extra scrutiny applied?

---

## Section 3: Scope Compliance

- [ ] Only the approved change is in this commit
- [ ] No unrelated changes bundled in
- [ ] No "while I was in here" additions that weren't discussed
- [ ] If scope expanded mid-session: Tom approved the addition

Bundled unrelated changes make debugging harder and violate the one-change-at-a-time rule.

---

## Section 4: Regression Risk

- [ ] What else could this change affect? Was it checked?
- [ ] If shared infrastructure was touched (nginx, apps.config.json, shared DB): all dependent apps considered
- [ ] If an API endpoint was modified: any consumers of that endpoint still work?
- [ ] If a database schema changed: queries that read from that table still work?
- [ ] For anderson-platform: PLATFORM-CONTEXT.md dependency registry reviewed?

---

## Section 5: Documentation

- [ ] PROJECT-CONTEXT.md updated
  - Version bumped (if project uses versioning)
  - What was done this session noted
  - Any new fragile areas or decisions documented
  - Active issues / resolved issues updated
- [ ] If API surface changed: PLATFORM-CONTEXT.md updated
- [ ] If platform behavior changed: BUILDER-CHANGELOG.md entry added
- [ ] Commit message is descriptive (not "fix" or "update") — says what changed and why

---

## Section 6: Platform Safety (anderson-platform projects)

Applies when the commit is to anderson-platform or touches shared Pi infrastructure.

- [ ] `src/exec.js` spawn still has `stdio: ['ignore','pipe','pipe']` — not removed
- [ ] anderson-platform is NOT in pm2 — still running under systemd
- [ ] nginx config: full file was read before any write; no partial overwrites
- [ ] apps.config.json: existing entries not removed or renamed without discussion
- [ ] PACE .env path not changed
- [ ] If backend self-deploy: async flag used; 524 response is expected and not treated as failure

---

## Section 7: Project-Specific Checks

Read PROJECT-CONTEXT.md → Skills section for project-specific items.  
These supplement (not replace) the global checklist above.

Common project-specific items typically include:
- Version bump requirements and format
- Test commands specific to the project
- Known fragile areas beyond the global list
- Deployment verification steps specific to the project

---

## Commit Message Standard

Format: `[type]: [what changed] — [why, if not obvious]`

Types:
- `feat:` — new capability
- `fix:` — bug fix
- `refactor:` — restructure without behavior change
- `docs:` — documentation only
- `chore:` — tooling, config, housekeeping

Examples:
- `feat: add pause/resume to stock-trader dashboard`
- `fix: deploy log not persisting on self-deploy — use pre-restart hook`
- `docs: update PLATFORM-CONTEXT.md with Session 5 endpoints`

Bad examples (do not use):
- `update`
- `fix bug`
- `changes`
- `session 7`

---

## Quick Reference Card

```
MUST PASS BEFORE COMMIT:
✅ Verification done — actual evidence, not reasoning
✅ Approved scope only — no extras
✅ Regression checked
✅ PROJECT-CONTEXT.md updated
✅ Platform safety checks (if applicable)
✅ Descriptive commit message

IF ANY ❌: Fix it. Re-review. Then commit.
```

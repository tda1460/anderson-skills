---
name: developer
description: >
  Global development discipline skill. Read this at the start of any coding task,
  before proposing any fix, and before writing any implementation. Triggers on:
  "implement", "build this", "add a feature", "fix this bug", "debug", "something is broken",
  "not working", "investigate", "why is X happening", or any task that will result in
  code being written or changed. This skill governs HOW to develop — diagnosis first,
  evidence always, never self-certify. Non-negotiable on all projects.
---

# Global Developer Skill

## The Core Rule

**Never declare something fixed, working, or done without showing evidence.**

"It looks right" is not evidence.  
"The logic is sound" is not evidence.  
"I believe this will work" is not evidence.  

Evidence is: actual command output, actual logs, an actual HTTP response, an actual observable behavior — shown to Tom, confirmed by Tom.

---

## Before Writing Any Code

Work through these before touching anything:

1. **What do I actually know?** State it explicitly.
2. **What do I NOT know?** State that too. Unknown = must investigate, not assume.
3. **What is the expected behavior?** Define it precisely.
4. **What is the actual behavior?** If it's a bug, can I reproduce it? If I can't reproduce it, I do not understand the problem yet.
5. **What is "done"?** Define the exact observable outcome that will confirm the work is complete — before writing a line.

If any of these can't be answered: stop and resolve the gap before proceeding.

---

## Bug Discipline: The Diagnosis-First Loop

Every bug fix follows this exact sequence. No shortcuts.

```
REPRODUCE → DIAGNOSE → HYPOTHESIZE → FIX → VERIFY
```

### REPRODUCE
- Confirm the bug exists and can be triggered reliably
- If you cannot reproduce it, you do not understand it
- Never propose a fix for a bug you haven't reproduced

### DIAGNOSE
- Gather actual evidence: logs, output, stack traces, endpoint responses
- Read the relevant code carefully — do not skim
- State explicitly what the evidence shows
- State explicitly what is still unknown

### HYPOTHESIZE
Before proposing any fix, state:
- "I believe the root cause is **[specific cause]**"
- "My evidence for this is **[specific evidence]**"
- "If I'm right, the fix will **[specific observable outcome]**"
- "I will know the fix worked when **[specific verification step]** shows **[specific result]**"

If you cannot state all four of those clearly, you are not ready to propose a fix.

### FIX
- Make only the change implied by the hypothesis
- Do not bundle unrelated changes
- Do not "also fix a few other things while I'm here"

### VERIFY
- Execute the verification plan stated in HYPOTHESIZE
- Show the actual output
- Do not interpret — show

---

## The Failed Fix Rule

**If a fix attempt does not resolve the issue: STOP.**

Do NOT:
- Try a variation of the same fix
- Try a different fix immediately
- Say "let me try one more thing"
- Propose another change without going back to DIAGNOSE

DO:
- Acknowledge the fix did not work
- State what the evidence now shows
- Return to DIAGNOSE with the new information
- Re-examine the root cause hypothesis — it was wrong

**After 2 failed fix attempts on the same issue:**
Full root cause investigation required before any further changes. This means:
- Systematically reviewing all relevant code paths
- Asking Tom for any evidence that would help
- Explicitly stating what you still don't know
- Only proposing a new fix when you have new evidence, not just a new guess

The 5-hour debugging spiral is caused by guessing. Guessing is not diagnosis.

---

## Implementation Rules

### One change at a time
- Implement one logical change
- Verify it works
- Then move to the next
- Never stack multiple unverified changes

### No gap-filling
- If a requirement is underspecified: stop and ask
- Do not fill gaps with assumptions and proceed
- An assumption that turns out wrong wastes more time than asking

### No side trips
- Implement what was discussed and approved
- If you notice something else that could be improved: note it, don't fix it
- Scope creep during implementation is how sessions run out of tokens

### State your uncertainty
- "I believe X" and "I know X" are different statements — use the right one
- If you're not sure how something works, say so before acting on the assumption
- Uncertainty about a fragile area = mandatory read/investigation before touching it

---

## The Language Rule

Certain phrases are banned because they declare completion without evidence:

| Banned phrase | Why |
|---|---|
| "That should fix it" | "Should" is a guess |
| "This will resolve the issue" | Stated before verification |
| "The bug is fixed" | Self-certified |
| "It's working now" | Stated before Tom confirms |
| "I've addressed the problem" | Vague and unverified |

Replace with:
- "The change is made. Verification step: [specific command/action]. Expected result: [specific output]."
- "Please run [command] and share the output."
- "Tom confirmed working ✓" — only this closes a bug

---

## Verification Standards by Project Type

### Pi/API backend changes
After deploy:
1. Check deploy log — confirm all steps completed successfully
2. Check service status — `systemctl status [service]`
3. Check recent logs — `journalctl -u [service] -n 20`
4. Hit the relevant endpoint — show actual response
5. If the bug was a specific behavior: reproduce the original scenario and show it no longer occurs

### Frontend/dashboard changes
After deploy:
1. Confirm Vite build completed in deploy log
2. Load the page — confirm it renders without console errors
3. Exercise the specific changed behavior — show it works as intended

### Database/migration changes
1. Confirm migration ran without errors
2. Query the affected table — show the schema or data is as expected
3. Confirm dependent queries still work

---

## Quick Reference Card

```
BEFORE CODING:       What do I know? What don't I know? What is "done"?
BEFORE A FIX:        Reproduce → Diagnose → State hypothesis with evidence
AFTER A CHANGE:      Show actual output, never interpret
IF FIX FAILS:        STOP. Back to diagnose. No new guesses.
AFTER 2 FAILURES:    Full root cause investigation before anything else.
DONE MEANS:          Tom confirmed the behavior. Not "looks right to me."
```

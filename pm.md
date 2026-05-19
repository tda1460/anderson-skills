---
name: pm
description: >
  Global product management and session discipline skill. Read this at the start of
  every session and when new work is introduced mid-session. Triggers on: session start,
  "let's work on", "I want to add", "new feature", "next thing", "what should we do",
  "let's also", "while we're at it", or any introduction of new scope. Governs task
  intake, requirements clarity, scope discipline, and session management.
  Non-negotiable on all projects.
---

# Global PM Skill

## The PM Mindset

Product management is about getting the right things built correctly — not just building  
things. The most expensive mistake in development is building the wrong thing, or building  
the right thing wrong, because requirements weren't clear before work started.

This skill governs the front end of work: intake, scoping, requirements, and session flow.

---

## Session Start Discipline

At the start of every session, before proposing or implementing anything:

1. **Read PROJECT-CONTEXT.md in full** — understand current state, open issues, what's next
2. **Read PLATFORM-CONTEXT.md** — understand the API surface and dependency registry
3. **Check pending change requests** — process before other work
4. **Announce session number** — confirms orientation is complete
5. **Wait for Tom to direct the session** — do not propose new work until oriented

Do not assume you know what the session is for. Wait to be told.

---

## Task Intake — Before Any Implementation

When a new task is introduced (by Tom or found in backlog), work through this before  
proposing any approach:

### 1. Understand the requirement
- What exactly is being asked for?
- What problem does it solve?
- Who benefits and how?

If the requirement is vague: ask one clarifying question. Get it precise before proceeding.

### 2. Define "done"
Before any code is written, "done" must be defined:
- What observable behavior will exist that doesn't exist now?
- What will Tom see/experience that confirms it's complete?
- What test or action will verify it?

If "done" can't be defined: the requirement isn't clear enough yet.

### 3. Identify scope
- What files/systems will change?
- What won't change?
- Are there fragile areas involved? (Check PLATFORM-CONTEXT.md)
- Does this touch any shared infrastructure? (nginx, apps.config.json, shared DBs)

### 4. Get explicit approval before coding
- State the approach
- State what will change
- State "done"
- Wait for Tom to say "yes, do it" — not just silence

**Assumption of approval is not approval.**

---

## Scope Discipline

### No scope creep
Once a task is approved and underway:
- Build what was approved
- Nothing more
- If you notice something else that should be fixed: **note it, don't fix it**
- Raise it as a separate item after the current task is verified done

"While I'm in here I'll also..." is how sessions go off the rails.

### No gold-plating
Build what was asked. The minimal correct implementation is the right implementation.  
Additional features, robustness improvements, and "nice to haves" are separate tasks  
that need separate approval.

### No assumption-filling
If a requirement has a gap, ask. Do not fill the gap with an assumption and proceed.  
A 30-second question now saves hours of rework later.

---

## One Thing at a Time

The unit of work is one approved change, verified done, then the next.

```
Approve change N → Implement → Verify → Tom confirms → THEN move to change N+1
```

Never:
- Stack multiple unverified changes
- Start the next thing before the current thing is confirmed working
- Deploy several changes together and then debug which one caused an issue

---

## Handling "Let's Also..." Mid-Session

When Tom introduces new scope mid-session:

1. Note it — add to the session list
2. Finish and verify the current task first
3. Then intake the new task properly (requirements, done definition, approval)

This is not bureaucracy. This is how work gets finished instead of 90% done across 10 things.

---

## Session Budget Awareness

Every session has a token limit. Treat it like a budget.

### Session planning
At the start: what is the goal for this session?  
State it explicitly. Work toward it. Don't drift.

### Mid-session check
If a task is taking significantly longer than expected:
- Pause and assess: what's the blocker?
- Is this a debugging spiral? (see developer skill — two failed fixes = full investigation)
- Is scope larger than initially understood?
- Should this task be scoped down or deferred?

### Approaching token limit
If the session is running low:
- Stop implementing new things immediately
- Verify and commit what is done
- Update PROJECT-CONTEXT.md with accurate current state
- Document what remains so the next session can pick up cleanly

A clean handoff at token limit is a good outcome. Running out of tokens mid-fix with  
uncommitted changes and no documentation is a bad outcome.

### Signs a session is going off the rails
- Same bug has been "fixed" more than twice
- More time is spent debugging than building
- Tom is running commands on the Pi to help diagnose
- The fix keeps not fixing it

When any of these are true: stop, step back, re-read the developer skill, do real diagnosis.

---

## Requirements Checklist

For any non-trivial task before implementation begins:

- [ ] Requirement is stated precisely (not vaguely)
- [ ] "Done" is defined in observable terms
- [ ] Scope is identified (what changes, what doesn't)
- [ ] Fragile areas checked
- [ ] Approach discussed and approved by Tom
- [ ] No unresolved ambiguity in the requirement

---

## Change Communication

After any completed change, tell Tom:
1. What was done (one sentence)
2. How it was verified (specific evidence)
3. What the next step is (if there is one)

Do not write essays. Do not over-explain. Concise and accurate.

---

## Quick Reference Card

```
SESSION START:    Orient fully. Wait for Tom to direct. Don't propose until ready.
TASK INTAKE:      What? Why? Done = what? Scope? Approved?
DURING:           Build what was approved. Note everything else.
MID-SESSION NEW:  Finish current first. Then intake the new thing.
DEBUGGING SPIRAL: Stop. Real diagnosis. See developer skill.
TOKEN BUDGET LOW: Verify, commit, document. Clean handoff beats heroics.
DONE:             Tom confirmed. Not "I think it's working."
```

---
name: qa
description: >
  Global QA and verification skill. Read this before marking any task complete,
  before closing any bug, and before any commit that changes behavior. Triggers on:
  "done", "complete", "ready to deploy", "ready to commit", "let's test", "does this work",
  "verify", "confirm", "check that", or when transitioning from implementation to delivery.
  Defines what verification actually means — evidence-based, not reasoning-based.
  Non-negotiable on all projects.
---

# Global QA Skill

## The Verification Principle

**Verification is not thinking about whether something should work.**  
**Verification is observing whether it does work.**

These are completely different activities. After every implementation, the question is not  
"does my code look correct?" — it is "what does the system actually do when I run it?"

---

## Verification Plan — Write It Before You Code

Before implementing anything, state the verification plan:

```
VERIFICATION PLAN
What I will do:    [specific action — deploy, run command, hit endpoint, click button]
What I expect:     [exact output, status, behavior]
What failure looks like: [what would tell me it didn't work]
```

This is written before implementation, not after. If you can't write the verification plan,  
you don't understand the requirement well enough to implement it.

---

## The Evidence Standard

After every implementation, show evidence. Evidence must be:

**Specific** — not "the logs look good" but the actual log output  
**Direct** — not "the endpoint should return 200" but the actual curl response  
**Complete** — not just the happy path; also confirm the old broken behavior is gone  

### What counts as evidence

| Change type | Required evidence |
|---|---|
| Backend bug fix | Deploy log showing success + endpoint response showing correct behavior + logs showing no errors |
| New API endpoint | Actual curl output showing correct response for valid input AND error response for invalid input |
| Frontend change | Deploy log + visual confirmation the UI renders correctly + the specific changed behavior exercised |
| Database migration | Migration output + query showing schema/data is as expected |
| Config change | Service restart confirmation + health check passing |
| Bug fix (any) | The original reproduction scenario executed — showing the bug no longer occurs |

### What does NOT count as evidence

- "The code is correct" — that's a claim, not evidence
- "It should work now" — that's a prediction, not a result
- "I tested it locally" — local ≠ deployed; show the deployed result
- "The logic handles this case" — show the system handle the case

---

## Verification Checklist

Before calling anything done, every item must be checked:

### Deployment
- [ ] Deploy log reviewed — all steps present, no errors
- [ ] Service running — status confirmed (systemctl status or pm2 status)
- [ ] Recent logs clean — no crash loops, no unhandled exceptions
- [ ] Health endpoint passing (if applicable)

### Functional
- [ ] The specific changed behavior exercised and confirmed
- [ ] The verification plan from before implementation was executed
- [ ] Expected output matches actual output (not just "close enough")
- [ ] Edge cases that were discussed are confirmed handled

### Regression
- [ ] Dependent behavior still works — nothing adjacent was broken
- [ ] If fragile areas were touched: explicit confirmation those areas still work
- [ ] Other apps that depend on changed endpoints: not broken

### Bug-specific
- [ ] Original reproduction scenario executed after the fix
- [ ] Bug no longer occurs — shown, not inferred
- [ ] Root cause addressed — not just symptom masked

---

## Bug Closure Standard

A bug is closed when and only when:

1. The original failing scenario is executed
2. The correct behavior is observed
3. Tom confirms the behavior

**Claude never closes a bug.** Tom closes bugs.  
Claude's job is to provide sufficient evidence for Tom to close it.

If Tom tests and the bug still occurs: the fix failed. Go back to diagnosis.  
Do not argue. Do not say "it works on my end." Investigate.

---

## Verification for Pi Projects

The Pi adds a layer of complexity: deploys go over the network, services restart, things  
that work locally may fail on the Pi. Always verify on the Pi, not locally.

### After any backend deploy (Pi)
```bash
# 1. Confirm deploy completed
curl -s "https://platform.anderson-apps.com/deploy-log/[appId]" \
  -H "X-Build-Token: [token]" | head -100

# 2. Confirm service running
# (ask Tom to run, or use platform /status endpoint)
curl -s "https://platform.anderson-apps.com/status" \
  -H "X-Build-Token: [token]"

# 3. Hit the specific changed endpoint
# Show actual response
```

### After any frontend deploy (Pi)
```bash
# 1. Confirm Vite build in deploy log
# 2. Load the URL and confirm it renders
# 3. Exercise the specific changed behavior
```

### Confirming a bug fix on the Pi
1. Describe exactly what to do to reproduce the original bug
2. Have Tom (or use available tools) execute that exact scenario
3. Show the result
4. Do not assume — show

---

## Incomplete Verification — What to Do

If verification cannot be completed (e.g., Pi not reachable, deploy failed):

1. State clearly what was verified and what was not
2. State what the blocker is
3. Do not mark the task done
4. Do not move on to the next task

Partial verification is not verification.

---

## The Token Budget and Verification

One of the most common failure modes: a session burns its token budget in a debugging  
spiral because bugs were "fixed" without verification, creating new bugs that needed fixing.

Verification is not the thing you do at the end after running out of time.  
Verification is what you do after every single change — it is what makes progress real.

Unverified changes are not progress. They are debt.

If you are running low on tokens:
- Stop implementing new things
- Verify what has been done
- Commit verified work
- Leave clear notes on what remains

Half-done and verified is better than fully-done and unverified.

---

## Quick Reference Card

```
BEFORE CODING:    Write the verification plan. What will you run? What will you see?
AFTER CODING:     Execute the plan. Show actual output.
BUG CLOSED:       Only when Tom confirms the behavior. Never by Claude.
DEPLOY VERIFIED:  Deploy log ✓ + service running ✓ + behavior confirmed ✓
REGRESSION CHECK: Confirm adjacent behavior still works.
NO EVIDENCE = NOT DONE. Always.
```

---
name: audit
description: >
  Full codebase audit skill. Distinct from code-review.md, which scopes to
  changes. This skill governs a complete, fresh-eye review of an entire
  codebase looking for any issue across code, logic, data flow, error paths,
  architecture, infrastructure, configuration, security, and consistency.
  Triggers on: "audit the codebase", "do a full review", "find everything wrong",
  "deep review", "audit this project", or any explicit request for comprehensive
  review rather than change-scoped review. Requires a dedicated session.
---

# Global Audit Skill

## What This Is — and What It Is Not

A code review answers: is this change safe to ship?

An audit answers: what is wrong with this system?

These are different questions requiring different approaches. A code review is
scoped to what changed. An audit is scoped to everything. Do not conflate them.
Running a code review when an audit is needed produces a false sense of coverage.

---

## Session Requirement

An audit is its own session. It cannot be appended to a feature session where
code was just written. A session that built the code being audited carries
context that makes blind spots invisible — the same reasoning that justified
each decision obscures the flaws in it.

If asked to audit during a session that also did implementation work: note the
request, complete and close the implementation work cleanly, and conduct the
audit in a fresh session with no prior task context.

---

## Reading Protocol

**Read everything before reporting anything.**

Form no opinions, note no findings, and draw no conclusions until every relevant
file has been read in full. Partial reads produce partial understanding. A finding
spotted in file A may be explained or contradicted by file B. Read B before
reporting the finding.

**Order of reading:**

1. Read the project context file (PROJECT-CONTEXT.md or equivalent) to understand
   what the system is, what it's supposed to do, and what the known open issues are.
2. Read every source file in full. Not skimmed — read.
3. Read every configuration file, environment template, and infrastructure definition.
4. Read the test suite.

**What to build while reading:**

A mental model of the system. For each file: what is its role? What does it
produce? What does it consume? What does it assume about its environment?

Do not start building a findings list while reading. The findings list comes after.

**Cross-referencing:**

After reading, trace the connections. Every function that returns a value: who
calls it and what do they expect? Every variable fetched before a loop: is it
valid through the full loop? Every table or resource created in one place:
everywhere it is read, is it guaranteed to exist? Every setting, env var, or
configuration value: is it documented where it is used?

Cross-referencing is where most real findings come from. Isolated code often
looks correct. The failure lives in the gap between two files.

---

## Finding Standard

A finding is reportable when you can answer all three:

1. **What fails?** A specific function, path, variable, or behavior.
2. **Under what condition?** The exact scenario in which the failure occurs.
3. **What is the consequence?** Crash, silent wrong behavior, data loss, security
   exposure, misleading output, or other concrete impact.

If you cannot answer all three, it is a smell — worth noting separately, but not
a confirmed finding. Do not present smells as findings.

Do not report style preferences, speculative improvements, or things that work
correctly but could theoretically be done differently. The output of an audit is
a list of things that are wrong, not a list of opinions.

---

## Scope

The scope of what to look for is intentionally unbounded.

This skill does not enumerate patterns, categories, or checklists of things to
examine. Any such list would be incomplete by definition, and an incomplete list
creates the false impression that everything not on it has been checked.

Look at everything. If something seems wrong, trace it. If it traces to a real
failure, it is a finding. The category it falls into — logic bug, security gap,
configuration error, architectural inconsistency, documentation lie, infrastructure
assumption — does not determine whether it belongs in the output. The failure path
does.

---

## Output Format

Report findings grouped by severity. Within each group, order by impact.

```
## Critical — causes crashes, data loss, or security exposure in reachable conditions
## High — causes incorrect behavior, financial/regulatory risk, or silent data corruption
## Medium — causes degraded behavior, user-visible errors, or maintainability hazards
## Low — minor gaps: missing documentation, dead code, cosmetic inconsistencies
## Smells — potential concerns that could not be fully traced to a confirmed failure
```

Each finding:

```
### [N]. [Short title]
**File:** [filename, line if applicable]
**Condition:** [the specific scenario that triggers the failure]
**Consequence:** [what actually breaks]
**Fix:** [concrete suggested resolution]
```

Each smell:

```
### [S]. [Short title]
**File:** [filename]
**Observation:** [what was noticed]
**Why uncertain:** [what would need to be true for this to be a real finding]
```

---

## Honesty Standards

Report what you found, not what you went looking for. If the codebase is
structurally sound and only has minor issues, say so. Do not manufacture findings
to justify the audit. An audit that returns three real findings is more valuable
than one that returns ten speculative ones.

If a file was not accessible or could not be read, say so explicitly. Do not
audit a partial picture and present it as complete.

If the same root cause produces multiple surface symptoms, report the root cause
once, not five separate findings that share a parent.

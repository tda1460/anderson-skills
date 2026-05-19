---
name: jigsaw-code-review
description: >
  Code review skill for the jigsaw-puzzle-app project. Use this skill BEFORE
  every commit, whenever code changes have been made, or when something breaks.
  Triggers on: "review this", "check before commit", "pre-commit review",
  "audit the code", "anything look wrong", "catch issues", or any time you
  are about to call `git commit` on jigsaw-puzzle-app.html or server.js.
  Covers: pre-commit checklist, subsystem-specific checks (audio, rendering, UI),
  general JS/HTML quality, and full file audit. Always use this skill before
  committing — even for small changes. Never skip it.
---

# Jigsaw Puzzle App — Code Review Skill

## How to use this skill

1. Read the change(s) about to be committed (or the full file for an audit)
2. Run through the **Pre-Commit Checklist** — all items must pass before `git commit`
3. Run the relevant **Subsystem Checks** for whatever was touched
4. Run **General Quality Checks**
5. Report findings clearly: ✅ pass, ⚠️ warning (won't break things, but should fix), ❌ blocker (must fix before commit)
6. If any ❌ blockers exist, fix them first, then re-review

---

## Pre-Commit Checklist

These must all pass. No exceptions.

### Version bump (REQUIRED on every commit)
- [ ] `<!-- APP VERSION: X.XX -->` near line 2 of the HTML — updated
- [ ] `const v = 'vX.XX'` in the JS init block — updated to match
- [ ] Both version strings match each other exactly
- [ ] Version is higher than the previous commit (never lower, never same)

### PROJECT-CONTEXT.md
- [ ] Updated with what was done this session
- [ ] "Current app version" in the header matches the new version
- [ ] "Last updated" date is current
- [ ] If something was fixed: moved from 🔴 ACTIVE ISSUE to ✅ RESOLVED
- [ ] If a new issue was discovered: added to ACTIVE ISSUES

### Delivery
- [ ] File copied to `/mnt/user-data/outputs/jigsaw-puzzle-app.html`
- [ ] `present_files` tool called so Tom can download it
- [ ] `git push origin main` executed after commit

---

## Subsystem Checks

Read the relevant reference file for deep checks on touched subsystems.
See `references/audio.md`, `references/rendering.md`, `references/ui.md`.

### Quick subsystem triage — check which files apply:

**Audio touched?** → Read `references/audio.md`
Look for: AudioContext creation, playSnap, _warmSound, _doSnapPlay, _snapBuf2,
_ac2, resume(), decodeAudioData, _SNAP_B64, capture-phase listeners.

**Rendering touched?** → Read `references/rendering.md`
Look for: drawPieces, bakePieceBevels, drawImage, shadowBlur, offscreen canvas,
group rendering, solo shadow cache, zoom, worldPW/PH, pathScaleX/Y.

**UI / navigation touched?** → Read `references/ui.md`
Look for: navigate(), hash routing, gallery views, diff modal, PIN flow,
sort bar, tag filters, upload flow, startPuzzle(), exitToGallery().

---

## General Quality Checks

### JavaScript
- [ ] No `var` — use `const` or `let`
- [ ] No `console.log` left in committed code (unless it's a permanent debug tool)
- [ ] No `alert()` or `debugger` statements
- [ ] Async errors caught — every `fetch()`, `decodeAudioData()`, `resume()` has `.catch(() => {})`  
- [ ] No silent swallows of meaningful errors (empty catch on critical paths = ⚠️)
- [ ] No new global variables without a comment explaining why they're global
- [ ] Event listeners added in pairs — if you add a listener, check it's cleaned up if the element is ever removed
- [ ] `try/catch` around any code that touches DOM elements that might not exist

### HTML/CSS
- [ ] No inline `style="..."` for things that belong in the `<style>` block
- [ ] z-index values are consistent with the established stack (see `references/ui.md` for z-index map)
- [ ] No new `<script src="...">` external dependencies added without discussion

### Architecture rules (hard lessons — never violate)
- [ ] Board = full image: no code changes the image, crops it, resizes it, or pads it **unless** it's the established center-crop AR logic in `initPuzzle`
- [ ] Tab geometry uses `G.worldPW/PH * zoom` as reference — never per-piece dimensions
- [ ] Group shadow: groups render to offscreen canvas first, then composite with single shadow (no shadow bleed across seams)
- [ ] Seam fix: full-board `drawImage` clipped to combined group shape — not per-piece clipped draws
- [ ] Cutout format detection: `buildPiecesFromCutout` checks `data.width` to distinguish cutouts2024 format
- [ ] `G.cropX/Y` defaulted via `G.cropX||0` in any ghost draw code

### One-change-at-a-time rule
- [ ] If multiple subsystems were changed, verify each change is independent and can be individually reverted
- [ ] If a change touches both `jigsaw-puzzle-app.html` AND `server.js`, flag it — server restart will be needed

### server.js changes
- [ ] If `server.js` was changed: note in commit message and PROJECT-CONTEXT.md that server restart is required
- [ ] Restart command: `launchctl kickstart -k gui/$(id -u)/com.jigsaw.server`

---

## Full File Audit Mode

When asked to audit the full file (not just a diff), additionally check:

- [ ] Version comment at line 2 matches JS constant
- [ ] No duplicate function definitions (search for `function foo(` twice)
- [ ] No orphaned variables declared but never used
- [ ] `G` object has all expected fields initialized in `initPuzzle` / `resetGame`
- [ ] All `canvas.addEventListener` calls have corresponding cleanup or are on persistent elements
- [ ] The `_SNAP_B64` constant is present and non-empty
- [ ] Audio graph wired correctly: `_snapGain2 → _snapComp2 → _ac2.destination`
- [ ] Capture-phase resume listeners registered at module level (not inside a function that might not be called)
- [ ] `cutouts2024/` path used for piece JSON loading (not old `cutouts/` path unless explicitly for legacy shapes)

---

## How to report findings

```
PRE-COMMIT REVIEW — v11.XX

VERSION BUMP
✅ Both version strings updated to v11.XX

PROJECT-CONTEXT.md
✅ Updated, date current, active issues reflect current state

SUBSYSTEM: Audio
✅ Always-resume capture listeners present
✅ Buffer decoded at page load
⚠️  Empty catch on decodeAudioData — consider logging to console.warn in dev

SUBSYSTEM: Rendering
✅ No rendering changes in this diff

GENERAL QUALITY
❌ console.log('debug snap') left in playSnap() — must remove before commit

VERDICT: 1 blocker. Fix console.log then re-review.
```

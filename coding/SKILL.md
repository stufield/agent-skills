---
name: coding
description: >
  Behavioral guidelines for writing code — think before coding,
  simplicity first, surgical changes, goal-driven execution. Apply when
  writing, reviewing, or refactoring any code. Always active.
metadata:
  author: Stu Field
  version: "1.0"
---


## Think Before Coding

**Do not assume. Do not hide confusion. Surface tradeoffs.**

Before implementing:
- State assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — do not pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what is confusing. Ask.

---

## Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that was not requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: *"Would a senior engineer say this is overcomplicated?"*
If the answer is yes, simplify.

---

## Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Do not "improve" adjacent code, comments, or formatting.
- Do not refactor things that are not broken.
- Match existing style, even if you would do it differently.
- If you notice unrelated dead code, mention it — do not delete it.

When your changes create orphans:
- Remove imports/variables/functions that **your** changes made unused.
- Do not remove pre-existing dead code unless asked.

> **The test:** Every changed line should trace directly to the user's request.

---


### Comments

Comment extensively. Capture reasons for design choices, possible
failure modes, and troubleshooting tips where appropriate.

---

**These guidelines are working if:** fewer unnecessary changes appear
in diffs, fewer rewrites stem from overcomplication, and clarifying
questions come *before* implementation rather than after mistakes.

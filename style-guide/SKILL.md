---
name: style-guide
description: >
  R style guide for all text files — naming conventions, spacing,
  pipes, ggplot2 formatting, roxygen2 docs, error messages, NEWS.md,
  and Git workflow. Apply when writing or reviewing R code, R package
  files, or R-adjacent documentation.
metadata:
  author: Stu Field
  version: "1.0"
---

# General text style

IMPORTANT! When writing text to file, keep all lines <= 70 chars.

# R style guide

> Compiled from https://style.tidyverse.org/ — all 19 sections.
> Use `styler` to auto-restyle code; use `lintr` to check conformance.

Apply when:
- Writing or reviewing any R code
- Naming files, objects, or functions in R
- Formatting pipes (`|>`), ggplot2 chains (`+`), or function calls
- Writing roxygen2 documentation
- Constructing error/warning/message strings in a package
- Writing or grooming `NEWS.md`
- Composing Git commit messages or PR descriptions

---

## Sections (load the file that matches the task)

| File | Sections | Covers |
|---|---|---|
| `references/naming-and-formatting.md` | 1–4 | File naming, file internal structure, object names, spacing |
| `references/functions-control-flow-pipes.md` | 5–12 | Vertical space, function calls, braces, control flow, misc syntax, function definitions, pipes, ggplot2 |
| `references/package-development.md` | 13–18 | Package file names/organisation, roxygen2 docs, tests, error messages, NEWS.md |

---

## Git / GitHub

**Commit messages:**
- Subject line: <= 50 chars, sentence case, no trailing period.
- Blank line, then paragraph detail if needed.
- `Fixes #<issue-number>` auto-closes issues on merge.

**Pull requests:**
- Title: brief description of changes, standalone (no issue number in title).
- Description: blank for obvious diffs; overview for complex changes;
  include `Fixes #<issue-number>` in description if applicable.

---

## Quick reference cheat sheet

| Topic | Rule |
|---|---|
| File names | `snake_case.R`, ISO dates, padded numbers |
| Object names | `snake_case`; verbs for functions, nouns for vars |
| Assignment | `<-` always |
| Strings | `"double"` by default |
| Logicals | `TRUE` / `FALSE` never `T` / `F` |
| Line length | 80 chars max |
| Indentation | 2 spaces |
| Pipe | `\|>` (base), not `%>%` |
| `{` placement | Last char on line; `}` first char on line |
| `return()` | Early returns only |
| Anonymous fns | `\(.x) .x + 1` for short; `function(x) { }` for multi-line |
| ggplot `+` | Same rules as `\|>` |
| `if` condition | `&&` / `\|\|` not `&` / `\|` |
| Comments | `# ` prefix; explain *why* not *what* |
| Semicolons | Never |
| Roxygen title | Sentence case, no trailing period |
| Error messages | `cli::cli_abort()`; must/can't; `x`/`i` bullets |
| Test files | Mirror `R/` structure; `test-foofy.R` |

## Part 2 — Packages

---

### 13. Package File Names

- Single function in a file → file name matches function name.
- Multiple related functions → concise evocative name.
- Deprecated functions → file prefixed `deprec-`.

---

### 14. Package File Organisation

Public functions + their roxygen docs come first. Private (unexported)
helper functions go after all documented public functions.

```r
# Good
#' Lots of functions for doing something cool
#'
#' @name something-cool
NULL

#' @describeIn something-cool Get the mean
#' @export
get_cool_mean <- function(x) { ... }

#' @describeIn something-cool Get the sum
#' @export
get_cool_sum <- function(x) { ... }

help_compute <- function() { ... }    # private, after public

# Bad — private before public
help_compute <- function() { ... }

#' @export
do_something_cool <- function() { help_compute() }
```

---

### 15. Roxygen2 Documentation

Use roxygen2 with markdown support.

**Title:** first line, sentence case, no trailing period.

```r
#' Combine values into a vector or list
#'
#' This is a generic function which combines its arguments.
```

**`@description`:** explicit tag only when multi-paragraph or contains
a bulleted list.

**Indentation:** one space after `#'`; continuation lines for a tag
add 2 more spaces.

```r
#' @param key The bare (unquoted) name of the column whose values will be used
#'   as column headings.
```

**`@param`, `@return`, `@seealso`:** start with capital, end with period.

**`@inheritParams`:** use to avoid duplicating parameter docs.

**Capitalization:** sentence case throughout; no code font for package
names (use `{glue}` or "the glue package").

```r
# Good
Use the glue package to flexibly interpolate values into strings.
Use {glue} to flexibly interpolate values into strings.

# Bad
Use `glue` to ...
Use the {glue} package to ...   # redundant — not both
```

**Cross-linking:** `[function()]` for same-package;
`[pkg::function()]` for other packages. Use `@seealso` for related
functions; `@family` for groups of related functions (plural family
names).

**R code in docs:** backtick all function args, values (`TRUE`, `NA`),
literals, and class names. Consider `[function()]` over ``
`function()` `` for cross-link benefit.

**Internal functions:** document with `#'` but add `@noRd` to suppress
`.Rd` generation.

```r
#' Drop last
#'
#' Drops the last element from a vector.
#'
#' @param x A vector object to be trimmed.
#'
#' @noRd
```

---

### 16. Tests

Test file organisation mirrors `R/` organisation:
- `R/foofy.R` → `tests/testthat/test-foofy.R`
- Use `usethis::use_test()` to create correctly named test files automatically.

---

### 17. Error Messages

Use `cli::cli_abort()` (preferred over `stop()` in packages).

**Structure:**
1. **Problem statement** — general, sentence case, ends with period.
   Use "must" when expected type/size is clear; "can't" when it isn't.
2. **Bulleted detail** — `x` bullets for what's wrong; `i` bullets for context.
3. **Hint** (optional) — `i` bullet, ends with `?`, only for
   clear/common mistakes.

```r
# Good problem statements
cli_abort("`n` must be a numeric vector, not a character vector.")
cli_abort("Can't find column `b` in `.data`.")

# Good bulleted detail
cli_abort(c(
  "`.x` and `.y` must have compatible lengths:",
  "x" = "`.x` has length 4",
  "x" = "`.y` has length 2"
))

# Good hint
cli_abort(c(
  "Filter specifications must be named.",
  "i" = "Did you mean `Species == \"setosa\"`?"
))
```

**Punctuation rules:**
- Sentence case; end in period.
- Prefer singular in problem statements.
- Show up to 5 problems, then `... and N more problems`.
- Surround argument names in backticks; use "column" (not "variable")
  to distinguish from args.
- No manual line breaks — let cli wrap automatically.
- Keep each component under 70 chars where possible.

**Localisation:** write simple sentences; keep localisation-friendly structure.

---

### 18. NEWS.md

**During development:** add bullets at the top of the file immediately
under the version heading; one line per bullet.

```md
# pkg (development version)

* `ggsave()` now uses full argument names to avoid partial match warnings (@wch, #2355).
```

- Include issue number `(#NNN)` and author `(@user)` for external contributions.
- Bullets in parentheses before the final period.

**Pre-release grooming:**
- Function name near the start of each bullet.
- Wrap lines at 80 chars; end each bullet with a period.
- Frame positively ("now does X"), present tense.
- Version heading: level 1 `#`; sub-sections: level 2 `##`.
- Common section headings: `## Breaking changes`, `## New features`,
  `## Minor improvements and fixes`.
- Within a section, sort bullets alphabetically by first function mentioned.
- Breaking changes section at the top; each bullet includes symptoms
  and fix instructions.
- Functions/args/filenames in backticks; function names include `()`.

**Blog post:** for major/minor releases, write a post highlighting new
features with examples.

---

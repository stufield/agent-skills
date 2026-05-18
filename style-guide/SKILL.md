---
name: tidyverse-style
description: Tidyverse R style guide — naming conventions, spacing, pipes, ggplot2 formatting, roxygen2 docs, error messages, NEWS.md, and Git workflow. Apply when writing or reviewing any R code, package files, or documentation.
---

# Tidyverse R Style Guide

> Compiled from https://style.tidyverse.org/ — all 11 sections.
> Use `styler` to auto-restyle code; use `lintr` to check conformance.

## When to use

Apply when:
- Writing or reviewing any R code
- Naming files, objects, or functions in R
- Formatting pipes (`|>`), ggplot2 chains (`+`), or function calls
- Writing roxygen2 documentation
- Constructing error/warning/message strings in a package
- Writing or grooming `NEWS.md`
- Composing Git commit messages or PR descriptions

---

## Part 1 — Analyses

---

### 1. File Naming (Analysis Scripts)

**Machine-readable:** lowercase only, no spaces/symbols, words delimited by `-` or `_`, extension `.R`.

```r
# Good
fit_models.R
exploratory-data-analysis.R

# Bad
fit models.R
ExploratoryDataAnalysis.r
foo.r
```

**Human-readable:** name describes what's in the file.

```r
# Good
report-draft-notes.txt

# Bad
temp.r
```

**Sort-friendly:** dates in `yyyy-mm-dd` (ISO 8601); padded numbers (`01-`, `02-`, not `1-`, `2-`); sequence numbers at the *start* of the name.

```r
# Good
01-load-data.R
02-exploratory-analysis.R
2025-01-01-report.Rmd

# Bad
model_first_try.R
run-first.r
feb 01 report.Rmd
```

Avoid `final`, `final2`, etc. Use Git or put the date in the name.

---

### 2. File Internal Structure

Use commented separator lines to break a file into readable sections:

IMPORTANT! when writing text to file, keep all lines <= 70 chars


```r
# Load data ---------------------------

# Plot data ---------------------------
```

Load all packages with `library()` at the **very top** of the file — never sprinkle them throughout.

---

### 3. Object Names

- snake_case only: lowercase letters, numbers, `_`.
- Variables → **nouns**; functions → **verbs**.
- Concise and meaningful; avoid overly long names.
- Never shadow built-ins (`T`, `c`, `mean`, etc.).
- Avoid dots in names — reserve `.` for S3 dispatch (`function.class`).

```r
# Good
day_one
add_row()
permute()

# Bad
DayOne
dayone
first_day_of_the_month
row_adder()
T <- FALSE
c <- 10
```

If cramming data into names (e.g. `model_2018`, `model_2019`), use a list or data frame instead.

---

### 4. Spacing

**Commas:** space after, never before.

```r
x[, 1]    # Good
x[,1]     # Bad
```

**Parentheses — regular function calls:** no spaces inside or outside.

```r
mean(x, na.rm = TRUE)    # Good
mean (x, na.rm = TRUE)   # Bad
mean( x, na.rm = TRUE )  # Bad
```

**Parentheses — control flow (`if`, `for`, `while`):** space before and after `()`.

```r
if (debug) { ... }    # Good
if(debug){ ... }      # Bad
```

**Anonymous function args:** space after `()`.

```r
function(x) {}    # Good
function (x) {}   # Bad
```

**Embracing operator `{{ }}`:** always inner spaces.

```r
group_by({{ by }})    # Good
group_by({{by}})      # Bad
```

**Infix operators** (`==`, `+`, `-`, `<-`, etc.): surrounded by spaces.

```r
height <- (feet * 12) + inches    # Good
height<-feet*12+inches            # Bad
```

**No spaces around high-precedence operators:** `::`, `:::`, `$`, `@`, `[`, `[[`, `^`, unary `-`/`+`, `:`.

```r
sqrt(x^2 + y^2)   # Good
df$z              # Good
x <- 1:10         # Good

sqrt(x ^ 2 + y ^ 2)   # Bad
df $ z                 # Bad
x <- 1 : 10            # Bad
```

**Tidy eval bang operators** — no spaces around `!!` / `!!!`.

```r
call(!!xyz)     # Good
call(!! xyz)    # Bad
```

**Single-sided formulas** with a single RHS identifier — no space after `~`.

```r
~foo            # Good
~ foo           # Bad
~ .x + .y      # Bad (complex RHS needs space: `~ .x + .y` is Bad; write `~ .x + .y`)
```

Wait — correction per the guide: single-sided formulas with a *single* identifier get no space (`~foo`); complex RHS *does* get a space (`~ .x + .y`).


**Extra spaces for alignment** are preferred when they improve readability:

```r
list(
  total = a + b + c,
  mean  = (a + b + c) / n
)
```

---

### 5. Vertical Space

- No empty lines at the start or end of functions.
- At most one empty line to separate "thoughts" or functions.
- Empty line before a comment block is helpful to visually connect it to the code below.

---

### 6. Function Calls

**Named arguments:** omit names for data args; use full names when overriding defaults.

```r
mean(1:10, na.rm = TRUE)         # Good
mean(x = 1:10, , FALSE)          # Bad — positional mess
```

**No partial matching of argument names.**

```r
rep(1:2, times = 3)    # Good
rep(1:2, t = 3)        # Bad
```

**No assignment inside function calls** (except `capture.output`).

```r
x <- complicated_function()
if (nzchar(x) < 1) { ... }    # Good

if (nzchar(x <- complicated_function()) < 1) { ... }    # Bad
```

**Long calls:** 80-char line limit. If too long, one argument per line, closing `)` on its own line.

```r
# Good
do_something_very_complicated(
  something = "that",
  requires = many,
  arguments = "some of which may be long"
)

# Bad
do_something_very_complicated("that", requires, many, arguments,
                              "some of which may be long"
                              )
```

Multiple unnamed closely-related args can share a line (common in `paste0`):

```r
paste0(
  "Requirement: ", requires, "\n",
  "Result: ", result, "\n"
)
```

---

### 7. Braced Expressions `{}`

- `{` is the **last character on its line**.
- Contents indented **2 spaces**.
- `}` is the **first character on its line**.
- Empty braces: `{}` with no space inside.

```r
# Good
if (y < 0 && debug) {
  message("y is negative")
}

function(...) {}

# Bad
if (y < 0 && debug) {
message("Y is negative")
}

function(...) { }
```

---

### 8. Control Flow

**Loops:** body must be a braced expression.

```r
for (i in seq) {
  x[i] <- x[i] + 1
}
```

**If statements:**
- Single-line `if` (no braces) only for simple, side-effect-free assignments.
- Multi-line `if` must use braced expressions.
- `else` on the same line as `}`.
- Use `&&` / `||` in conditions, never `&` / `|` (which are vectorised).
- Avoid implicit type coercion in conditions: `if (length(x) > 0)` not `if (length(x))`.

```r
# Good — single line
message <- if (x > 10) "big" else "small"

# Good — multi-line
if (x > 10) {
  x * 2
} else {
  x * 3
}

# Bad
if (x > 0) return(x)
if (x > 10)
  x * 2
```

**Control flow modifiers** (`return()`, `stop()`, `break`, `next`) always go in their own `{}` block.

```r
# Good
if (y < 0) {
  stop("Y is negative")
}

# Bad
if (y < 0) stop("Y is negative")
```

**Switch statements:** use named (not positional) args; each element on its own line; include a fall-through error.

```r
switch(x,
  a = ,
  b = 1,
  c = 2,
  stop("Unknown `x`", call. = FALSE)
)
```

---

### 9. Miscellaneous Syntax

**Semicolons:** never. Not at line end, not to separate statements.

**Assignment:** always `<-`, never `=`.

```r
x <- 5    # Good
x = 5     # Bad
```

**Strings:** double quotes `"` by default; single quotes `'` only when content contains `"`.

**Logicals:** always `TRUE` / `FALSE`, never `T` / `F`.

**Comments:** `# ` (hash + single space). In analysis code, use comments to record *findings and decisions*, not to explain what the code does. If you need to explain what it does, rewrite it to be clearer.

---

### 10. Functions

**Naming:** verbs for functions, nouns for variables.

**Anonymous functions:** use `\(x) x + 1` for short inline lambdas in arguments. Do **not** use `\()` for multi-line bodies, named functions, or inside pipes.

```r
# Good
map(xs, \(x) mean((x + 5)^2))

map(xs, function(x) {
  mean((x + 5)^2)
})

cv <- function(x) {
  sd(x) / mean(x)
}

# Bad
map(xs, ~ mean((.x + 5)^2))          # formula lambda — avoid
map(xs, \(x) { mean((x + 5)^2) })    # \() with multi-line body
cv <- \(x) sd(x) / mean(x)           # \() for named function
```

**Multi-line function definitions:** two acceptable styles — *single-indent* (args indented 2 spaces, `) {` on its own line) or *hanging-indent* (args aligned with opening `(`).

```r
# Single-indent (preferred for long names / S7 methods)
long_function_name <- function(
  a = "a long argument",
  b = "another argument"
) {
  # body
}

# Hanging-indent
long_function_name <- function(a = "a long argument",
                               b = "another argument") {
  # body
}
```

**`return()`:** use only for *early returns*. Let R implicitly return the last expression otherwise.

```r
# Good
find_abs <- function(x) {
  if (x > 0) {
    return(x)
  }
  x * -1
}

add_two <- function(x, y) {
  x + y
}

# Bad
add_two <- function(x, y) {
  return(x + y)
}
```

**Side-effect functions:** return first argument `invisible(x)` to support piping.

```r
print.url <- function(x, ...) {
  cat("Url: ", build_url(x), "\n", sep = "")
  invisible(x)
}
```

**Function comments:** explain the *why*, not the what/how. Sentence case; end with `.` only if two or more sentences.

---

### 11. Pipes

**Use `|>` (base pipe).** Do not use `%>%` from magrittr.

`|>` should:
- Always have a space before it.
- Be followed by a new line.
- Each subsequent step indented 2 spaces.

```r
# Good
iris |>
  filter(Species == "setosa") |>
  select(Sepal.Length) |>
  arrange(Sepal.Length)

# Bad
iris |> filter(Species == "setosa") |>
select(Sepal.Length) |>
arrange(Sepal.Length)
```

**Avoid pipes when:**
- Manipulating more than one primary object at a time.
- There are meaningful intermediate objects worth naming.

**Long steps:** if a step's args don't fit on one line, each arg on its own line:

```r
iris |>
  summarise(
    Sepal.Length = mean(Sepal.Length),
    Sepal.Width = mean(Sepal.Width),
    .by = Species
  )
```

**Short pipes on one line** are OK, but one-function-per-line is generally better.

**Assignment with pipes:** variable name first (acts as a heading) or at end with `->`. Both are acceptable; name-first is most readable.

```r
iris_long <- iris |>
  gather(measure, value, -Species) |>
  arrange(-value)
```

**Inline pipes as arguments:** OK for short, obvious sub-pipes; extract to named variables for complex ones.

```r
# Good
x |>
  semi_join(y |> filter(is_valid))
```

---

### 12. ggplot2

ggplot2 `+` follows the same rules as `|>`.

- `+` always has a space before it and is followed by a new line.
- One level of indentation throughout (no extra indent after `ggplot()`).
- Long layer args: each arg on its own line.

```r
# Good
iris |>
  filter(Species == "setosa") |>
  ggplot(aes(x = Sepal.Width, y = Sepal.Length)) +
  geom_point() +
  labs(
    x = "Sepal width, in cm",
    y = "Sepal length, in cm",
    title = "Sepal length vs. width of irises"
  )

# Bad — data manipulation inside ggplot call
ggplot(filter(iris, Species == "setosa"), aes(x = Sepal.Width, y = Sepal.Length)) +
  geom_point()

# Bad — extra indent after ggplot()
iris |>
  filter(Species == "setosa") |>
  ggplot(aes(x = Sepal.Width, y = Sepal.Length)) +
    geom_point()

# Bad — layer on same line as +
ggplot(aes(...)) + geom_point()
```

Do **all** data manipulation in a pipeline *before* `ggplot()`. Never filter/mutate inside the `data` argument.

---

## Part 2 — Packages

---

### 13. Package File Names

- Single function in a file → file name matches function name.
- Multiple related functions → concise evocative name.
- Deprecated functions → file prefixed `deprec-`.

---

### 14. Package File Organisation

Public functions + their roxygen docs come first. Private (unexported) helper functions go after all documented public functions.

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

**`@description`:** explicit tag only when multi-paragraph or contains a bulleted list.

**Indentation:** one space after `#'`; continuation lines for a tag add 2 more spaces.

```r
#' @param key The bare (unquoted) name of the column whose values will be used
#'   as column headings.
```

**`@param`, `@return`, `@seealso`:** start with capital, end with period.

**`@inheritParams`:** use to avoid duplicating parameter docs.

**Capitalization:** sentence case throughout; no code font for package names (use `{glue}` or "the glue package").

```r
# Good
Use the glue package to flexibly interpolate values into strings.
Use {glue} to flexibly interpolate values into strings.

# Bad
Use `glue` to ...
Use the {glue} package to ...   # redundant — not both
```

**Cross-linking:** `[function()]` for same-package; `[pkg::function()]` for other packages. Use `@seealso` for related functions; `@family` for groups of related functions (plural family names).

**R code in docs:** backtick all function args, values (`TRUE`, `NA`), literals, and class names. Consider `[function()]` over `` `function()` `` for cross-link benefit.

**Internal functions:** document with `#'` but add `@noRd` to suppress `.Rd` generation.

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
1. **Problem statement** — general, sentence case, ends with period. Use "must" when expected type/size is clear; "can't" when it isn't.
2. **Bulleted detail** — `x` bullets for what's wrong; `i` bullets for context.
3. **Hint** (optional) — `i` bullet, ends with `?`, only for clear/common mistakes.

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
- Surround argument names in backticks; use "column" (not "variable") to distinguish from args.
- No manual line breaks — let cli wrap automatically.
- Keep each component under 70 chars where possible.

**Localisation:** write simple sentences; keep localisation-friendly structure.

---

### 18. NEWS.md

**During development:** add bullets at the top of the file immediately under the version heading; one line per bullet.

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
- Common section headings: `## Breaking changes`, `## New features`, `## Minor improvements and fixes`.
- Within a section, sort bullets alphabetically by first function mentioned.
- Breaking changes section at the top; each bullet includes symptoms and fix instructions.
- Functions/args/filenames in backticks; function names include `()`.

**Blog post:** for major/minor releases, write a post highlighting new features with examples.

---

## Part 3 — Other

---

### 19. Git / GitHub

**Commit messages:**
- Subject line: ≤ 50 chars, sentence case, no trailing period.
- Blank line, then paragraph detail if needed.
- `Fixes #<issue-number>` auto-closes issues on merge.

**Pull requests:**
- Title: brief description of changes, standalone (no issue number in title).
- Description: blank for obvious diffs; overview for complex changes; include `Fixes #<issue-number>` in description if applicable.

---

## Quick Reference Cheat Sheet

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
| Anonymous fns | `\(x) x + 1` for short; `function(x) { }` for multi-line |
| ggplot `+` | Same rules as `\|>` |
| `if` condition | `&&` / `\|\|` not `&` / `\|` |
| Comments | `# ` prefix; explain *why* not *what* |
| Semicolons | Never |
| Roxygen title | Sentence case, no trailing period |
| Error messages | `cli::cli_abort()`; must/can't; `x`/`i` bullets |
| Test files | Mirror `R/` structure; `test-foofy.R` |

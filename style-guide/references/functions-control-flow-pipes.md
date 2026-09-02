### 5. Vertical Space

- No empty lines at the start or end of functions.
- At most one empty line to separate "thoughts" or functions.
- Empty line before a comment block is helpful to visually connect it
  to the code below.

---

### 6. Function Calls

**Named arguments:** omit names for data args; use full names when
overriding defaults.

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

**Long calls:** 70-char line limit. If too long, one argument per
line, closing `)` on its own line.

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

Multiple unnamed closely-related args can share a line (common in
`paste0`):

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
- Single-line `if` (no braces) only for simple, side-effect-free
  assignments.
- Multi-line `if` must use braced expressions.
- `else` on the same line as `}`.
- Use `&&` / `||` in conditions, never `&` / `|` (which are
  vectorised).
- Avoid implicit type coercion in conditions: `if (length(x) > 0)` not
  `if (length(x))`.

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

**Control flow modifiers** (`return()`, `stop()`, `break`, `next`)
always go in their own `{}` block.

```r
# Good
if (y < 0) {
  stop("Y is negative")
}

# Bad
if (y < 0) stop("Y is negative")
```

**Switch statements:** use named (not positional) args; each element
on its own line; include a fall-through error.

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

**Comments:** `# ` (hash + single space). In analysis code, use
comments to record *findings and decisions*, not to explain what the
code does. If you need to explain what it does, rewrite it to be
clearer.

---

### 10. Functions

**Naming:** verbs for functions, nouns for variables.

**Anonymous functions:** use `\(.x) .x + 1` for short inline lambdas in
arguments. Do **not** use `\()` for multi-line bodies, named
functions, or inside pipes.

```r
# Good
map(xs, \(.x) mean((.x + 5)^2))

map(xs, function(.x) {
  mean((.x + 5)^2)
})

cv <- function(.x) {
  sd(.x) / mean(.x)
}

# Bad
map(xs, ~ mean((.x + 5)^2))          # formula lambda — avoid
map(xs, \(.x) { mean((.x + 5)^2) })  # \() with multi-line body
cv <- \(.x) sd(.x) / mean(.x)        # \() for named function
```

**Multi-line function definitions:** two acceptable styles —
*single-indent* (args indented 2 spaces, `) {` on its own line) or
*hanging-indent* (args aligned with opening `(`).

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

**`return()`:** use only for *early returns*. Let R implicitly return
the last expression otherwise.

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

**Function comments:** explain the *why*, not the what/how. Sentence
case; end with `.` only if two or more sentences.

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

**Long steps:** if a step's args don't fit on one line, each arg on
its own line:

```r
iris |>
  summarise(
    Sepal.Length = mean(Sepal.Length),
    Sepal.Width = mean(Sepal.Width),
    .by = Species
  )
```

**Short pipes on one line** are OK, but one-function-per-line is generally better.

**Assignment with pipes:** variable name first (acts as a heading) or
at end with `->`. Both are acceptable; name-first is most readable.

```r
iris_long <- iris |>
  gather(measure, value, -Species) |>
  arrange(-value)
```

**Inline pipes as arguments:** OK for short, obvious sub-pipes;
extract to named variables for complex ones.

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

Do **all** data manipulation in a pipeline *before* `ggplot()`. Never
filter/mutate inside the `data` argument.

---


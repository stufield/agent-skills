### 1. File Naming (Analysis Scripts)

**Machine-readable:** lowercase only, no spaces/symbols, words
delimited by `-` or `_`, extension `.R`.

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

**Sort-friendly:** dates in `yyyy-mm-dd` (ISO 8601); padded numbers
(`01-`, `02-`, not `1-`, `2-`); sequence numbers at the *start* of the
name.

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
# Load data ------

# Plot data ------
```

Load all packages with `library()` at the **very top** of the file —
never sprinkle them throughout.

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

If cramming data into names (e.g. `model_2018`, `model_2019`), use a
list or data frame instead.

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

**No spaces around high-precedence operators:** `::`, `:::`, `$`, `@`,
`[`, `[[`, `^`, unary `-`/`+`, `:`.

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

Wait — correction per the guide: single-sided formulas with a *single*
identifier get no space (`~foo`); complex RHS *does* get a space (`~
.x + .y`).


**Extra spaces for alignment** are preferred when they improve
readability:

```r
list(
  total = a + b + c,
  mean  = (a + b + c) / n
)
```

---


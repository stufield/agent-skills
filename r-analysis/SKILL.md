---
name: r-analysis
description: >
  Idiomatic R code guidelines for data analysis, wrangling,
  visualization, modeling, and utility functions — tidyverse patterns,
  native pipe, ggplot2 conventions, preferred packages. Use when
  writing, reviewing, debugging, or extending R code for analysis,
  wrangling, visualization, or modeling.
metadata:
  author: Stu Field
  version: "1.0"
---

# R Analysis

## Core philosophy
- **Idiomatic over clever**: prefer simple, readable tidyverse patterns
- **Concise**: 2–3 line solutions are almost always better than
  elaborate ones; push back if a solution is over-engineered
- **Native pipe**: prefer `|>` over `%>%`
- **No unnecessary dependencies**: don't reach for a package if base R
  or a tidyverse function already does it cleanly

---

## Pipe & wrangling conventions

```r
# Correct pipe style
df |>
  filter(...) |>
  mutate(...) |>
  summarise(...)

# Pairwise equality with NA-safety
vctrs::vec_equal(x, y, na_equal = FALSE)

# Replacing NAs
tidyr::replace_na(list(col = 0))   # in mutate context
coalesce(col, replacement)         # inline

# Multi-column returns from mutate — avoid list-columns
# Use pick(everything()) or return a tibble and unnest
```

### Never use `.by =`

Always use a preceding `group_by()`. `.by` returns groups in order of
first appearance in the data and drops factor level ordering, so
result rows come back scrambled relative to the factor levels.
`group_by()` sorts by level.

```r
# Wrong
df |> summarise(.by = protocol, n = n())
df |> mutate(.by = protocol, mu = mean(dose, na.rm = TRUE))

# Right
df |> group_by(protocol) |> summarise(n = n())
df |> group_by(protocol) |> mutate(mu = mean(dose, na.rm = TRUE)) |> ungroup()
```

Applies to every verb that accepts `.by`, including `mutate()`,
`filter()` and `slice()`. No exceptions, for consistency.

---

## Always use 1L for integers when appropriate

- always use the integer notation when appropriate, especially when
  indexing

```
x[1L]
x[1:8L]
if (length(x) == 2L) ...
```

## ggplot2 conventions

- **Axis limits**: always `coord_cartesian(xlim = ..., ylim = ...)` —
  never `xlim()`/`ylim()` which drops data silently
- **Dodge histograms**: `geom_histogram(position = "dodge")`
- **Jitter + dodge**: `position_jitterdodge()` when combining both
- **Vertical reference lines**: `geom_vline()` with explicit
  `xintercept`; use small offsets or jitter when multiple lines
  cluster
- **Large datasets in geom layers**: subsample within the geom call
  rather than pre-filtering the whole dataset
- **Smooth over categorical axes**: add `group = 1` or use numeric
  coding explicitly

---

## Preferred packages (reach for these first)

| Task | Package / function |
|------|--------------------|
| Wrangling | dplyr, tidyr, helpr, wranglr |
| Modeling  | randomForest, parsnip, broom |
| Plotting  | ggplot2 |
| Package Dev | devtools, roxygen2, testthat |


## Avoid these packages 

- purrr
- packrat
- stringr
- magrittr
- feather

---

# R Analysis Skill

## When to use
When writing, reviewing, debugging, or extending R code for data
analysis, wrangling, visualization, modeling, or utility functions.
Also applies when designing function APIs or reviewing tidyverse
pipelines.

---

## Core philosophy
- **Idiomatic over clever**: prefer simple, readable tidyverse patterns
- **Concise**: 2–3 line solutions are almost always better than elaborate ones; push back
  if a solution is over-engineered
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

---

## ggplot2 conventions

- **Axis limits**: always `coord_cartesian(xlim = ..., ylim = ...)` — never `xlim()`/`ylim()`
  which drops data silently
- **Dodge histograms**: `geom_histogram(position = "dodge")`
- **Jitter + dodge**: `position_jitterdodge()` when combining both
- **Vertical reference lines**: `geom_vline()` with explicit `xintercept`; use small offsets
  or jitter when multiple lines cluster
- **Large datasets in geom layers**: subsample within the geom call rather than pre-filtering
  the whole dataset
- **Smooth over categorical axes**: add `group = 1` or use numeric coding explicitly

---

## Preferred packages (reach for these first)

| Task | Package / function |
|------|--------------------|
| Wrangling | dplyr, tidyr, helpr, wranglr |
| Modeling  | randomForest, parsnip, broom |
| Plotting  | ggplot2 |
| Package Dev | devtools, roxygen2, testthat |

---

## Things to avoid
- `attach()` — ever
- `setwd()` — ever
- `T` / `F` shorthand — always write `TRUE` / `FALSE`
- `subset()` — use `dplyr::filter()` instead
- Reassigning built-in names (`c`, `df`, `t`, etc.)

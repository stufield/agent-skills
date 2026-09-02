---
name: r-pkg-dev
description: >
  R package development guidelines — project structure, GitHub Actions
  CI/CD, DESCRIPTION files, test infrastructure, and non-CRAN
  dependency management. Use when working on R package structure,
  GitHub Actions CI/CD, `DESCRIPTION` files, unit tests, or non-CRAN
  dependencies.
metadata:
  author: Stu Field
  version: "1.0"
---

# R Package Development

## Non-CRAN dependencies

Always declare non-CRAN packages in `DESCRIPTION` under `Remotes:` — without this,
GitHub Actions will fail to install dependencies even if `Imports:` is correct.

```
Imports:
    helpr,
    wranglr,
    libml

Remotes:
    stufield/helpr,
    stufield/wranglr,
    SomaLogic/SomaPlotr
```

---

## Test structure

- Tests live in `tests/testthat/`
- One test file per source file: `test-utils.R` → `tests/testthat/test-utils.R`
- Use `testthat::test_that()` with descriptive labels
- Use `withr::local_*` helpers for environment/option side effects
- Snapshot tests (`expect_snapshot()`) for complex output like tables or plots



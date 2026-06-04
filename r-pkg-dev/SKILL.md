---
name: r-pkg-dev
description: >
  R package development guidelines — project structure,
  GitHub Actions CI/CD, DESCRIPTION files, test infrastructure, and
  non-CRAN dependency management.
when_to_load:
  - when working on R package structure and management
  - when working with GitHub Actions CI/CD
  - when working on `DESCRIPTION` files
  - when creating unit tests and test infrastructure
  - when considering non-CRAN dependencies
author: Stu Field
version: 1.0
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



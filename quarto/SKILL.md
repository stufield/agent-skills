---
name: quarto
description: Conventions for creating, editing, and debugging Quarto documents (.qmd) — HTML reports, parameterized content, code chunks, TOC formatting, and client branding.
---

# Quarto Reports Skill

## When to use
When creating, editing, or debugging Quarto documents (.qmd) —
especially HTML reports with custom branding, parameterized content,
or client-facing output.

---

## Standard HTML report setup

```yaml
---
title: "Report Title"
format:
  html:
    theme: [cosmo, custom.scss]
    toc: true
    toc-depth: 3
    toc-location: left
    code-fold: true
    code-tools: true
    self-contained: true
params:
  client: "Client Name"
---
```

---

## Code chunk conventions

- Set global chunk options in a setup chunk at the top:

```r
#| label: setup
#| include: FALSE

knitr::opts_chunk$set(
  echo       = TRUE,
  message    = FALSE,
  warning    = FALSE
)
```

- Label every chunk meaningfully — no anonymous chunks in final reports

---

## Parameterized client headers

Render with specific client:

```bash
quarto render report.qmd -P client:"Acme Corp"
```

---

## TOC & section formatting

- Use `##` for top-level report sections (not `#` — that's the title)
- Callout blocks for key findings: `::: {.callout-note}` / `::: {.callout-important}`
- Tabsets for multi-view content: `::: {.panel-tabset}`

---

## Things to avoid
- `self-contained: true` is deprecated in newer Quarto — use `embed-resources: true`
- Avoid `fig.cap` in chunk options for HTML (use `#| fig-cap:` YAML-style instead)
- Don't hardcode client names in prose — always route through `params$client`

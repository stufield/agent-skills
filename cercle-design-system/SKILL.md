---
name: cercle-design-system
description: >
  Cercle's complete design token reference for any frontend work. Use this skill
  whenever an engineer touches CSS, React, Tailwind, styled-components, Emotion,
  shadcn, or any UI code, even if they don't mention "design system" or "tokens".
  Trigger when a user asks to build, style, or update a button, card, modal, form,
  input, table, header, navigation, sidebar, badge, alert, toast, dropdown,
  tooltip, avatar, layout, page, or logo placement; or makes a decision about
  colors, fonts, font sizes, font weights, spacing, padding, margins, shadows,
  borders, radius, z-index, breakpoints, transitions, animations, or focus states.
  If the work is visual at Cercle, this skill must be active.
metadata:
  author: David Vegas Garcia
  version: "1.4"
---

# Cercle design system

> Authored and maintained by **David Vegas Garcia** · v1.4 · April 2026

All values come from `cercle-design-tokens.json` (source: brand
guideline March 2022 + UI extensions). The aesthetic is **calm,
clinical, considered**: crisp edges, warm-tinted shadows, structured
typography.

A live visual reference is at `design-system/showcase.html`. Open it
in a browser to preview every token and component.

---

## Common mistakes (read first)

Six mistakes that show up most often. Avoid them up front, even before reaching for tokens.

1. **Don't use `cercleOrange` (`#F76922`) for body or paragraph
   text.** It's reserved for CTAs, focus rings, links, and accent
   emphasis only.
2. **Don't use `blueGray` (`#809EB0`) for body copy.** It's an accent
   for charts, infographics, and avatar backgrounds.
3. **Don't use pure black (`rgba(0,0,0,…)`) shadows.** All Cercle
   shadows are tinted with `rgba(28, 43, 54, …)` for warmth.
4. **Don't use `transition: all`.** It animates layout-triggering properties and feels heavy. Specify the properties.
5. **Don't invent values.** No 5px, 13px, 17px paddings; no `#eee`
   borders; no `border-radius: 6px`. Always snap to the token scales in
   the reference files below.
6. **Don't mix icon libraries.** Cercle uses **Iconoir** exclusively
   (https://iconoir.com). Never import from Material Icons, Heroicons,
   Lucide, FontAwesome, or any other library, even for a single icon.

---

## Token reference (load the file that matches the task)

| File | Covers |
|---|---|
| `references/brand-and-iconography.md` | Logo files, logo rules, logo-in-code, Iconoir usage, icon sizing, health-icon mapping |
| `references/color-tokens.md` | Brand palette, neutral/orange/blue-gray scales, semantic colors, chart palette |
| `references/typography-tokens.md` | Font families, weights, letter-spacing, line-height, full type scale |
| `references/spacing-shape-elevation.md` | Spacing scale, border radius/width, shadows, z-index |
| `references/layout-sizing-motion-focus.md` | Breakpoints, grid, icon/control/avatar sizes, motion durations/easings, focus rings |
| `references/component-recipes.md` | Ready-made token combos for button, input, card, modal, dropdown, toast, section heading |

---

## Quick-reference cheat sheet

| Decision | Token to reach for |
|----------|--------------------|
| Primary CTA button | bg `#F76922`, hover `#DC5310`, text `#FFF` |
| Page background | `#FFFFFF` |
| Raised surface (card) | bg `#F7F7F6`, shadow elevation-1 |
| Body text | `#1C2B36` at `bodyM` (16px/24px Barlow 400) |
| Secondary text | `#54565A` |
| Muted/helper text | `#7F7F82` |
| Section heading | `headingM`–`headingXL` Barlow 600 |
| Input border | `#D0CFCD` default, `#F76922` on focus |
| Default border radius | 4px (`radius-sm`) |
| Dropdown shadow | elevation-3 |
| Modal shadow + z | elevation-5 · z=1200 |
| Destructive action | bg `#C0392B`, hover `#8E2A21` |
| Logo on light bg | `Cercle_logo_RGB_Orange.png` |
| Logo on dark bg | `Cercle_logo_RGB_White.png` |
| Chart series 1 (always lead with this) | `chart-1.default` (#F76922) |
| Heatmap intensity | `chart-sequential-warm` (5 stops) |
| Below/above baseline | `chart-diverging-coolWarm` |
| On-target vs miss | `chart-diverging-badGood` |
| Icon library | **Iconoir only** (`<i class="iconoir-X">` or `iconoir-react`) |
| Vitals icon | `iconoir-activity` |
| Cardiology icon | `iconoir-heart` |
| Mental health icon | `iconoir-brain` |
| Care plan / clinic icon | `iconoir-healthcare` |

---

## Authoring

This skill, the showcase, and the token JSON were authored by **David
Vegas Garcia** (`david.vegas@cercle.ai`). Open issues, suggest tokens,
or propose new component recipes by filing a PR against
`design-system/`. Keep changes traceable to the brand guideline or
document the rationale in the PR description.

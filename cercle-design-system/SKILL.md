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
author: David Vegas Garcia
version: 1.4
---

# Cercle Design System

> Authored and maintained by **David Vegas Garcia** · v1.4 · April 2026

All values come from `cercle-design-tokens.json` (source: brand
guideline March 2022 + UI extensions). The aesthetic is **calm,
clinical, considered**: crisp edges, warm-tinted shadows, structured
typography.

A live visual reference is at `design-system/showcase.html`. Open it
in a browser to preview every token and component.

---

## Brand & logo

**Cercle** is a healthcare platform for clinicians coordinating
complex care. The brand voice is calm, clinical, considered, never
loud or decorative. The visual system reflects that: crisp rectangles,
warm-tinted shadows (never pure black), structured Barlow typography,
and a single sharp accent: `cercleOrange`.

### Logo files

The wordmark "cercle" is the only brand mark. Files live in
`design-system/assets/PNG/`:

| File | When to use |
|---|---|
| `Cercle_logo_RGB_Orange.png` | **Default** on light surfaces. `cercleOrange` (#F76922) |
| `Cercle_logo_RGB_Gray.png` | Light surfaces where orange would compete with content |
| `Cercle_logo_RGB_White.png` | Dark or `cercleOrange` surfaces only (sidebars, hero strips) |
| `Cercle_logo_RGB_Black.png` | Print or single-color contexts only. Avoid in product UI |

### Logo rules

- **Minimum height: 30px** in any digital surface. Below that the
  letterforms break. Use a stylized "c" mark instead (see
  favicon/avatar conventions).
- **Clear space equals the wordmark's x-height** on all four sides.
  Nothing inside that frame.
- **Lock contrast pairings**: orange on white, gray on white, white on
  cercleGray, white on cercleOrange. Always check legibility.
- **Never** recolor, gradient, drop-shadow, rotate, stretch
  non-proportionally, or place on busy imagery without an overlay. The
  wordmark is a fixed asset with a locked aspect ratio (~4.22:1, 1182:280).

### Logo in code

```html
<!-- Default app sidebar (dark surface) -->
<img src="/assets/PNG/Cercle_logo_RGB_White.png" alt="Cercle" style="height:32px" />

<!-- Marketing site / light pages -->
<img src="/assets/PNG/Cercle_logo_RGB_Orange.png" alt="Cercle" style="height:40px" />
```

Always include `alt="Cercle"` and never set width and height in a way
that distorts the aspect ratio. Set one dimension and let the other
auto-scale.

---

## Iconography

Cercle uses **Iconoir** (https://iconoir.com) as its **single icon
library**. Never mix in icons from other libraries (Material Icons,
Heroicons, Lucide, FontAwesome, Feather, etc.). Mixing libraries
breaks visual consistency because each has its own stroke weight,
optical balance, and corner radius. **Iconoir is mandatory, not a
default.**

### Why Iconoir

- Open source, 1,500+ icons drawn on a 24×24 grid with consistent 1.5px strokes
- Geometric, calm, minimal aesthetic that fits Cercle's "calm,
  clinical, considered" voice
- Has a meaningful set of health-domain icons (`activity`, `heart`,
  `brain`, `dna`, `hospital`, `healthcare`, `cube-bandage`, etc.)
- Available as plain SVG, CSS classes, or React components

### Use icons sparingly

Icons add cognitive load. They are a tool of last resort, not decoration:

- Only when an icon helps comprehension faster than text alone
- Never decoratively (no "sparkle next to a heading", no checkmark on
  every list item)
- One icon per button maximum, always to the left of the label
- In data tables and stacked lists, prefer text plus a status badge over icons
- Don't use an icon next to a text label that says the same thing (no
  phone icon next to "Phone")

### Importing

```html
<!-- Plain HTML / static pages -->
<link rel="stylesheet"
      href="https://cdn.jsdelivr.net/gh/iconoir-icons/iconoir@main/css/iconoir.css">

<i class="iconoir-heart"></i>
<i class="iconoir-activity"></i>
```

```jsx
// React
import { Heart, Activity } from 'iconoir-react';

<Heart width={20} height={20} />
<Activity width={20} height={20} />
```

### Sizing

Match icon size to the surrounding control or text. Reuse the existing icon size tokens:

| Token | Size | Use |
|-------|------|-----|
| `iconXS` | 12px | Inline with `caption` or `overline` text |
| `iconS` | 16px | Inline with `bodyS`/`label`, button labels |
| `iconM` | 20px | **Default**. Alerts, inputs, list items |
| `iconL` | 24px | Navigation, card headings |
| `iconXL` | 32px | Empty states, hero/feature areas |
| `icon2XL` | 48px | Large empty states only |

### Color

Iconoir icons inherit `currentColor`. Set the color via CSS `color`
(not `fill` or `stroke`) so icons respect their parent's text color
and any state changes (hover, active, disabled).

### Health icons (clinical contexts)

Map clinical concepts to specific Iconoir icons. Use these instead of
generic placeholders like folders:

| Concept | Iconoir icon | When to use |
|---------|--------------|-------------|
| Vitals, heart rate | `activity` | BP readings, HR streams, vitals over time |
| Cardiology | `heart` | Heart programs, cardio conditions |
| Worsening cardio metric | `heart-arrow-down` | Declining vitals, alerts |
| Mental health, neurology | `brain` | Behavioral health, cognitive |
| General healthcare | `healthcare` | Care plan home, clinic |
| Hospital, clinic | `hospital` | Facility references |
| Home care | `home-hospital` | Home-based programs |
| Wound care, first aid | `cube-bandage` | Post-op, injuries |
| Genetics | `dna` | Genomics, family history |
| Lab, diagnostics | `microscope`, `test-tube` | Lab orders, diagnostics |
| Imaging | `xray-view` | Radiology orders |
| Infection prevention | `mask-square` | PPE, infection control |
| Sleep, rest | `pillow` | Sleep tracking, recovery |
| Activity, exercise | `running`, `walking` | Exercise goals, mobility |
| Health protection | `health-shield` | Insurance, security |

### General UI icons

For non-clinical UI, prefer these names:

| Concept | Iconoir icon |
|---------|--------------|
| Add, create | `plus` |
| Search | `search` |
| Filter | `filter` |
| Edit | `edit-pencil` |
| Delete | `trash` |
| Settings | `settings` |
| User, patient | `user` |
| Calendar | `calendar` |
| Clock, time | `clock` |
| Notification | `bell` |
| Message, chat | `message`, `chat-bubble` |
| Email | `mail` |
| Phone | `phone` |
| More menu | `more-horiz` (rows) or `more-vert` (columns) |
| Navigation | `nav-arrow-right`, `nav-arrow-left`, `nav-arrow-down`, `nav-arrow-up` |
| Status check | `check` |
| Status warning | `warning-triangle` |
| Status info | `info-circle` |
| Status error, close | `xmark` |
| Download | `download` |
| Archive, ZIP | `archive` |

### Don'ts

- Don't use emojis (📞 🏥 ❤️) as icons. They render inconsistently
  across platforms and break the visual system.
- Don't recolor health icons with semantic colors (e.g., a red heart).
  They should inherit `text-secondary` or `text-primary` like other UI icons.
- Don't put more than one icon in a single button.
- Don't create custom icons for things Iconoir already has. If
  something is genuinely missing from Iconoir, propose adding it
  upstream before drawing bespoke artwork.
- Don't import individual icons from other libraries even "just for
  one missing concept". Find the closest Iconoir match or use text
  only.

---

## Common mistakes (read first)

Five mistakes that show up most often. Avoid them up front, even before reaching for tokens.

1. **Don't use `cercleOrange` (`#F76922`) for body or paragraph
   text.** It's reserved for CTAs, focus rings, links, and accent
   emphasis only.
2. **Don't use `blueGray` (`#809EB0`) for body copy.** It's an accent
   for charts, infographics, and avatar backgrounds.
3. **Don't use pure black (`rgba(0,0,0,…)`) shadows.** All Cercle
   shadows are tinted with `rgba(28, 43, 54, …)` for warmth.
4. **Don't use `transition: all`.** It animates layout-triggering properties and feels heavy. Specify the properties.
5. **Don't invent values.** No 5px, 13px, 17px paddings; no `#eee`
   borders; no `border-radius: 6px`. Always snap to the
   spacing/radius/color scales below.
6. **Don't mix icon libraries.** Cercle uses **Iconoir** exclusively
   (https://iconoir.com). Never import from Material Icons, Heroicons,
   Lucide, FontAwesome, or any other library, even for a single icon.
   See the Iconography section for the full rule and health-icon
   mapping.

---

## Colors

### Brand palette

| Token | Hex | Usage |
|-------|-----|-------|
| `cercleGray` | `#1C2B36` | Primary logo, headlines, dark surfaces |
| `cercleOrange` | `#F76922` | CTAs, highlights. **Never body copy** |
| `mediumGray` | `#54565A` | Body text only |
| `blueGray` | `#809EB0` | Accents, infographics. **Not body copy** |
| `lightGray` | `#D0CFCD` | Infographic fills |
| `white` | `#FFFFFF` | Surfaces, reverse logo |

### Neutral scale
`neutral-0` `#FFFFFF` → `neutral-50` `#F7F7F6` → `neutral-100` `#EFEEED` → `neutral-200` `#E2E1DF` → `neutral-300` `#D0CFCD` → `neutral-400` `#A9A8A6` → `neutral-500` `#7F7F82` → `neutral-600` `#54565A` → `neutral-700` `#363B42` → `neutral-800` `#243038` → `neutral-900` `#1C2B36` → `neutral-1000` `#0E161C`

### Orange scale
`orange-50` `#FFF1E9` → `100` `#FFDDC9` → `200` `#FCB997` → `300` `#FA9663` → `400` `#F87E40` → `500` `#F76922` → `600` `#DC5310` → `700` `#B0410B` → `800` `#7E2E08` → `900` `#511D05`

### Blue-gray scale
`blueGray-50` `#F0F4F7` → `100` `#DCE5EB` → `200` `#BDCED9` → `300` `#9DB6C5` → `400` `#809EB0` → `500` `#637F92` → `600` `#4D6577` → `700` `#3A4E5C` → `800` `#283740` → `900` `#172128`

### Semantic colors

| Purpose | Default | Subtle (bg) | Strong (emphasis) |
|---------|---------|-------------|-------------------|
| Success | `#3F8F6B` | `#E6F2EC` | `#286A4D` |
| Warning | `#D9A123` | `#FBF1D7` | `#A57A14` |
| Danger  | `#C0392B` | `#FAE5E2` | `#8E2A21` |
| Info    | `#3D7CA8` | `#E3EEF6` | `#285A7E` |

### Semantic surface, text, and border tokens

**Surface**
- `page` → `#FFFFFF` | `pageInverse` → `#1C2B36` | `raised` → `#F7F7F6` | `sunken` → `#EFEEED`
- `overlay` → `rgba(28, 43, 54, 0.56)`

**Text**
- `text-primary` → `#1C2B36` | `text-secondary` → `#54565A` | `text-tertiary` → `#7F7F82`
- `text-disabled` → `#A9A8A6` | `text-inverse` → `#FFFFFF`
- `text-accent` / `text-link` → `#F76922` | `text-linkHover` → `#DC5310`

**Border**
- `border-subtle` → `#E2E1DF` | `border-default` → `#D0CFCD` | `border-strong` → `#54565A`
- `border-inverse` → `#FFFFFF` | `border-focus` → `#F76922`

### Interactive (button) tokens

| Variant | Background | Hover bg | Active bg | Foreground |
|---------|-----------|----------|-----------|------------|
| Primary | `#F76922` | `#DC5310` | `#B0410B` | `#FFFFFF` |
| Secondary | `#1C2B36` | `#243038` | `#363B42` | `#FFFFFF` |
| Tertiary | `transparent` | `#EFEEED` | `#E2E1DF` | `#1C2B36` |
| Destructive | `#C0392B` | `#8E2A21` | n/a | `#FFFFFF` |

### Chart palette

For data visualizations: line/bar/area/donut/heatmap. **Always lead
with `chart-1` (Orange)** so the brand carries the dashboard. Naming
is positional (`chart-1` … `chart-8`) so series can swap colors
without renaming code.

**Categorical · 8 series**

Each color has 3 stops: `subtle` for area fills and hover backgrounds,
`default` for line/bar fills, `strong` for emphasis or dark-mode line
strokes. Defaults sit at similar perceptual lightness, so no series
visually dominates.

| # | Name | Subtle | Default | Strong |
|---|------|--------|---------|--------|
| 1 | Orange | `#FFDDC9` | `#F76922` | `#B0410B` |
| 2 | Teal | `#C9E3E1` | `#3F8F8B` | `#1F6562` |
| 3 | Plum | `#E2D6E8` | `#7E5B8E` | `#5A3D6A` |
| 4 | Gold | `#F4E2B6` | `#C8932A` | `#8B6618` |
| 5 | Slate | `#DCE5EB` | `#4D6577` | `#2C3E4F` |
| 6 | Sage | `#D7E4D8` | `#7FA383` | `#56785A` |
| 7 | Coral | `#F5D8CF` | `#D77762` | `#9E513F` |
| 8 | Indigo | `#D6DAE6` | `#3F4E7C` | `#1F2A4A` |

The ordering alternates warm/cool so a 2-series chart auto-gets Orange
+ Teal, the strongest pair. **Don't go past 8.** Beyond 8 categories,
group/aggregate or switch to a sequential scale.

**Sequential ramps · 5 stops, single hue**

For heatmaps, density plots, "more is darker" intensity. All three
reuse existing scales, no new tokens:

- `chart-sequential-warm` → `#FFF1E9` · `#FFDDC9` · `#FA9663` · `#F76922` · `#B0410B`
- `chart-sequential-cool` → `#F0F4F7` · `#DCE5EB` · `#9DB6C5` · `#637F92` · `#3A4E5C`
- `chart-sequential-neutral` → `#F7F7F6` · `#EFEEED` · `#D0CFCD` · `#7F7F82` · `#363B42`

**Diverging ramps · 5 stops, midpoint anchor**

For deviation-from-baseline visualizations:

- `chart-diverging-coolWarm` → `#3A4E5C` · `#9DB6C5` · `#EFEEED` · `#FA9663` · `#B0410B`
  Direction-only. Use when "below/above" carries no good/bad meaning (e.g., variance from average).
- `chart-diverging-badGood` → `#8E2A21` · `#C0392B` · `#EFEEED` · `#3F8F6B` · `#286A4D`
  Valenced. Uses semantic `danger` ↔ `success`. Use when one direction is desirable (e.g., on-target adherence).

**Chart anti-patterns**

- Don't use `cercleOrange` for the chart background or grid lines (it's a series color now).
- Don't mix categorical and semantic colors in the same chart. Pick one system.
- Grid lines should be `neutral-100` or `neutral-200`, never `cercleOrange` or any chart color.
- Axis labels use `text-secondary` (`#54565A`) at `bodyS` (14px) or `caption` (13px).

---

## Typography

### Font families

```css
--font-primary: "Barlow", system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, sans-serif;
--font-display: "Stix Two Text", "Times New Roman", Georgia, serif;
--font-mono:    "JetBrains Mono", ui-monospace, SFMono-Regular, Menlo, monospace;
```

### Font weights

| Token | Value |
|-------|-------|
| `regular` | 400 |
| `medium` | 500 |
| `semibold` | 600 |
| `bold` | 700 |

Barlow Light (300) and weights ≥800 are not part of the system.

### Letter-spacing scale

`tight` = -0.02em · `snug` = -0.01em · `normal` = 0 · `wide` = 0.02em · `wider` = 0.04em

### Line-height scale

`none` = 1 · `tight` = 1.15 · `snug` = 1.3 · `normal` = 1.5 · `loose` = 1.7

### Type scale

| Token | Family | Weight | Size | Line-height | Tracking |
|-------|--------|--------|------|-------------|----------|
| `displayXL` | Barlow | 500 | 72px | 76px | -0.02em |
| `displayL` | Barlow | 500 | 56px | 60px | -0.02em |
| `displayM` | Barlow | 500 | 48px | 52px | -0.02em |
| `displayS` | Barlow | 500 | 36px | 40px | -0.01em |
| `headingXL` | Barlow | 600 | 32px | 40px | -0.01em |
| `headingL` | Barlow | 600 | 28px | 36px | -0.01em |
| `headingM` | Barlow | 600 | 24px | 32px | 0 |
| `headingS` | Barlow | 600 | 20px | 28px | 0 |
| `headingXS` | Barlow | 600 | 16px | 24px | 0 |
| `subheadDisplay` | Stix Two Text *italic* | 500 | 40px | 48px | -0.01em |
| `bodyL` | Barlow | 400 | 18px | 28px | 0 |
| `bodyM` *(default)* | Barlow | 400 | 16px | 24px | 0 |
| `bodyS` | Barlow | 400 | 14px | 20px | 0 |
| `label` | Barlow | 600 | 14px | 20px | 0.02em |
| `caption` | Barlow | 700 | 13px | 20px | 0.02em |
| `overline` | Barlow | 700 | 12px | 16px | 0.04em, **UPPERCASE** |
| `code` | JetBrains Mono | 400 | 14px | 20px | n/a |

`subheadDisplay` is **editorial only**. Use it for large pull-quotes
or hero subheads, never for section headers.

### Typography rules (never do these)

- All-caps headlines (only `overline` is uppercase)
- Barlow Light (weight 300) for headlines
- Stix Two Text as the sole headline font
- Stix Two Text Italic for body copy
- Flush-right text alignment
- Centered headlines or body copy
- Highlights, gradients, or effects on type

---

## Spacing

Base unit: **4px**. Always snap to this grid.

| Token | Value | Token | Value |
|-------|-------|-------|-------|
| `space-0` | 0px | `space-8` | 32px |
| `space-px` | 1px | `space-10` | 40px |
| `space-0.5` | 2px | `space-12` | 48px |
| `space-1` | 4px | `space-16` | 64px |
| `space-2` | 8px | `space-20` | 80px |
| `space-3` | 12px | `space-24` | 96px |
| `space-4` | 16px | `space-32` | 128px |
| `space-5` | 20px | `space-40` | 160px |
| `space-6` | 24px | `space-48` | 192px |
| | | `space-64` | 256px |

### Semantic spacing

```
Component padding:  XS=8px  S=12px  M=16px  L=24px
Stack (vertical):   XS=8px  S=12px  M=16px  L=24px  XL=40px
Section gaps:       S=64px  M=96px  L=128px
Page gutters:       mobile=16px  tablet=24px  desktop=40px
```

---

## Shape

### Border radius

| Token | Value | When to use |
|-------|-------|-------------|
| `radius-none` | 0px | Tables, code blocks, structural dividers |
| `radius-xs` | 2px | Tags, small chips |
| `radius-sm` | 4px | **Default** for inputs, buttons, cards |
| `radius-md` | 8px | Larger cards, panels |
| `radius-lg` | 12px | Modals, drawers |
| `radius-xl` | 16px | Feature cards |
| `radius-2xl` | 24px | Hero sections |
| `radius-pill` | 999px | Badges, status dots, Cercle-logo-derived shapes only |
| `radius-circle` | 50% | Avatars |

Cercle's brand is **crisp and structural**. Default to `sm` (4px), not
rounded. Do not apply `pill` to buttons.

### Border width

| Token | Value | Default? |
|-------|-------|---------|
| `border-0` | 0px | n/a |
| `border-hair` / `border-thin` | 1px | Yes |
| `border-thick` | 2px | n/a |
| `border-heavy` | 4px | Structural accent lines |

---

## Shadows & Elevation

All shadows use `rgba(28, 43, 54, …)`, **never pure black**, for a
warm, clinical feel.

| Level | Shadow | z-index | Use for |
|-------|--------|---------|---------|
| 0 | none | 0 | Page surface |
| 1 | `0 1px 2px rgba(28,43,54,.06)` | 10 | Cards, list rows |
| 2 | `0 2px 4px rgba(28,43,54,.08), 0 1px 2px rgba(28,43,54,.04)` | 100 | Sticky headers |
| 3 | `0 4px 8px rgba(28,43,54,.10), 0 2px 4px rgba(28,43,54,.06)` | 1000 | Dropdowns, tooltips |
| 4 | `0 12px 24px rgba(28,43,54,.12), 0 4px 8px rgba(28,43,54,.06)` | 1100 | Popovers, menus |
| 5 | `0 24px 48px rgba(28,43,54,.16), 0 8px 16px rgba(28,43,54,.08)` | 1200 | Modals |
| 6 | `0 40px 80px rgba(28,43,54,.20), 0 16px 32px rgba(28,43,54,.10)` | 1300 | Toasts, overlays |

**Special shadows**
- `inner`: `inset 0 1px 2px rgba(28,43,54,.08)` for sunken inputs
- `focusRing`: `0 0 0 3px rgba(247,105,34,.40)` for focus on light bg
- `focusRingInverse`: `0 0 0 3px rgba(255,255,255,.60)` for focus on dark bg

---

## Z-index

```
hide=-1  base=0  raised=10  sticky=100
dropdown=1000  popover=1100  modal=1200  toast=1300  tooltip=1400
```

Always use named z-index values, not arbitrary numbers.

---

## Layout

### Breakpoints

| Token | Value |
|-------|-------|
| `xs` | 0px |
| `sm` | 640px |
| `md` | 768px |
| `lg` | 1024px |
| `xl` | 1280px |
| `2xl` | 1536px |

### Max-widths

`sm`=640px · `md`=768px · `lg`=1024px · `xl`=1200px · `2xl`=1440px · `prose`=680px

### Grid

| | Mobile | Tablet | Desktop |
|-|--------|--------|---------|
| Columns | 4 | 8 | 12 |
| Gutter | 16px | 24px | 24px |
| Margin | 16px | 24px | 40px |

---

## Sizes

### Icons

`iconXS`=12px · `iconS`=16px · `iconM`=20px · `iconL`=24px · `iconXL`=32px · `icon2XL`=48px

### Controls (height of interactive elements)

`controlS`=32px · `controlM`=40px · `controlL`=48px · `controlXL`=56px

### Avatars

`avatarS`=24px · `avatarM`=32px · `avatarL`=48px · `avatarXL`=64px

**Logo**: minimum height 30px; clear space equal to wordmark x-height on all sides.

---

## Motion

Cercle brand principle: **fluid, flat moves without added effects**.
Default to `base` (200ms) with `standard` easing. Use longer durations
only for choreographed, multi-element entrances.

### Durations

`instant`=0ms · `fast`=120ms · `base`=200ms · `slow`=320ms · `slower`=480ms · `slowest`=720ms

### Easings

```css
--ease-standard: cubic-bezier(0.2, 0, 0.2, 1);   /* most transitions */
--ease-entrance:  cubic-bezier(0, 0, 0.2, 1);     /* elements entering */
--ease-exit:      cubic-bezier(0.4, 0, 1, 1);     /* elements leaving */
--ease-emphasis:  cubic-bezier(0.2, 0, 0, 1);     /* spring-like pops */
```

### Transition shorthands

```css
/* color/bg/border: use fast */
transition: color 120ms cubic-bezier(0.2,0,0.2,1),
            background-color 120ms cubic-bezier(0.2,0,0.2,1),
            border-color 120ms cubic-bezier(0.2,0,0.2,1);

/* transform or opacity: use base */
transition: transform 200ms cubic-bezier(0.2,0,0.2,1);
transition: opacity 200ms cubic-bezier(0.2,0,0.2,1);
```

Do not use `transition: all`. It causes layout-triggering properties to animate.

### Reduced motion

Always honor user preference:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## Opacity

`0` = 0 · `5` = 0.05 · `10` = 0.10 · `20` = 0.20 · `40` = 0.40 · `60` = 0.60 · `80` = 0.80 · `90` = 0.90 · `100` = 1

Disabled elements: `opacity: 0.48` or use `text-disabled` (`#A9A8A6`)
for text-only disabled state.

---

## Focus

Every interactive element must have a visible focus ring.

```css
outline: 3px solid #F76922;
outline-offset: 2px;
/* or via box-shadow: */
box-shadow: 0 0 0 3px rgba(247, 105, 34, 0.40);
```

On dark (inverse) backgrounds use `focusRingInverse`: `0 0 0 3px rgba(255,255,255,.60)`.

---

## Component recipes

How tokens combine for the most common UI elements. Use these as the
starting point. Don't re-derive them.

### Button (medium, primary)

```
height: 40px (controlM)
padding: 0 16px (componentPaddingM)
border-radius: 4px (radius-sm)
font: Barlow 600 14px/20px, tracking 0.02em (label)
background: #F76922 → hover #DC5310 → active #B0410B
color: #FFFFFF
transition: color/bg/border 120ms cubic-bezier(0.2,0,0.2,1)
focus: box-shadow 0 0 0 3px rgba(247,105,34,0.40)
disabled: opacity 0.48
```

### Input (text field)

```
height: 40px (controlM)
padding: 0 12px
border: 1px solid #D0CFCD (border-default)
border-radius: 4px (radius-sm)
font: Barlow 400 16px/24px (bodyM)
background: #FFFFFF (surface-page)
placeholder color: #7F7F82 (text-tertiary)
hover border: #A9A8A6 (neutral-400)
focus: border #F76922 + box-shadow 0 0 0 3px rgba(247,105,34,0.40)
error border: #C0392B (danger) + helper text in danger
```

### Card

```
background: #FFFFFF or #F7F7F6 (surface-raised)
border: 1px solid #E2E1DF (border-subtle)
border-radius: 8px (radius-md)
padding: 24px (componentPaddingL)
shadow: elevation-1 (xs)
hover: shadow → elevation-3 (md), transform: translateY(-2px)
transition: 200ms cubic-bezier(0.2,0,0.2,1)
```

### Modal

```
overlay: rgba(28, 43, 54, 0.56) covering viewport
panel background: #FFFFFF
panel border-radius: 12px (radius-lg)
panel padding: 24px–32px (componentPaddingL or space-8)
shadow: elevation-5 (xl)
z-index: 1200 (modal)
entrance: opacity + translateY(8px), 200ms ease-entrance
```

### Dropdown / Popover

```
background: #FFFFFF
border: 1px solid #E2E1DF (border-subtle)
border-radius: 8px (radius-md)
shadow: elevation-3 (md)
z-index: 1000 (dropdown) or 1100 (popover)
item padding: 8px 12px (space-2 / space-3)
item hover bg: #EFEEED (neutral-100)
```

### Toast

```
background: #1C2B36 (cercleGray) for default; semantic-subtle bg + strong text for status
color: #FFFFFF
border-radius: 8px (radius-md)
padding: 12px 16px (space-3 / space-4)
shadow: elevation-6 (2xl)
z-index: 1300 (toast)
auto-dismiss: 4–6s
```

### Section heading + body block

```
overline:  Barlow 700 12px/16px tracking 0.04em UPPERCASE, color #54565A
heading:   Barlow 600 32px/40px tracking -0.01em, color #1C2B36 (headingXL)
body:      Barlow 400 16px/24px (bodyM), color #54565A (text-secondary)
stack: 12px between overline and heading; 16px between heading and body
```

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

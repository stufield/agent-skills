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


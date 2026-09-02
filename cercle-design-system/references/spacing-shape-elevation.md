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

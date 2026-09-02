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

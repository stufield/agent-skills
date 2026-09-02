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

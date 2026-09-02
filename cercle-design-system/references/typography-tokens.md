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

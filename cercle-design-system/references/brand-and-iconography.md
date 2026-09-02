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


# Looped Design System Tokens

**Source:** `mobile/src/theme/tokens.ts`
**Purpose:** Design system reference for the Looped mobile app

## Brand Intent
"structured opportunity" — premium, professional, creator-first.

## Semantic Color Rules
- **Navy** = platform / infrastructure / trust / verified
- **Purple** = creator output / momentum / highlights
- **Gray** = neutral structure / draft / background

---

## Color Palette

### Navy Family
- navy900: `#000033`
- navy800: `#1F1F3D`
- navyInk: `#0D0D2B` (deep brand navy)

### Purple Family
- purple600: `#6C63FF`
- purple500: `#8B7BA5`
- purple400: `#A594C0`
- purpleVivid: `#A855F7`

### Gray Family
- gray900: `#000000`
- gray800: `#1F2937`
- gray700: `#4B5563`
- gray600: `#6B7280`
- gray500: `#9CA3AF`
- gray400: `#D1D5DB`
- gray300: `#E5E7EB`
- gray200: `#F3F4F6`
- gray100: `#F8F8FA`
- gray50: `#FDFCFE`
- white: `#FFFFFF`

### Status Colors

**Red:**
- red600: `#DC2626`
- red500: `#EF4444`
- red100: `#FEE2E2`
- red50: `#FEF2F2`

**Green:**
- green600: `#059669`
- green500: `#10B981`
- green400: `#34D399`
- green50: `#F0FDF4`

**Amber:**
- amber600: `#D97706`
- amber500: `#F59E0B`
- amber50: `#FEF3C7`

**Blue/Indigo:**
- blue500: `#3B82F6`
- indigo500: `#6366F1`
- indigo50: `#E0E7FF`

### Social Brands
- instagram: `#E4405F`
- linkedin: `#0A66C2`
- youtube: `#FF0000`
- tiktok: `#000000`

---

## Semantic Colors (export const colors)

### Brand Pillars
- `navy` — Primary UI anchor (trust, platform, verified)
- `navyDark` — Auth screens, deep emphasis
- `purple` — Creator momentum, accent highlights
- `purpleBright` — Vivid accent

### Backgrounds
- `background` — Page-level (gray100)
- `surface` — Cards, elevated (white)
- `surfaceSecondary` — Subtle inset (gray200)

### Text
- `textPrimary` — navy800
- `textSecondary` — gray600
- `textMuted` — gray500
- `textOnFill` — white (on navy/purple)

### Status
- `error`, `errorDark`, `errorLight`, `errorSurface`
- `success`, `successDark`, `successLight`, `successSurface`
- `warning`, `warningDark`, `warningSurface`
- `info`, `infoLight`, `infoSurface`

---

## Spacing (8pt grid)

- `xxs`: 4px
- `xs`: 8px
- `sm`: 12px
- `md`: 16px
- `lg`: 24px
- `xl`: 32px
- `xxl`: 40px

---

## Border Radius

- `xs`: 4px — tags, tiny chips
- `sm`: 8px — small buttons, pills
- `md`: 10px — inputs, compact buttons
- `lg`: 12px — cards, primary buttons
- `xl`: 16px — modals, bottom sheets
- `xxl`: 20px — large containers
- `full`: 9999px — avatars, circle buttons

---

## Elevation (Shadows)

### card
```js
{
  shadowColor: gray900,
  shadowOffset: { width: 0, height: 2 },
  shadowOpacity: 0.08,
  shadowRadius: 8,
  elevation: 4,
}
```

### cardSubtle
```js
{
  shadowColor: gray900,
  shadowOffset: { width: 0, height: 1 },
  shadowOpacity: 0.05,
  shadowRadius: 4,
  elevation: 2,
}
```

### modal
```js
{
  shadowColor: gray900,
  shadowOffset: { width: 0, height: -4 },
  shadowOpacity: 0.12,
  shadowRadius: 16,
  elevation: 8,
}
```

---

## Motion (fast & controlled)

- **fast** (micro interactions): 180ms, ease-out
- **standard** (expand/collapse): 240ms, ease-out
- **emphasis** (milestone events): 280ms, ease-out

---

## Usage Notes

1. Always use semantic colors (`colors.primary`, `colors.textPrimary`) over raw hex values
2. Maintain 8pt grid for spacing
3. Use elevation.card for list items and cards, elevation.modal for modals
4. Follow semantic rules: navy for trust/infrastructure, purple for creator highlights
5. All tokens are exported as `const` for tree-shaking

---

*Generated from mobile/src/theme/tokens.ts for design agent reference*
# Handoff: sanganivarun.me

## Overview
Personal site for Varun Sangani — a technical program manager who writes daily system-design posts. The site has four pages: homepage, Notebook (blog index), Projects, and About. This handoff captures a design session that simplified the blog index, redesigned the project cards, and cleaned up sitewide typography.

## About the Design Files
The HTML files in this bundle are **high-fidelity design references** built as prototypes — not production code to copy directly. Your task is to recreate these designs in your target environment (static HTML/CSS, a static site generator like Hugo/Eleventy/Astro, or a framework of your choice). Use the HTML/CSS here as the exact visual specification: measure colors, spacing, and type directly from the source.

The shared design system lives in `assets/styles.css`. It uses CSS custom properties (design tokens) throughout — read that file first before touching anything else.

---

## Design Tokens (`assets/styles.css`)

### Colors
```css
--paper:      oklch(0.994 0.001 95)   /* page background — warm near-white */
--paper-2:    oklch(0.978 0.001 95)   /* cards / insets */
--paper-3:    oklch(0.965 0.001 95)   /* hover wells */
--ink:        oklch(0.275 0.012 60)   /* primary text */
--ink-soft:   oklch(0.415 0.012 60)   /* secondary text */
--grey:       oklch(0.555 0.010 70)   /* meta, muted */
--grey-2:     oklch(0.660 0.008 70)   /* faint meta */
--rule:       oklch(0.890 0.002 95)   /* hairlines */
--rule-soft:  oklch(0.930 0.001 95)   /* fainter hairlines */
```

### Typography
- **Serif:** Newsreader (Google Fonts) — body text, headings, post titles
- **Mono:** IBM Plex Mono (Google Fonts) — nav, dates, tags, footer, meta labels
- Base font size: `19px`
- Base line-height: `1.65`

### Layout
```css
--measure:      40rem   /* reading column ~640px — used on blog, about, post pages */
--measure-wide: 54rem   /* wider layout — used on Notebook index and projects */
--pad:          clamp(1.25rem, 4vw, 2rem)  /* horizontal page padding */
```

---

## Screens / Views

### 1. Notebook Index (`blog.html`)
**Purpose:** Chronological list of all posts. Clean, archive-style. No descriptions, no tags shown here (tags live inside each post only).

**Layout:**
- Wrap: `max-width: var(--measure-wide)` (54rem), centered, horizontal padding `--pad`
- Small "Notebook" heading: `0.88rem` serif, `font-weight: 500`, `color: var(--ink)`, `margin-bottom: 1.4rem`
- Post list: `<ul>` with no list styling, `margin: 0; padding: 0`

**Each row (`.nb-row`):**
- `display: flex; justify-content: space-between; align-items: baseline`
- `gap: 1rem`
- `padding: 0.55rem 0`
- No border/separator between rows
- Entire row is an `<a>` tag linking to the post

**Title (`.nb-row__title`):**
- `font-size: 0.88rem`
- `font-family: var(--serif)`
- `font-weight: 400`
- `line-height: 1.4`
- `flex: 1`
- Underline on hover: `text-decoration-color` transitions from `transparent` → `var(--ink)` over `0.15s ease`

**Date (`.nb-row__date`):**
- `font-family: var(--mono)`
- `font-size: 0.79rem` (intentionally ~10% smaller than title)
- `color: var(--ink)`
- `white-space: nowrap; flex-shrink: 0`
- Format: `"June 06, 2026"` — full month name, zero-padded day, full year

**Responsive:** Below 480px, stack to column; date drops to `color: var(--grey)`

**Tags:** Tags are stored per-post in the backend and rendered only inside the post detail page — NOT shown in the index list.

---

### 2. Projects (`projects.html`)
**Purpose:** Grid of project cards with full-bleed background images.

**Layout:**
- Wrap: `max-width: var(--measure-wide)` with class `wrap--wide`
- Page heading: `.page-title` — `clamp(2rem, 5vw, 2.7rem)` serif
- Groups (Shipped / Building) labeled with `.proj-group__label` — `0.7rem` mono, `letter-spacing: 0.1em`, uppercase, `color: var(--grey)`
- Cards grid: `display: grid; grid-template-columns: repeat(2, 1fr); gap: 0.9rem`
- Single column below 540px

**Each card (`.proj-tile`):**
- `position: relative; overflow: hidden`
- `min-height: 220px`
- `display: flex; flex-direction: column; justify-content: flex-end`
- `border: 1px solid var(--rule-soft)`
- `border-radius: 14px`
- On hover: `transform: translateY(-2px)`, `box-shadow: 0 6px 24px oklch(0.2 0.01 60 / 0.07)`, `border-color: var(--rule)`
- Transition: `border-color .18s ease, transform .18s ease, box-shadow .18s ease`

**Background image (`.proj-bg`):**
- `position: absolute; inset: 0`
- Contains an `<image-slot>` web component (drag-and-drop image placeholder — see `assets/image-slot.js`)
- `image-slot` is `display: block; width: 100%; height: 100%; object-fit: cover`

**Gradient overlay (`.proj-tile::after`):**
```css
content: '';
position: absolute;
inset: 0;
background: linear-gradient(to top,
  var(--paper) 0%,
  oklch(0.994 0.001 95 / 0.85) 38%,
  oklch(0.994 0.001 95 / 0.1) 100%);
pointer-events: none;
```

**Text content (`.proj-content`):**
- `position: relative; z-index: 1`
- `display: flex; flex-direction: column; gap: 0.3rem`
- `padding: 1.1rem 1.2rem 1.2rem`

**`.proj-name`:** `1.04rem` serif, `font-weight: 500`, `letter-spacing: -0.01em`
**`.proj-desc`:** `0.9rem` serif, `color: var(--ink-soft)`, `line-height: 1.46`
**`.proj-cat`:** `0.64rem` mono, `letter-spacing: 0.03em`, `color: var(--grey-2)`

**image-slot web component:**
- Loaded via `<script src="assets/image-slot.js"></script>`
- Usage: `<image-slot id="proj-1" shape="rect" placeholder="project screenshot"></image-slot>`
- Each slot needs a unique `id` for persistence (drag-and-drop saves to localStorage by id)
- Before any image is dropped, shows a hatched placeholder pattern

---

### 3. Shared Header & Nav
- Brand: `"Varun Sangani"` — `1.12rem` serif, `font-weight: 500`, link to `index.html`
- Nav links: `0.72rem` mono, `letter-spacing: 0.04em`, uppercase, `color: var(--grey)`
- Active page: `color: var(--ink)`, `border-bottom: 1px solid var(--ink)`
- Nav labels (in order): **Notebook** (href: `blog.html`), **Projects**, **About**
- Note: the nav label was renamed from "Blog" → "Notebook" sitewide

### 4. Shared Footer
- `border-top: 1px solid var(--ink)` — full black rule (not `--rule`)
- Footer links and copyright text: `color: var(--ink)` — full black (not muted grey)
- Link hover: `opacity: 0.6` transition
- Links: GitHub, LinkedIn, Email
- Copyright: `© 2026 Varun Sangani`

---

## Interactions & Behavior

| Element | Interaction | Details |
|---|---|---|
| Nav links | Hover | `color: var(--ink)` |
| Post rows | Hover | Title underline fades in (`text-decoration-color` → ink, 0.15s ease) |
| Project cards | Hover | Lift + shadow (`translateY(-2px)`, box-shadow, border darkens, 0.18s ease) |
| Footer links | Hover | `opacity: 0.6` (0.15s ease) |

---

## Tags / Backend Integration

Tags are associated with posts at the data level. The `.tag` class is defined in `styles.css`:
```css
.tag {
  font-family: var(--mono);
  font-size: 0.66rem;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  color: var(--grey);
  border: 1px solid var(--rule);
  border-radius: 999px;
  padding: 0.08rem 0.5rem;
}
```
Tags render as pill chips inside post detail pages only — not in the Notebook index. The backend should store tags per post and inject them into the post template.

---

## Files in This Bundle

| File | Purpose |
|---|---|
| `blog.html` | **Notebook index** — the main design reference for the blog list page |
| `projects.html` | **Projects page** — full-bleed card grid |
| `index.html` | Homepage |
| `about.html` | About page |
| `posts/designing-a-rate-limiter.html` | Example post detail page |
| `assets/styles.css` | **Shared design system** — read this first |
| `assets/image-slot.js` | Drag-and-drop image placeholder web component |

---

## Key Design Decisions to Preserve

1. **No descriptions in the Notebook index.** Titles only. Resist the urge to add excerpts.
2. **Date format is always `"Month DD, YYYY"`** — full month, zero-padded day, four-digit year.
3. **Tags only inside posts**, not in the index.
4. **Footer is full black** — `var(--ink)` for both the rule and the text.
5. **Two type families only** — Newsreader (serif) for content, IBM Plex Mono for all metadata.
6. **No separator rules** between notebook rows.
7. **Project card image is full-bleed** — the image is the card background, text overlays at the bottom via gradient.

---
name: Aura TESLA Opt. V5
description: Classical Mediterranean materials with an inspectable technical interior.
colors:
  paper: "#f6f2e8"
  ink: "#254b57"
  body: "#40585d"
  gold: "#987634"
  line: "#cfc9b9"
  blue: "#e1ebed"
  red: "#aa4932"
typography:
  display:
    fontFamily: "Cinzel, Georgia, serif"
    fontWeight: 500
    letterSpacing: "-0.025em"
    lineHeight: 1.14
  headline:
    fontFamily: "Cormorant Garamond, Georgia, serif"
    fontSize: "clamp(36px, 3.5vw, 53px)"
    fontWeight: 500
    lineHeight: 1.1
    letterSpacing: "-0.02em"
  title:
    fontFamily: "Cormorant Garamond, Georgia, serif"
    fontSize: "30px"
    fontWeight: 500
    lineHeight: 1.2
  body:
    fontFamily: "Space Grotesk, sans-serif"
    fontSize: "16px"
    fontWeight: 400
    lineHeight: 1.75
  label:
    fontFamily: "Space Grotesk, sans-serif"
    fontSize: "12px"
    fontWeight: 400
rounded:
  square: "0"
spacing:
  compact: "10px"
  related: "24px"
  separated: "25px"
  panel: "48px"
components:
  button-primary:
    backgroundColor: "{colors.red}"
    textColor: "#fff9ef"
    typography: "{typography.label}"
    rounded: "{rounded.square}"
    padding: "15px 22px"
  button-primary-hover:
    backgroundColor: "#853b29"
    textColor: "white"
  text-link:
    textColor: "{colors.ink}"
    typography: "{typography.label}"
    padding: "6px 0"
  select:
    backgroundColor: "#f6f8f5"
    textColor: "{colors.ink}"
    typography: "{typography.label}"
    rounded: "{rounded.square}"
    padding: "10px"
  navigation:
    typography: "{typography.label}"
---

# Design System: Aura TESLA Opt. V5

## Overview

**Creative North Star: "Aura's classical Mediterranean world"**

Warm stone, Aegean ink and restrained gold carry the user-pinned Aura identity into technical hardware presentation. Classical landscape imagery supplies atmosphere; open editorial sections and ruled specifications make the hardware inspectable.

Cinzel, Cormorant Garamond and Space Grotesk are intentionally inherited brand faces. The shipped static site self-hosts them through `WEB/assets/fonts.css`. This record derives from `WEB/styles.css`, `WEB/index.html` and `WEB/app.js`; it describes implemented patterns rather than a new direction.

**Key Characteristics:**

- Warm paper and cool blue section fields.
- Inscription-style identity with expressive serif headings.
- Open columns, square controls and fine dividing rules.
- Technical information presented in readable lists and tables.

## Colors

The palette pairs warm stone with muted blue-green text and small gold and terracotta accents. Frontmatter preserves the CSS custom-property names and exact values.

### Primary

- **Terracotta (`red`):** primary actions, link hover and keyboard focus.

### Secondary

- **Restrained gold (`gold`):** italic supporting statements, identity detail and selected-tab underline.

### Neutral

- **Warm stone (`paper`):** page ground, menu surface and image washes.
- **Aegean ink (`ink`):** titles, brand and emphasized technical values.
- **Slate body (`body`):** prose and default controls.
- **Stone rule (`line`):** separators and quiet control borders.
- **Pale blue (`blue`):** architecture field; related pale green surfaces appear in the parts section without forming a general tonal scale.

### Named Rules

**The Accent Has a Job Rule.** Terracotta signals action or focus; gold supports emphasis and selection.

## Typography

**Display Font:** Cinzel, with Georgia and serif fallbacks.

**Body Font:** Space Grotesk, with sans-serif fallback.

**Editorial Font:** Cormorant Garamond, with Georgia and serif fallbacks.

**Character:** Inscription-like identity meets soft editorial serifs and precise technical sans-serif text. Italic serif phrases provide emphasis inside headlines without adding decorative labels.

### Hierarchy

- **Display:** Cinzel identifies the product and brand. The hero uses a responsive size (`clamp(52px, 5.25vw, 84px)`) and a mobile override (`58px`); the recurring brand is smaller and uses weight 600.
- **Headline:** the frontmatter headline role serves section headings, with mobile size (`40px`) and contextual closing-section adjustments.
- **Title:** serif titles introduce principles and explorer content; document-link titles use a slightly smaller size (`27px`).
- **Body:** the frontmatter body role is the inherited base. Actual editorial copy generally uses (`14–15px`); vision paragraphs limit measure (`62ch`) and principle descriptions (`56ch`).
- **Label:** the recurring compact sans-serif role serves controls and navigation. Table headings use (`11px`) with weight 500.

### Named Rules

**The Three Voices Rule.** Use Cinzel for identity, Cormorant Garamond for editorial hierarchy, and Space Grotesk for prose, controls and specifications.

## Layout

The shared section inset combines generous vertical space (`104px`) with fluid horizontal gutters (`max(7%, calc((100vw - 1280px)/2))`). At the intermediate breakpoint (`1050px`) sections use (`80px 6%`); at mobile (`760px`) they use (`65px 6%`). Repeated related-content gaps cluster around the frontmatter spacing values rather than a formal mathematical scale.

Desktop sections use open two-column arrangements with differing ratios appropriate to the content. Mobile stacks these columns and moves supporting copy below headings. The parts table keeps horizontal scrolling and a sticky header inside a height-limited region (`505px`); it does not crush data columns to fit a phone.

The image-led hero places copy beside artwork on desktop and above a lower image on mobile. Preserve legible paper washes over imagery. The header is absolute rather than sticky. A wide-screen adjustment begins at (`1700px`).

## Elevation & Depth

Depth comes primarily from artwork, transparent paper washes and alternating section tones. Text sections and specification rows remain open and flat. Two subtle ambient shadows distinguish raised interactive surfaces; neither is a hard offset shadow.

### Shadow Vocabulary

- **Primary action:** (`0 5px 18px #5f38241a`) provides a slight lift beneath terracotta actions.
- **Mobile navigation:** (`0 12px 20px #254b5712`) separates the expanded menu from content.

### Named Rules

**The Open Surface Rule.** Separate editorial content with space, section tone and fine rules, following the implemented open lists and columns.

## Shapes

Controls and image containers have square corners. Action and menu icons use inline SVG. Thin rules define navigation tabs, specification rows, principle articles and document links. Rectangular image crops retain the original landscape and technical drawing character. The build does not establish a reusable rounded-card or chip system. Sidecar tonal ramps are synthesized panel previews, not additional shipped palette tokens.

## Components

### Buttons

Compact, square and confident. Primary actions use the frontmatter colors and padding with an ambient shadow. Mobile padding tightens (`14px 17px`). Hover darkens the terracotta fill, with background and color transitions (`0.25s`). Text actions use an underlined rule (`1px solid #a48c5b`) and turn terracotta on hover.

All links, buttons, selects and focusable regions share a visible terracotta outline (`3px`, offset `5px`).

### Inputs / Fields

The native select uses a pale surface, square border (`1px solid #a7b7b5`) and compact sans-serif text. Its label remains external and visible. It is disabled while component data loads; a nearby live status gives loading, result counts or the download fallback. No custom error or disabled visual treatment is established.

### Navigation

Small sans-serif links form a horizontal row with restrained tracking (`0.03em`) and spacing (`30px`, reduced to `18px` at the intermediate breakpoint). At mobile the Menu control opens a full-width paper panel with vertically spaced links. Expanded state is exposed through ARIA; link activation closes the panel and Escape restores focus to the menu button.

### Hardware Explorer

An image and open text panel share a pale blue field. Four text tabs use a thin baseline with a gold selected underline; selection also changes text color. Arrow keys, Home and End move selection and focus. Specifications appear as ruled definition-list rows with right-aligned values. On mobile the image sits above the panel.

### Ruled Lists and Tables

Principle articles and document links use bottom rules rather than card shells. Serif titles sit above compact supporting copy. The parts table uses a pale sticky heading, fine row rules and right-aligned quantities and costs. Preserve the scroll region's visible keyboard focus and readable column widths.

### Motion

The hero arrives once with a small vertical movement and clipping reveal (`1.2s`, `cubic-bezier(.16,1,.3,1)`). It is a surface-specific entrance, not a reusable animation mandate. Reduced-motion preference disables animations, transitions and smooth scrolling.

## Do's and Don'ts

### Do:

- **Do** preserve the inherited Aura faces and classical Mediterranean imagery.
- **Do** use terracotta for actions and focus, with gold for supporting emphasis and selection.
- **Do** keep editorial sections open and technical information separated by fine rules.
- **Do** retain mobile stacking, table scrolling and visible keyboard focus.

### Don't:

- **Don't** replace the pinned identity with a different visual world.
- **Don't** turn one-off image washes or page-specific measurements into general tokens.
- **Don't** infer a rounded-card system from this build's open sections.

Not canonized: the build's 9–10px annotations are not a recommended general text scale; one-off image washes and hero measurements remain surface-specific. Decorative Greek symbols were removed and action icons use inline SVG in the finished build.

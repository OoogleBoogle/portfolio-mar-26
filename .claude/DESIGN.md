# Design System: Editorial Specification

## 1. Overview & Creative North Star: "The Analog Disruptor"

This design system is a digital manifesto against the sterile, pixel-perfect layouts of the modern web. Our Creative North Star is **"The Analog Disruptor"**—an aesthetic that honors the raw, tactile energy of 1970s DIY punk zines and xeroxed gig posters. 

We are moving beyond the "template" look by embracing intentional chaos. This system leverages an **Anti-Grid Philosophy**: elements should appear pasted-on, rotated, and layered with the urgency of a midnight flyer run. We replace standard UI logic with high-contrast clashing colors, heavy grain textures, and brutalist typography to create an experience that feels alive, rebellious, and unapologetically human.

---

## 2. Colors & Textural Depth

The palette is built on the friction between "dirty" tactile backgrounds and electric, synthetic accents. 

### The Palette
- **Base Surface (`background` #f7f7f2):** A "dirty paper" off-white that serves as our canvas.
- **Electric Accents:** `primary` (#5f5f00 / #ffff00) and `secondary` (#a8216e / #ff69b4) provide the high-voltage "shock" required for interaction.
- **Heavy Ink:** `on-surface` (#2d2f2c) is not a soft grey; it is deep, heavy toner black.

### The "No-Line" Rule
Prohibit 1px solid borders for sectioning. Boundaries must be defined by:
1.  **Background Shifts:** Use `surface-container` tiers to define "scraps" of paper.
2.  **Texture:** Use halftone patterns or heavy grain to separate content blocks. 
3.  **Tear Lines:** Instead of a line, use a CSS mask or SVG "torn paper" edge to terminate a container.

### Surface Hierarchy & Nesting
Treat the UI as a series of physical layers stacked on a workbench. 
- **Bottom Layer:** `background` (The table).
- **Middle Layer:** `surface-container-low` (The main zine page).
- **Top Layer:** `surface-container-highest` (A pasted-on scrap or photo).
Each inner container should use a slightly different tier to imply it was cut from a different sheet of paper.

### Signature Textures
- **Halftone Overlays:** Apply 15% opacity halftone dot patterns to `primary-container` backgrounds.
- **Xerox Grain:** Use a global noise filter (3-5% opacity) over the entire UI to kill the "digital" flatness.
- **Tape & X-Marks:** Use `outline-variant` tokens to create "tape" textures that hold components in place at 5-degree tilts.

---

## 3. Typography: Brutalist Friction

Our typography is a clash between the industrial and the literary.

- **The Headline Scale (`spaceGrotesk`):** This is our "Ransom Note." Use `display-lg` and `headline-lg` with tight letter-spacing (-0.05em) and occasional CSS transforms (scaleY(1.2) or skew) to mimic distorted photocopier stretching.
- **The Body Scale (`newsreader`):** Provides the "Typewriter" counterpoint. This serif font brings an editorial, investigative feel to the content.
- **Hierarchy as Impact:** Large `display-sm` labels should clash directly with small `body-md` text. Do not seek harmony; seek hierarchy through sheer scale disparity.

---

## 4. Elevation & Depth: Tonal Layering

Traditional shadows and "material" elevation are forbidden. We use **Physical Layering**.

- **The Layering Principle:** Depth is achieved by "stacking." A `secondary-container` (Hot Pink) element should overlap a `primary-container` (Electric Yellow) element. Use the `0.5` to `2` spacing tokens to create "tight overlaps" where elements feel physically crowded.
- **Ambient Shadows (The "Paste" Effect):** If a floating effect is required (e.g., a modal), use a high-spread, low-opacity shadow (#2d2f2c at 8%) to simulate the thickness of heavy cardstock lifted off a page.
- **Glassmorphism & Ink:** Use `backdrop-blur` on `surface-variant` containers at 60% opacity to create a "vellum paper" effect, allowing high-contrast colors underneath to bleed through softly.
- **The "Ghost Border" Fallback:** If containment is needed for an input, use `outline-variant` at 20% opacity with a `px` width. It should look like a faint pencil mark, not a structural border.

---

## 5. Components

### Buttons
- **Primary:** `surface-container-highest` background with a heavy 2px `on-surface` offset "shadow" (not a blur, a hard block). Text in `label-md` uppercase.
- **States:** On `hover`, rotate the button -2 degrees. On `active`, shift the offset shadow to 0.

### Input Fields
- **Styling:** No bottom line or full box. Use a `surface-container-low` background with a `body-md` font.
- **Error State:** Use `error` (#b02500) text with an "X" mark texture overlapping the field.

### Cards & Lists
- **Cards:** Forbid divider lines. Use `spacing-6` for vertical breathing room. Each card should have a random rotation between -1deg and 1deg.
- **Lists:** Use `surface-container` shifts to distinguish rows. Leading elements should use halftone-filtered avatars.

### Additional Components: "The Tape Strip"
- Use a `primary-fixed` colored rectangle with `0.7` opacity and torn edges to act as a "highlight" or "tag" over images and headers.

---

## 6. Do’s and Don’ts

### Do:
- **Overlap Everything:** If two elements don't touch, the layout is too "safe." 
- **Embrace Asymmetry:** Use the `Spacing Scale` inconsistently—pair a `10` top margin with a `3` bottom margin.
- **Use "X" Marks:** Use the `on-surface` color to strike through deleted or inactive items manually.

### Don’t:
- **Don't use Rounded Corners:** Every `borderRadius` token is set to `0px`. Roundness is the enemy of the "Punk Zine."
- **Don't use Pure Grays:** Use the `surface` and `surface-container` tokens which have a slight yellow/warm tint to maintain the "aged paper" feel.
- **Don't Align to a Grid:** If a column of text is perfectly aligned to the left of an image, nudge one of them by `spacing-2` to break the digital perfection.

---

## 7. Accessibility & Readability
While we embrace "Brutalist Mess," readability is paramount. 
- **Contrast:** Always ensure `on-surface` text sits on `surface` or `surface-container` tiers to maintain a 7:1 contrast ratio.
- **Focus States:** Use a high-contrast `inverse-primary` (#ffff00) thick 3px outline for keyboard navigation. It should look like a neon highlighter mark.
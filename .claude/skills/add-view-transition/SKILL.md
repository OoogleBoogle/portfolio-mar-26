---
name: add-view-transition
description: Add a new element to the view transition animation system in the punk-portfolio Astro site. Use this skill whenever a user wants to animate a new block, wire up a className to the page-load or navigation transitions, add vt-enter to an element, register a new view-transition-name, or asks "add transitions to .my-class" or "animate this element" in this project. Trigger even if the user just gives a className — you have everything you need to do the full wiring.
---

# Add View Transition to an Element

Wires up a new element to the site's two-mode animation system:
- **Page load**: the element rises in from below on first visit
- **Navigation**: the element falls off the bottom when leaving, then rises in when arriving

## What you need from the user

- **className** (required): the CSS class of the element to animate
- **page** (infer from context, ask if unclear): `home`, `about`, or `work`
- **--vt-delay** (optional): milliseconds to stagger the rise-in. If not given, read the page's existing `--vt-delay` values and suggest the next logical slot (typically last delay + 30ms)
- **exit-delay** (optional): milliseconds to delay the fall-out. Defaults to the same value as `--vt-delay`

## The three steps

### Step 1 — HTML: add `vt-enter`

Find the element's HTML opening tag in `src/pages/{page}.astro` and add `vt-enter` to its classes:

```html
<div class="my-block vt-enter">
```

### Step 2 — CSS: add `view-transition-name` and `--vt-delay`

In the same `.astro` file, find the element's CSS rule and add:

```css
.my-block {
  view-transition-name: vt-{page}-{short-name};
  --vt-delay: 80ms;
  /* existing styles unchanged */
}
```

**Naming**: `vt-{page}-{short-description}` — e.g. `vt-work-summary`, `vt-about-skills`. The name must be globally unique across all pages. No two elements anywhere on the site can share a `view-transition-name` at the same time.

### Step 3 — `global.css`: register exit and entry

Open `src/styles/global.css`. Inside `html:active-view-transition-type(slide)`, two edits:

**1. Add the name to the `::view-transition-new` selector block** (the comma-separated list near the top of the type block):

```css
&::view-transition-new(vt-{page}-{short-name}),
```

All names in that block share one rule body — just append the new selector to the list.

**2. Add an exit rule** in the correct page group (home/about/work), ordered by delay:

```css
&::view-transition-old(vt-{page}-{short-name}) { animation: fall-out 0.4s ease-in {exit-delay}ms both; }
```

## Optional CSS properties

| Property | Where | Effect |
|---|---|---|
| `--vt-delay` | element's CSS rule | Stagger for rise-in on page load AND navigation entry |
| `--vt-opacity` | element's CSS rule | Target opacity after animation completes (see below) |
| exit delay | `global.css` `fall-out` rule | Stagger for fall-out during navigation |

Exit delay is set directly in `global.css` — not via a custom property — because exit animations run on pseudo-elements (screenshots of the old page), not live DOM elements, so they can't read CSS variables from the real element.

## Elements with custom opacity

If the element has an `opacity` value other than `1`, you **must** use `--vt-opacity` or the animation will override it. The `rise-in` keyframe ends at `opacity: var(--vt-opacity, 1)` — without `--vt-opacity` set, it snaps to fully opaque after animating in.

**When you see `opacity: X` on the element's CSS rule**, replace it like this:

```css
.my-block {
  view-transition-name: vt-{page}-{short-name};
  --vt-delay: 80ms;
  --vt-opacity: 0.2;        /* ← declare the target opacity */
  opacity: var(--vt-opacity); /* ← reference it */
}
```

**Also check for `translate` conflicts.** If the element uses `translate` for positioning (e.g. `translate: 0 -50%` for vertical centering), the `rise-in` keyframe will override it because both use the same `translate` property. Fix by replacing the `translate`-based centering with an equivalent using `top` + `margin-top`:

```css
/* Before — conflicts with rise-in */
top: 50%;
translate: 0 -50%;

/* After — translate is free for the animation */
top: 50%;
margin-top: -Xrem; /* half the element's known height */
```

## Why `translate` not `transform: translateY()`

The keyframes use the CSS `translate` property, not `transform: translateY()`. Many elements on this site already have `transform: rotate()`. Using `translateY()` inside `transform` would override the rotation. `translate` composes independently and leaves existing transforms alone.

## Checking the stagger sequence before committing to a delay

Before finalising, scan the page's `<style>` block for all existing `--vt-delay` values and list them in ascending order. Pick the next logical value — typically the highest existing delay plus 30ms. The timing envelope to know: exits finish at ~540ms (last exit: 140ms + 400ms), entries start at 600ms (the base offset in `global.css`), and the last entry finishes around 760ms.

## Full example

User: "add transitions to `.work-summary` on the work page with a 190ms delay"

**`src/pages/work.astro` HTML:**
```html
<section class="work-summary vt-enter">
```

**`src/pages/work.astro` CSS:**
```css
.work-summary {
  view-transition-name: vt-work-summary;
  --vt-delay: 190ms;
  /* ...existing styles */
}
```

**`src/styles/global.css`** — add to `::view-transition-new` selector list:
```css
&::view-transition-new(vt-work-cta),
&::view-transition-new(vt-work-summary) {   /* ← added */
```

**`src/styles/global.css`** — add exit rule in work group:
```css
&::view-transition-old(vt-work-cta)     { animation: fall-out 0.4s ease-in 140ms both; }
&::view-transition-old(vt-work-summary) { animation: fall-out 0.4s ease-in 190ms both; }  /* ← added */
```

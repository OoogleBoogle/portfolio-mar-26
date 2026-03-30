# View Transitions — How It Works

Blocks animate on page load (rise in from below) and on navigation (fall out, then rise in on the new page). All of this is the native CSS View Transitions API — no JS animation library.

---

## The three pieces

### 1. Keyframes and helper classes (`src/styles/global.css`)

```css
@keyframes fall-out {
  from { translate: 0 0;     opacity: 1; }
  to   { translate: 0 100vh; opacity: 0; }
}

@keyframes rise-in {
  from { translate: 0 60px; opacity: 0; }
  to   { translate: 0 0;    opacity: 1; }
}
```

> **Why `translate` and not `transform: translateY()`?**
> Many elements already have `transform: rotate()`. The `translate` property composes independently — using `translateY` inside `transform` would override those rotations.

`.vt-enter` is the entry animation class:

```css
.vt-enter {
  animation: rise-in 0.5s cubic-bezier(0.22, 1, 0.36, 1) var(--vt-delay, 0ms) both;
}
```

The `--vt-delay` custom property controls stagger. Default is `0ms`.

`.vt-transitioning` pauses `.vt-enter` while a page transition is in progress (see below).

---

### 2. Named elements (`view-transition-name` + `--vt-delay` in page CSS)

Each animated block gets two things in its CSS rule:

```css
.my-element {
  view-transition-name: vt-page-name; /* must be globally unique */
  --vt-delay: 80ms;
  /* ...rest of styles */
}
```

`view-transition-name` opts the element out of the root screenshot and gives it its own capture. The browser creates `::view-transition-old(vt-page-name)` (exit) and `::view-transition-new(vt-page-name)` (entry) pseudo-elements for it.

---

### 3. Exit/entry rules in `global.css`

The `html:active-view-transition-type(slide)` block handles the exit animations and hides the entry pseudo-elements (entry is handled by the real elements instead):

```css
html:active-view-transition-type(slide) {
  /* Old page: staggered fall-out per element */
  &::view-transition-old(vt-page-name) { animation: fall-out 0.4s ease-in 80ms both; }

  /* New page pseudo-elements: hidden or delayed */
  &::view-transition-new(vt-page-name) {
    animation: rise-in 0.5s cubic-bezier(0.22, 1, 0.36, 1) calc(600ms + var(--vt-delay, 0ms)) both;
  }
}
```

The `600ms` base delay ensures entry only starts after the exit window closes (~540ms for the last block).

---

## How page load works

On first load (no transition), `.vt-transitioning` is not on `<html>`, so all `.vt-enter` elements play `rise-in` immediately with their individual `--vt-delay` stagger.

---

## How navigation works

1. User clicks a link. Browser captures screenshots of named elements on the current page.
2. `pageswap` fires on the old page (not used here).
3. Browser navigates. New page loads.
4. `pagereveal` fires on the new page (`Layout.astro`):

```js
window.addEventListener('pagereveal', async (e) => {
  await document.fonts.ready;
  if (e.viewTransition) {
    document.documentElement.classList.add('vt-transitioning');
    await e.viewTransition.finished;
    document.documentElement.classList.remove('vt-transitioning');
  }
});
```

5. `.vt-transitioning` on `<html>` pauses all `.vt-enter` animations on the new page's real elements.
6. The exit animations (`::view-transition-old`) play — blocks fall down staggered.
7. The entry pseudo-elements (`::view-transition-new`) are either hidden or delayed.
8. After `viewTransition.finished`, `.vt-transitioning` is removed. Paused `.vt-enter` animations resume from their `from` state and play.

> The net effect: exit plays fully, then entry plays. No overlap, no cross-fade.

---

## Adding a new animated block

**Step 1** — Add `vt-enter` and a unique name to the HTML element:

```html
<div class="my-new-block vt-enter">...</div>
```

**Step 2** — Add `view-transition-name` and `--vt-delay` to its CSS rule:

```css
.my-new-block {
  view-transition-name: vt-page-my-block; /* pick a unique name */
  --vt-delay: 120ms;                       /* stagger position */
  /* ...rest of styles */
}
```

**Step 3** — Register it in `global.css`. Two places:

```css
/* 1. Hide the new-page pseudo-element (inside the big selector list): */
&::view-transition-new(vt-page-my-block) {
  animation: rise-in 0.5s cubic-bezier(0.22, 1, 0.36, 1) calc(600ms + var(--vt-delay, 0ms)) both;
}

/* 2. Exit animation: */
&::view-transition-old(vt-page-my-block) { animation: fall-out 0.4s ease-in 120ms both; }
```

---

## Naming convention

All names follow `vt-{page}-{block}`:

| Page | Prefix |
|------|--------|
| `index.astro` | `vt-home-*` |
| `about.astro` | `vt-about-*` |
| `work.astro` | `vt-work-*` |

> **Constraint**: `view-transition-name` must be unique across the entire document at the moment the screenshot is taken. Two elements with the same name on a single page = the API silently opts both out.

---

## Timing reference

| What | Duration | Notes |
|------|----------|-------|
| Exit animation | `0.4s` + delay | `fall-out`, `ease-in` |
| Last exit starts | ~`140ms` | |
| Full exit window | ~`540ms` | `140 + 400` |
| Entry base delay | `600ms` | buffer after exit |
| Entry animation | `0.5s` | `rise-in`, spring easing |
| `::view-transition-group(*)` | `1.6s` | keeps the transition alive until last entry completes |

---

## Files to touch

| File | What lives there |
|------|-----------------|
| `src/styles/global.css` | Keyframes, `.vt-enter`, `::view-transition-old/new` rules |
| `src/layouts/Layout.astro` | `pagereveal` script — pause/resume logic |
| `src/pages/*.astro` | `vt-enter` class on HTML, `view-transition-name` + `--vt-delay` in CSS |
| `src/components/Nav.astro` | `view-transition-name: site-nav` — pins the nav during transitions |

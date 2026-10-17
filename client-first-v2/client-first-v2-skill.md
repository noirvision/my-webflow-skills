---
name: Client-First v2
description: Generate clean HTML + vanilla CSS following Finsweet Client-First v2 naming conventions, optimized for conversion via moden.club HTML-to-Webflow tool.
---

# Client-First v2 Skill for Webflow

## Purpose
Generate clean HTML + vanilla CSS that follows Finsweet's Client-First v2 naming conventions, ready for conversion via moden.club HTML-to-Webflow tool.

---

## Output rules (always follow)

- Output a single HTML file with inline `<style>` block. No separate CSS file.
- No Tailwind, no Bootstrap, no framework utility classes.
- No CSS variables (e.g. `var(--color)`). Use explicit literal values (`#111111`, `1.5rem`).
- No complex CSS selectors. Use simple class-based selectors only.
- Use Flexbox for layout. Avoid complex Grid tricks (`minmax`, `repeat`, `fit-content`).
- No `filter`, `backdrop-filter`, or blur effects.
- Use `rem` for spacing and font sizes. Base: 1rem = 16px.
- Use semantic HTML tags: `<section>`, `<div>`, `<h1>`–`<h6>`, `<p>`, `<a>`, `<span>`, `<img>`, `<button>`, `<nav>`, `<ul>`, `<li>`.
- Images: always set `object-fit: contain` for logos, `object-fit: cover` for photos.
- Images: always set a `background-color` as skeleton placeholder.
- Aspect ratio: use `aspect-ratio` instead of fixed `height` where possible.

---

## Class naming — Client-First v2 rules

### Two types of classes

**Utility class** — no underscore. Global, reusable across the entire project.
```
padding-global
padding-section-medium
container-large
text-size-large
text-weight-bold
text-color-primary
margin-bottom
spacing-clean
display-inlineflex
```

**Custom class** — has underscore. Specific to a component, section, or element.
```
hero_component
hero_content
hero_heading
hero_image-wrapper
pricing_card
pricing_card-header
faq_item
faq_title
team-list_headshot-wrapper
```

### Key rule
- `_` in class name = custom class (belongs to a folder)
- No `_` = utility class
- Component root always ends with `_component`: `hero_component`, `nav_component`
- Combo class variants use `is-` prefix: `button is-primary`, `section_header is-dark`

### Naming convention
Always general → specific:
```
text-size-large       ✓  (not large-size-text)
hero_image-wrapper    ✓  (not wrapper-image-hero)
team-list_headshot    ✓
```
No abbreviations, no shorthand. Full readable words only.

---

## Core structure (6 layers)

Every page section follows this nesting order:

```html
<div class="page-wrapper">
  <section class="section_[name]">
    <div class="padding-global">
      <div class="container-large">
        <div class="padding-section-medium">
          <!-- component content here -->
        </div>
      </div>
    </div>
  </section>
</div>
```

### Core structure classes

| Class | Purpose |
|---|---|
| `page-wrapper` | Outermost div wrapping entire page |
| `section_[name]` | Section identifier (e.g. `section_hero`, `section_features`) |
| `padding-global` | Left/right horizontal padding only |
| `container-large` | Max-width wrapper (also: `container-medium`, `container-small`) |
| `padding-section-medium` | Top/bottom vertical section padding (also: `small`, `large`) |

---

## Typography utility classes

```
heading-style-h1  heading-style-h2  heading-style-h3
text-size-xlarge  text-size-large  text-size-medium  text-size-small  text-size-xsmall
text-weight-bold  text-weight-semibold  text-weight-medium  text-weight-normal
text-color-primary  text-color-secondary  text-color-muted
text-align-center  text-align-left  text-align-right
```

---

## Spacing utility classes

```
margin-bottom  margin-top  margin-left  margin-right  margin-vertical  margin-horizontal
padding-bottom  padding-top  padding-left  padding-right  padding-vertical  padding-horizontal

Each available in sizes:
  -tiny / -xsmall / -small / -medium / -large / -xlarge / -xxlarge / -huge

spacing-clean   ← resets margin and padding to 0
```

Spacing blocks:
```html
<div class="spacer-medium"></div>
```

---

## Combo classes (variants)

```html
<a class="button is-primary">CTA</a>
<a class="button is-secondary">Learn more</a>
<section class="section_hero is-dark">...</section>
```

---

## Component structure example

```html
<div class="feature-card_component">
  <div class="feature-card_icon-wrapper">
    <img class="feature-card_icon" src="icon.svg" alt="Feature icon">
  </div>
  <div class="feature-card_content">
    <h3 class="feature-card_heading">Heading</h3>
    <p class="feature-card_description">Description text here.</p>
    <a class="button is-secondary" href="#">Learn more</a>
  </div>
</div>
```

---

## Navigation structure

```html
<nav class="nav_component">
  <div class="padding-global">
    <div class="nav_container">
      <a class="nav_logo-link" href="/">
        <img class="nav_logo" src="logo.svg" alt="Logo" style="object-fit: contain;">
      </a>
      <ul class="nav_menu">
        <li class="nav_item"><a class="nav_link" href="#">About</a></li>
        <li class="nav_item"><a class="nav_link" href="#">Services</a></li>
        <li class="nav_item"><a class="nav_link" href="#">Contact</a></li>
      </ul>
      <div class="nav_cta-wrapper">
        <a class="button is-primary" href="#">Get started</a>
      </div>
    </div>
  </div>
</nav>
```

---

## CSS structure in output file

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Section name</title>
  <style>
    .padding-global { padding-left: 2.5rem; padding-right: 2.5rem; }
    .container-large { max-width: 80rem; margin-left: auto; margin-right: auto; width: 100%; }
    .container-medium { max-width: 60rem; margin-left: auto; margin-right: auto; width: 100%; }
    .padding-section-medium { padding-top: 5rem; padding-bottom: 5rem; }
    .padding-section-small { padding-top: 3rem; padding-bottom: 3rem; }
    .padding-section-large { padding-top: 8rem; padding-bottom: 8rem; }
    .spacer-small { width: 100%; padding-top: 1rem; }
    .spacer-medium { width: 100%; padding-top: 2rem; }
    .spacer-large { width: 100%; padding-top: 3rem; }
    .spacing-clean { margin: 0; padding: 0; }
  </style>
</head>
<body>
  <!-- markup here -->
</body>
</html>
```

---

## What to avoid

- No CSS variables (`var(--spacing)`) — use literal values
- No Tailwind or Bootstrap classes
- No complex selectors (`.parent > .child + .sibling`)
- No `filter`, `backdrop-filter`, `blur()`
- No `repeat()`, `minmax()`, `fit-content` in grid
- No deep class stacking (max 2 classes per element)
- No auto-named classes (`div-block-1`, `container-5`)
- No abbreviations in class names (`hdg`, `btn-pr`, `img-w`)

---

## Moden.club compatibility

Optimized for moden.club/toolkit/html-to-webflow:
- All HTML + CSS in one file
- Vanilla CSS only
- Explicit literal values
- Simple Flexbox layouts
- Semantic HTML structure

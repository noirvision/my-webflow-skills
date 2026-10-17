---
name: webflow-build-section
description: Build a new page section in Webflow from a design reference (HTML/CSS export, Figma link, or screenshot). Use whenever the task is to add or rebuild a section on the canvas. Covers the mandatory HTML-first two-phase process, structure, and component decisions.
---

# Skill — Build a section in Webflow

## Phase 0 — Context, before touching the canvas

Do not start building on the first prompt. First:

1. Read this site's design system: variables, components, existing styles.
2. State back what you found, so it can be confirmed.
3. State whether an existing component fits this section, or whether it needs
   to be built from scratch. If unsure, ask — do not guess.

Reusing the wrong existing component is a common, observed failure. A section
with a genuinely new layout should be built fresh, not forced into a component
that was designed for a different section.

## Phase 1 — Code first, always

**Never build natively from a blank canvas with no code reference.** Testing
shows native-only builds produce unusable output: broken structure, misapplied
properties, no adherence to the design system.

Accepted inputs, in order of preference:
1. Clean semantic HTML + vanilla CSS, already following Client-First v2
2. A Figma link or design export
3. A screenshot (reconstruct against the existing design system)

If given only a description, draft the section in HTML/CSS first, then port it.
This two-phase process is mandatory, not a preference.

## Phase 2 — Insert

Use the WHTML builder to insert HTML/CSS as native Webflow elements. Work
section by section, not the whole page in one call.

Preserve the class names from the source HTML. They already follow Client-First
v2 — do not regenerate or "improve" them.

Answered in practice (2026-08/09): WHTML does carry source class names
through — but it drops classes on `h1`–`h3` and on the first text node inside a
heading, strips `:hover` and `var(...)` from its CSS, and writes each class a
second time as a raw `class` attribute. So after every insert:
1. query the inserted elements and re-apply any missing class;
2. add hover states with `update_style` + `pseudo: "hover"`;
3. bind tokens with `variable_as_value`;
4. remove the duplicate `class` attributes.

Create styles before markup that uses them when building with the element
builder — an unknown class is dropped without an error.

## Structure

Core layering:
`page-wrapper → section → padding-global → container → padding-section → content`

- Flexbox or Grid by default. Absolute positioning only on explicit request,
  with a stated reason.
- Padding lives on exactly one level — the section **or** its container, never
  both. Redundant nested padding is a repeated observed bug.
- `min-height` belongs on the section, not an inner container. State a reason
  if deviating.
- Full-width elements: `flex-stretch`, not `flex-center` with implicit width.
- The navbar sits outside `main-wrapper`, or `position: sticky` gives up at the
  footer on mobile. Every section needs its own `padding-section-*` — side
  padding never supplies vertical spacing.

## Components

- A block repeated 2+ times (cards, testimonials, logos, team members) should
  be a Component with configurable props — not copy-pasted markup.
- Use Variants for state or content differences (e.g. a "featured" card);
  use combo classes only for purely stylistic differences.
- Repeated static content that looks like it should be dynamic is a CMS
  candidate — flag it and ask, do not convert unprompted.

## Animation

Build animations as native IX3 interactions through the MCP (see the
`webflow-mcp` skill, `references/interactions.md`), not as custom code.
Describe them fully in the first prompt — trigger, direction, duration, easing,
behaviour on open and close — or expect several rounds of correction. Anything
that must remember state across a reload is not an animation: flag it.

## Definition of done

A section is not done when the elements exist. It is done when:
- classes match the naming rule and carry the section prefix
- all values bind to existing variables, nothing hardcoded
- the result has been checked visually on staging or in Designer
- no stale class references remain on any element
- every element has been checked for the classes it should carry — a correct
  class that was never applied is the most common defect, and class-level
  audits cannot see it
- the published page (not the API echo, not the Designer tree) has been
  compared against the source
- a single list of what still needs doing by hand in the Designer is handed
  over

See `references/known-issues.md` for the specific failure modes to check for.

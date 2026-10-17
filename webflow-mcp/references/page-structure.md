# Page structure, spacing and comparing against an original

## Shell

A Client-First page shell that survives contact with a sticky header:

```
body
└─ page-wrapper
   ├─ decorative background      (aria-hidden="true")
   ├─ navbar component           ← OUTSIDE the main wrapper
   ├─ main-wrapper               (tag main, id main-content)
   │  └─ section → padding-global → container-large → content wrapper
   ├─ footer component
   └─ legal bar
```

**The navbar must not sit inside the main wrapper.** `position: sticky` is
bounded by its parent: once the page scrolls past the end of that parent, a
sticky child is carried off screen with it. On desktop the footer is usually
below the fold and the bug stays hidden; on mobile the footer fills half the
viewport and the header visibly slides away. Getting this wrong on the first
page means getting it wrong on every page duplicated from it.

**Vertical spacing never comes from the side-padding class.** A section with no
`padding-section-*` class collides with the header above and the footer below.
Check it on every new section — this one recurs.

## Duplicating

Duplicate the cheapest page that already carries the chrome, then delete its
content sections. A legal page (chrome plus one section) is cheaper than a
marketing page with four.

Collection page templates cannot be duplicated. They start empty, so the shell
has to be rebuilt by hand — which means the rules above have to be applied
deliberately every time.

## Breakpoints

Webflow breakpoints are desktop-first max-width overrides. Tailwind `md:` is a
min-width rule. A value read from a Tailwind source belongs in the **opposite**
direction: a `md:` two-column rule means the one-column override goes on
`small` (≤767), not `medium` (≤991). Putting it on `medium` breaks the tablet
range only — the range nobody checks and every automated report notices.

## Comparing a Client-First build against a Tailwind original

A computed-style diff will report a large volume of differences that are not
defects. The same padding, gap or gradient lives on a different node in each
build: Client-First puts it on a wrapper, Tailwind puts it on the section
itself. The diff sees two nodes that disagree and cannot see that the rendered
result is identical.

Before acting on any group of differences, check which element actually carries
the property. On one build the three largest reported "defects" were all of
this kind, roughly a third of the entire report.

What is worth fixing is where the *same* node differs visibly: type size,
colour, tracking, breakpoint behaviour, missing state.

Two signals that make a report readable fast:

- **Identical counts across all widths and both themes** mean the defect is
  structural or typographic, not responsive — and if several pages share the
  number, it is one defect in a shared template.
- **A count that spikes on tablet only** is almost always an inverted
  breakpoint (see above).

Reports are also heavily multiplied: one wrong class on an accordion with forty
questions produces hundreds of rows. Count causes, not rows, before estimating
how much work is left.

## Structure vs. visual fidelity

When the brief is "faithful copy", agree explicitly what that covers. Visual
and behavioural identity is the goal; reproducing the original's DOM tree and
class organisation is not — especially when the original itself is inconsistent
across its own pages. The markup underneath should be clean Client-First.

Without that agreement, a computed-style diff will keep demanding that you
rebuild good structure into bad.

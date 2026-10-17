# Rule — Scope and safety

Applies to every task on this site, regardless of what was asked.

- Address the operator as **Val**. (This is a deliberate marker: if the reply
  does not use the name, these rules were not loaded — stop and check.)
- Pause and present a plan before creating anything new: collections, fields,
  components, or a batch of styles. Wait for approval, then build.
- Back up the site before any batch of canvas changes.
- On an unfamiliar site, start small: a metadata audit or a single class rename
  before a full section rebuild. This validates your read of the existing system
  while the cost of being wrong is still one element.
- Assume no access beyond what the connected account actually has. On
  client-owned workspaces the role may be narrower than the task implies — if an
  operation fails on permissions, report it, do not route around it.
- Never change CMS collection structure unless explicitly asked in that message.
- Prefer a page branch over editing production directly. Publish to staging for
  visual review before merging.
- The agent cannot see its own visual output. Never report a canvas task as
  complete on structural data alone — require a staging or Designer check.
- Interactivity comes in three tiers — pick the tier before building:
  1. **Visual, or a toggle that needn't survive a reload** (reveals, hover,
     scroll, load, stagger, a click that toggles a class or sets a variable):
     native IX3 interaction through the MCP. No JavaScript, no embed.
  2. **State that must persist or logic that computes** (a remembered theme,
     calculators, multi-step forms, filters, date pickers): not yours to
     improvise. Describe what it needs and stop — Val decides between a small
     head script and an AI Code Component.
  3. **Triggers the API doesn't expose** (navbar, dropdown, focus, blur,
     change) and Initial Appearance: list them for Val to set in the Designer.
- CSS first where CSS is enough. Performance comes first.
- Site head code is replaced on write, never appended: read it first, send the
  old content with the new.
- Before an API-only task is dropped as impossible, check the current tool list.
  The limits below were accurate on the date shown and have moved before.

## Platform limits — as of 2026-09-21

Works now (earlier versions of this file said otherwise):
- deleting elements (`remove_element`) and reordering them (`move_element`)
- binding an image on an existing element (`set_image_asset`)
- IX3 interactions — headless, no Designer tab needed
- site-level head/footer code (`data_scripts_tool`)

Still not possible through the API:
- `before`/`after` against a component instance as anchor — `append` on the
  shared parent instead; edit component definitions, not instances
- Initial Appearance; navbar/dropdown/focus/blur/change triggers
- renaming a class; deleting unused classes reliably (`remove_style` is flaky)
- anything bound to the current CMS item (filters, excluding it, linking a
  referenced item), bound date formats
- `width`/`height`/`sizes` attributes on images (reserved)
- vendor-prefixed CSS properties

## Access

- The MCP token is per workspace. A site moved to a client workspace answers
  404. Designer-role invites can't connect the MCP. Finish all API-only work
  (links, cleanup, SEO fields, redirects, head code) before a transfer.
- Element, style and asset tools need the Designer tab open and in front;
  headless tools don't. Follow the link the error returns.

## Known MCP bugs — check before trusting a result

- A style name that exists as a base class and inside a combo resolves to the
  wrong one when addressed by name alone. Pass `parent_style_names`; query
  first.
- After deleting an element, the class shown on a remaining element may be a
  stale reference from an unrelated page's style guide. Re-check before
  styling.

# Rule — Design tokens and variables

Applies to every value this agent writes on this site.

- Never hardcode color, font, spacing, radius, or border values. Always bind to
  a Webflow Variable.
- Before writing any value, check the site's existing variables and use the
  closest match. Do not invent a new variable when one already covers the case.
- Never introduce a new color variable without removing an existing one.
- Never use a custom CSS property in place of a native Designer control that
  already exists for that purpose.
- No `filter`, `backdrop-filter`, or blur effects of your own invention. If the
  source design has one (a frosted sticky navbar), reproduce it and say so in
  the report — fidelity to the source outranks this default.
- Bind tokens with `variable_as_value`. Never rely on `var(--x)` inside WHTML
  CSS: it is either discarded or silently creates a duplicate variable.
- Light/dark themes: one variable collection with a dark mode, bound to a theme
  class via `set_style_variable_mode`. Do not duplicate styles under `is-dark`
  combos.
- A font-family variable must match the registered custom font name exactly
  (`Satoshi Variable`, not `Satoshi`) — a mismatch falls back without warning.
- Images: always set `object-fit` and a `background-color` fallback. Prefer
  WebP or AVIF; the agent can compress assets directly through MCP.

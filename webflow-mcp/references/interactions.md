# Interactions (IX3) through the MCP

Available since the MCP update of 2026-09-21. `data_interactions_tool` creates,
updates and deletes native IX3/GSAP interactions — the same objects the
Interactions panel shows, editable there afterwards. **Headless:** no Designer
tab needed. Call its `guide` action once per session before the first create;
the notes below are the parts that bite.

## What goes where now

| Need | Route |
|---|---|
| Scroll reveal, hover, load animation, stagger, split text | IX3 via MCP |
| Click that toggles a class or sets a variable (menus, tabs, theme switch within a visit) | IX3 via MCP |
| State that must survive a reload (remembered theme, "don't show again"), follow OS colour scheme | Code — a head script or a Code Component. Explicit decision, never silent |
| Calculators, multi-step forms, anything with logic | Code Component |
| Navbar, dropdown, focus, blur, change triggers | Not in the API. Designer only |
| Initial Appearance (resting styles) | Not in the API. Designer, or set resting values on the class |

## Payload traps — all of these "succeed"

- **Unknown keys are silently dropped.** `timing.easing` is not a key — `ease`
  is, as a preset index (`2` = power1.out) or an ease object, never a string.
  Plugin options go under `config.pluginConfig`. If a field you sent is missing
  from the response, you addressed it wrong; it is not an unsupported feature.
- **A bare `duration` number is seconds.** `400` is almost seven minutes. Use
  `"400ms"` or `0.4`.
- **A To (`tt: 0`) reveal into the resting value does nothing.** `preset-fade-in`
  on an element already at opacity 1 stores, reads back, passes every check and
  is invisible. Give reveals both ends: `tt: 2` with `opacity: ["0%","100%"]`.
- **Opacity lives under `wf:transform`**, not `wf:style`. Values are arrays,
  never `{from, to}` objects. No GSAP `+=` / `<`.
- **A reveal target is invisible until it plays.** Never put a click or hover
  trigger on the element being revealed — put it on a visible parent or sibling.
- **`control: "play"` is dead after the first run.** For anything that must
  respond to every click (class toggles, Sets), use `restart`.
- **The body is targeted with `wf:selector`** (`"body"`) in actions; `wf:body`
  is trigger-only.
- **Schema errors abort the whole batch; guard errors fail one item.** After a
  `-32602`, nothing was written — check before retrying or you write duplicates.
  Check both error envelopes (`status: "error"` and `error: {...}`).
- **Every call needs `agent_id` and `session_id`** (`start` on the first call,
  then the issued `ses_…` value for the rest of the task).
- **Created ≠ played.** Verify on a published or preview page.

## Recipe: theme switch without JavaScript

1. Put the dark values in a variable-collection mode and bind that mode to a
   theme class with `data_style_tool` → `set_style_variable_mode` (one class
   switches every token).
2. Rules that need descendant selectors (a divider inside the navbar, swapping a
   light/dark image) can't be native styles — put them in site head CSS scoped
   under the theme class. Read the head first; the write replaces it.
3. Interaction: `wf:click` on the toggle, `control: "restart"`, one action
   `tt: 3`, target `wf:selector` `"body"`, properties
   `{"wf:class": {"class": {"operation": "toggleClass", "selectors": [themeStyleId]}}}`.

Result: works within a visit, resets on reload, ignores the OS setting. If the
brief needs either, that part is code — say so before building.

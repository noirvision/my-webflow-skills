# Failure modes — check these before reporting done

Collected from real builds, 2026-07 → 2026-09 (a client rebuild, v1–v11, Bentolio and
Startup-template tests). Master copy: `webflow-mcp/references/failure-modes.md`.
The site pack's `known-issues.md` is a copy of this file — edit here, then copy.

## Rejected calls, and what they actually mean

| Message | Cause | Fix |
|---|---|---|
| "Expected single root element" | Two sibling elements in one WHTML string | One root per call; issue a separate call per sibling |
| "styles not found" listing classes that exist | The exact combo chain does not exist yet | Create it with `parent_style_names`, then apply |
| "styles not found", chain does exist | Intermittent multi-class `set_style` bug | Retry up to 2×; only then fall back to one merged class |
| "Unsupported media query" (WHTML) | Arbitrary `@media` | Only `screen and (max-width: 991/767/479px)` or `screen and (min-width: 1280/1440/1920px)` |
| "Nested selectors are not allowed" (WHTML) | `.card strong`, `.parent svg` | Flat single-class selectors only; give the inner element its own class |
| Vendor-prefixed property rejected | `-webkit-background-clip`, `-webkit-text-fill-color` | Not settable via API. Gradient text: reuse an existing Designer-made class |
| "Connect this Collection List to a Collection before adding elements" | Built inside an unbound list | Set the list's source first |
| "expected { collectionId } or null" | Passed a bare id string as the source | Use `static_json` with `{"collectionId":"..."}` |
| "Invalid filter: expected fieldSlug" | Used `fieldId` in a filter or sort | Filters and sorts take slugs |
| "Invalid sort direction" | Wrote `desc` | Webflow wants the word `descending` |
| "Operator 'eq' is not valid for field (boolean)" | Guessed the operator | The error lists valid operators — use them |
| "does not support bound filter values in the Designer" | Tried to filter by the current item | Designer-only; stop here |
| "Setting X is not applicable to this element" | Element type does not support that setting | Usually a `Span` (no text binding) or a `Block` where a `Link` was needed |
| `set_text` unsupported on a TextBlock | Wrong action for the type | `set_settings`, key `text`, `static_text` |
| Internal error on `set_attributes` for an Image | `width`, `height`, `sizes` are reserved | Leave them to Webflow's responsive images. Test a `data-test` attribute first to prove the tool works |
| Element type rejected in a schema | Tried to create a component instance via the element builder | Use the component tool's insert action |
| "Cannot insert/move relative to a component instance" | Used an instance as `before`/`after` anchor | `append` on the shared parent, in the order you need |
| Repeated 429 | Frequency limit, roughly ten consecutive calls | Pause five to ten minutes; the payload is fine |
| 404 `resource_not_found` on a site that worked yesterday | Site moved to another workspace; token is per-workspace | Not an auth error, the id is unchanged. Needs the MCP app installed in the new workspace by an admin |
| "Unable to connect to Webflow Designer" | Tool needs the Designer bridge | Open the Designer link it returns, keep that tab in front. Headless tools (interactions, CMS, scripts) don't need it |
| "Style with name h1 is reserved and cannot be used" | Tried to create a tag style (`h1`, `p`, `a`…) with `create_style` | Tag styles can't be created through the API. Set any one property on "All H1 Headings" in the Designer once; after that `update_style("h1")` works (verified 2026-09-24) |
| `Style "h1" not found` on `update_style` | That tag style has never been touched in the Designer — it doesn't exist in site data yet, even though the Designer lists it under Inheritance | Same fix. `body` always exists. Note the first Designer touch writes Webflow's defaults into the tag (h1: 20px top margin, 38px/44px, 700) — override them, don't just add |
| "expected array, received string" on a call that worked earlier in the session | Connector's tool schema out of step with the server | Refresh the tool list in the connector settings or reconnect, then retry. Seen in Webflow's own demo (2026-09), not reproduced here; cause unknown |

## Silent failures — nothing errors, the result is wrong

**A style name that exists twice resolves to the wrong one.** The most
dangerous bug on these builds. If `padding-global` exists as a standalone class
and also as `.legal_bar.padding-global`, `update_style` / `create_style` /
`set_style` by name alone resolves to the combo — no error, the edit lands
elsewhere. Always pass `parent_style_names` for combos, query first to see which
variant resolves, and give modifiers unique names (`pricing_elite-muted`, not
`is-muted`). Editing variables by id is always unambiguous.

**`set_style` replaces the whole class list.** It does not append. Send every
class the element should keep.

**Classes applied before they exist are dropped.** `data_element_builder` drops
a class name that has no style yet. Create styles first, then build markup.

**WHTML drops classes on `h1`–`h3`** and on the first text node inside a
semantic heading. Run `query_styles` / query the elements after every insert
and re-apply what is missing.

**WHTML lowercases SVG camelCase tags.** `linearGradient` becomes
`lineargradient` and the gradient stops rendering. Build SVG gradients with
`data_element_builder` and `set_dom_config: {dom_tag: "linearGradient"}`.

**WHTML strips `:hover` and `var(...)` from its `css`.** Hover goes in a
separate `update_style` with `pseudo: "hover"`; variables are bound afterwards
with `variable_as_value`. `var(--x, fallback)` is inconsistent: sometimes it
auto-creates a real variable (seen with `--rounded`), sometimes it only warns
(`--text`). Never rely on it — and before creating a "proper" variable, check
`query_variables` for one WHTML already made.

**WHTML writes each class twice** — as a style and as a raw `class` attribute.
The two drift apart on any later rename. Remove the attribute with
`remove_attribute`.

**Images silently skipped on WHTML insert.** `upload_image_by_url` returns a CDN
URL (`cdn.prod.website-files.com`); WHTML matches images against the S3
`hostedUrl` from `list_assets`. Insert the `<img>` unbound, then bind each with
`set_image_asset` by asset id.

**Setting text on a heading wipes its children.** `set_settings` with `text` on
a heading replaces gradient spans and any other child. Set text first, append
spans after.

**Removing `href` removes the link binding**, not just the attribute value.

**Attributes set at creation time may not stick.** When several attributes are
passed together while creating an element, some can be dropped with no error.
Re-apply them in a follow-up call and verify.

**A created text block keeps its placeholder.** If `set_text` is not applicable,
"This is some text inside of a div block." remains. Insert a `<span>` via WHTML,
then remove the leftover raw `class` attribute.

**Rich text is normalised on write.** A nested `<p>` inside a `<blockquote>`
is unwrapped and the quote becomes a plain paragraph. Compare body lengths
before and after any bulk import — see `cms.md`.

**A collection link points at the template.** Caused by passing a page slug
alongside `collectionPage` mode. The page renders, the link 404s.

**Component props are not links.** A component whose nav items are driven by
text props may contain no `<a>` at all: every link in it is dead sitewide.
When auditing a component, check for link elements, not just correct text.

**A duplicated script include cancels itself out.** When a toggle "fires but
nothing changes", look for the same script at both site and page level. Two
handlers per click, the state flips twice.

**Site head code is replaced, not appended.** `set_site_freeform_code`
overwrites the whole location. Always `get_site_freeform_code` first and send
old + new together.

**A font falls back with no warning.** If a font-family variable does not
exactly match the registered custom font name (`Satoshi` vs `Satoshi Variable`),
text renders in the fallback. Verify against Site Settings → Custom Fonts.

**API state is not the published site.** Until Publish, the published HTML is
the old version. Check `view-source:` of the published page, not the API echo.

**Correctly defined classes that were never applied.** The most common defect
on large builds. Class-level audits cannot see it; only an element-by-element
pass or a rendered comparison can. Sibling: the class is applied but its
definition is incomplete — only a rendered comparison catches that.

**Invented class names.** Creating `hero_subtitle` when the site already uses
`plans_hero-lead` costs a create + swap + failed-cleanup cycle. Query the real
class before creating a page-specific one.

**Identical headings can't be told apart by type.** `element_filter: {type:
"Heading"}` returns look-alikes. Locate by `text` or by the parent section.

**Pass `siteId` and `pageId` explicitly** on every element call; defaults can
point at the wrong page.

## Figma side (when building a mockup through the Plugin API)

**`figma.createAutoLayout()` frames are opaque white.** A grouping-only frame
covers what is behind it; with light text inside it reads as vanished content.
Set `fills = []` on every grouping frame right after creation.

**`layoutSizingHorizontal = "FILL"` before `appendChild` throws**, and changing
`layoutMode` on an existing frame can reset sizing and collapse FILL children to
1px. Append first, set sizing after, then check for 1–2px-wide text nodes.

## Layout defects agents produce on the canvas

| Symptom | Cause | Fix |
|---|---|---|
| Element not full width | `flex-center` instead of `flex-stretch` | Fix on the base class, not per instance |
| Excess vertical space | `min-height` on the container instead of the section | Move to the section |
| Doubled spacing | Padding on both section and container | Keep one level |
| Layout collapses on resize | Absolute positioning instead of flex/grid | Rebuild with flow layout |
| Properties set via custom code | A native Designer control was bypassed | Rebuild with the native field |
| Text fields left empty | Elements inserted piecemeal, content lost | Re-check every text node against the source |

## Components

Per-instance text overrides work through
`data_component_props_tool → set_component_instance_prop_values`; the response
shows `hasOverride: true` when it took. Prefer this to `set_text` on instance
children. Turning an existing element into a component:
`data_component_tool → transform_element_to_component` (the element gets a new
id afterwards — re-query before the next call).

## Expectations

Element-by-element building is slow — a 5–6 section page can run 20+ minutes;
HTML-first plus WHTML is both faster and cleaner. Even with good instructions,
expect 2–3 rounds of correction on a new section. That is normal.

## Cleanup that has to wait

`remove_style` fails with an internal error often enough that unused classes
accumulate. Collect them and clear them in the Designer's unused-styles
cleanup in one pass at the end of a build.

Renaming does have an API path now: `data_style_tool → rename_style` (combos
via `parent_style_names`). Verified 2026-09-24 — the style id is kept, so every
element carrying the class follows. It does not touch raw `class` attributes
left behind by WHTML inserts, or head-CSS selectors keyed on the old name;
search for both after a rename.

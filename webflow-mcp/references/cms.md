# CMS, Collection Lists and collection page templates

## Payload shapes the API demands

These are inconsistent with each other. Do not infer one from another.

| Setting | Shape | Note |
|---|---|---|
| Collection List `source` | `static_json` with `{"collectionId":"..."}` | A plain id string is rejected: "expected { collectionId } or null" |
| `sort` | `static_json`, `[{"fieldSlug":"...","direction":"descending"}]` | **`fieldSlug`, not `fieldId`** — the only place Webflow wants a slug. `desc` is rejected; the word must be `ascending` / `descending` |
| `filters` | `static_json`, `[{"fieldSlug":"...","operator":"...","value":...}]` | Operators are per field type. Switch: `isOn` / `isOff` (not `eq`). Reference: `equals` / `doesNotEqual` / `isSet` / `isNotSet`. The error message names the valid set — read it rather than guessing again |
| Link to a collection item | `static_link` with `{"mode":"collectionPage"}` and **no `to`** | Adding `to: {pageSlug}` silently produces a link to the template path without the item slug. Looks like a broken template, is actually a malformed link |
| Reference sub-field binding | `field_id` as `refFieldId:::subFieldId` | How you reach an author's name or a category's name from inside a post |
| Date format | not settable | The `format` key on a binding is rejected. Every bound date needs its format set in the Designer |
| Page SEO on a template | `update_page_settings` with `{{wf {"path":"field-slug","type":"PlainText"} }}` | Open Graph image accepts the same shape with `"type":"ImageRef"` |

## Ordering

**Connect a Collection List to its collection before building anything inside
it.** Creating elements in an unconnected list fails outright: "Connect this
Collection List to a Collection before adding elements."

**Build inside the Collection Item, not the Collection List.** Elements placed
directly in the list are rejected silently. Bindings: text uses key `text`, rich
text `richText`; a page link is `{"mode":"page","to":"<pageId>"}` — `cms` and
`item` modes are rejected.

**Create a combo chain before applying it.** `set_style` with several class
names fails with "styles not found" when that exact chain does not exist yet.
Create it with `parent_style_names` first, then apply. Distinct from the
intermittent multi-class bug — check whether the chain exists before blaming
flakiness.

**Publish items, then the site.** `publish_site` does not publish CMS items.
Skipping the first step yields a live page with empty lists.

## Designer-only — do not search for an API path

Everything that references *the current or a related collection item* is
unreachable from the API. The errors are explicit: "does not support bound
filter values in the Designer", "must be a valid CMS item id", and
`get_bindable_sources` returns nothing for the `link` setting.

- Filtering a list by the current page's item — `Category is Current Category`
  on a category template, `Author is Current Author`, related posts by the
  current post's category
- Excluding the current item from a related list
- Linking to a **referenced** item's page — an author name on a post linking to
  that author's own page
- Date display format on any bound date
- Deleting an unused class (`remove_style` throws an internal error often
  enough to be unreliable). Renaming is no longer on this list — see
  `rename_style` in `failure-modes.md`

Build everything else, then hand over one consolidated list of Designer clicks.

## Consequences worth designing around, and telling the client about

**Rows-per-category on a listing page cannot be generated.** There is no
reverse lookup from a category to its posts inside a Collection List of
categories. Each row must be its own Collection List with a hardcoded category
id, heading and link. A new category will appear in anything driven by the
categories collection, but will not get its own row until someone opens the
Designer. Say this before building, not after.

**A hardcoded filter fails silently.** Delete and recreate that category and
the id no longer matches; the row renders empty with no error anywhere.

**There is no "if empty, use another field".** Either bind SEO fields to
required fields — title from Name, description from a required excerpt — and
drop the optional override fields, or make the override fields required. An
optional SEO field left blank ships a page with no description and nothing
warns you.

**Cards that are whole-page links cannot contain another link.** If a byline
inside a card needs to be clickable, the card cannot be one big `<a>` — the
link has to move onto the image and the heading separately. Decide this before
building the card, not after four copies of it exist.

## Seeding test content

Any required field left empty blocks item creation — text fields as much as
image fields. Create the schema with nothing required, import and wire the
templates, and switch fields to required at the end, once the content exists.
An agent that hits this mid-import may flip fields to optional on its own to
get the row in: that is a schema change, and it should say so.

Placeholder image URLs work — Webflow fetches and stores them as real assets,
which also means they need cleaning out of the asset library later.

Spread dates across a realistic range. Identical or sequential dates hide
sorting and layout problems that real content will expose.

## Rich text imports

**Webflow normalises rich text on write, and loses content without an error.**
Its blockquote holds inline content directly: `<blockquote><p>…</p></blockquote>`
from a legacy CMS gets unwrapped and the quote lands as a plain paragraph.
Flatten it to `<blockquote>…</blockquote>` before the push. Source: Webflow's
own migration demo (2026-09); not yet reproduced on our builds.

**Diff lengths after every bulk push.** Read each item back and compare the
rich-text body length with the source. A body that shrank — even by 25
characters — means something was stripped. This catches every kind of silent
normalisation, not only blockquotes, and costs one read per item.

## Reading items (MCP update 2026-09-21)

Item queries now sort and filter on custom fields and across reference fields,
and paginate, in one request. For audits and bulk edits on large collections,
ask for exactly the items needed instead of pulling the collection and sifting
it — that used to burn tokens and sometimes broke the run before it finished.
This is about reading items; Collection List `filters` on the canvas still use
the shapes in the table above.

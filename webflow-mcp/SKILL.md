---
name: webflow-mcp
description: "Hard-won operating rules for building Webflow sites through the Webflow MCP — page shell and sticky-header structure, CMS collections and Collection Lists, IX3 interactions and animations, theme switching, exact API payload shapes, operations that are Designer-only, workspace access, and the verification discipline that API checks cannot replace. Use this whenever a task involves the Webflow MCP at all — building or duplicating pages, creating or styling classes, working with components, variables or site custom code, adding interactions, scroll or hover animations, setting up CMS collections, binding fields, filtering or sorting Collection Lists or items, publishing, transferring a site to a client workspace, or comparing a Webflow build against an original site. Also use it when a Webflow API call fails with a validation error, when a build looks correct through the API but wrong in the browser, or when planning how much of a feature can actually be automated."
---

# Working with the Webflow MCP

Every rule here comes from a failure on a real build. The API is usable and
fast, but it is inconsistent in its payload shapes and silent about several
whole categories of mistake. Reading this before starting saves hours.

## Read these first, by task

- Any CMS work — collections, Collection Lists, templates, bindings:
  `references/cms.md`
- Building or duplicating a page, section spacing, sticky header:
  `references/page-structure.md`
- Animations, scroll/hover/click interactions, a theme switch, deciding
  whether something needs code: `references/interactions.md`
- A call failed, or something built cleanly but renders wrong:
  `references/failure-modes.md`

## The four rules that matter most

**1. Apply a fix at the level the evidence came from.**

A size measured on an inner element belongs on the inner element, not its
parent. A value measured on one combo class belongs on that combo, not on the
base class. Both mistakes look correct in isolation, and both create a new
mirrored defect somewhere else — usually one that the next report blames on
something unrelated.

**2. Never invent a value.**

If a report says the original is 22px and nothing in the source explains where
22px comes from, stop and ask. A guess is indistinguishable from a measurement
once it is in the build, and far harder to unwind later than to query now.

**3. The API cannot see whether the site works.**

Classes, structure and ids can all be correct while the theme toggle, the
mobile menu or a sticky header is dead. Only a person clicking in a browser
finds that. So: never batch a change touching JS-dependent behaviour across
several pages. Do one page, stop, ask for an in-browser check, then apply the
rest.

**4. Name what you could not do.**

Several common CMS operations are Designer-only (see `references/cms.md`).
A related-posts block that shows every post, or a category page that lists the
whole collection, looks finished and is not. Never present a build as complete
without handing over the list of remaining Designer clicks.

## Access and tool surface

The MCP token is per workspace. A site moved to another workspace answers 404,
not an auth error. A Designer-role invite into a client workspace cannot connect
the MCP at all — so anything that needs the API (cleanup, redirects, SEO fields,
head code) is done **before** a site is transferred, or the client's admin
installs the MCP app in their workspace.

Some tools are headless (interactions, CMS, scripts, sites); element, style and
asset operations go through the Designer bridge and need the Designer tab open
and in front. The tool's error says which — follow the link it returns.

The tool list changes. Before telling anyone "the MCP can't do X", run
`tool_search` for it; a limit that was true last month may be gone
(interactions were impossible until 2026-09-21; `rename_style` appeared while
these notes still called renaming Designer-only).

## Rhythm of work

Query before you create. Check whether a class already exists and what it
actually contains before making a new one — a duplicate class with a slightly
different name is the most common form of quiet damage on these builds. When a
name exists both as a base class and inside a combo, target it with
`parent_style_names` or you will edit the wrong one.

Create styles before the markup that uses them, and query the result after
every WHTML insert — both tools drop classes without saying so.

Batch operations where the tool allows it, but expect a rate limit: roughly ten
consecutive calls is the practical ceiling before 429s start. That is a
frequency limit, not a daily quota — pause five to ten minutes and continue.

Publish is two operations when CMS is involved: items first, then the site.

Tag styles (`body`, `h1`–`h6`, `p`, `a`…) are editable with `update_style`
only once they exist in site data. `body` always does; any other tag needs one
property set on it in the Designer first. `create_style` refuses tag names.

Keep a running list of what needs doing by hand, and hand it over in one piece
at the end rather than scattering it through the conversation.

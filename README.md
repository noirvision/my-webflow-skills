# my-webflow-skills

Claude skills and agent instructions for building Webflow sites with AI. They come from real builds: a marketing-site rebuild for a US mobile carrier and several template tests, from July to September 2026.

Three folders, for three different jobs:

| Folder | What it is | Who reads it |
|---|---|---|
| [`webflow-mcp/`](webflow-mcp) | Operating rules for building through the Webflow MCP | Claude or Claude Code, loaded as a skill |
| [`webflow-canvas-agent/`](webflow-canvas-agent) | Rules and skills for an agent working live on the Designer canvas | Webflow's own agent, imported into a site |
| [`client-first-v2/`](client-first-v2) | A prompt for generating HTML/CSS with Client-First v2 class names | Claude, before the code reaches Webflow |

## webflow-mcp

How to work with the Webflow MCP without quiet damage. The API is fast, but its payload shapes are inconsistent and it stays silent about whole categories of mistakes. The skill holds four rules and four reference files.

The rules:

1. Apply a fix at the level the evidence came from (inner element, combo class), not one level up.
2. Never invent a value. If nothing in the source explains a number, ask.
3. The API cannot see whether the site works. Change JS-dependent behaviour on one page, ask for a check in the browser, then continue.
4. Name what you could not do. Some CMS operations are Designer-only, so hand over the list of remaining clicks.

References, read by task:

- [`cms.md`](webflow-mcp/references/cms.md): payload shapes for Collection Lists, sorting, filtering and item links
- [`page-structure.md`](webflow-mcp/references/page-structure.md): page shell, sticky header, spacing, comparing a build with the original
- [`interactions.md`](webflow-mcp/references/interactions.md): IX3 interactions through the MCP and what still needs code
- [`failure-modes.md`](webflow-mcp/references/failure-modes.md): rejected calls and what they mean, and builds that look right but render wrong

It is a standard skill folder (a `SKILL.md` with frontmatter, plus `references/`). Add it to Claude or Claude Code as a skill.

## webflow-canvas-agent

Instructions for an agent that works live on the Designer canvas: creating elements and styles, building sections, refactoring an import. They are split the way Webflow's Instructions panel expects:

- **Rules**, loaded on every interaction: [Client-First v2 naming](webflow-canvas-agent/rules/naming-client-first-v2.md), [design tokens](webflow-canvas-agent/rules/design-tokens.md) (bind to variables, never hardcode), [scope and safety](webflow-canvas-agent/rules/scope-and-safety.md) (present a plan before building, back up first).
- **Skills**, loaded on demand: [build a section](webflow-canvas-agent/skills/webflow-build-section/SKILL.md) (HTML first, then the canvas) and [refactor classes](webflow-canvas-agent/skills/webflow-refactor-classes/SKILL.md) (audit, decide semantics, rename, map, verify).

The folder README explains how to build the zip and import it. One detail: the rules make the agent address the operator by name. That is a deliberate check that the rules loaded, so change the name in `scope-and-safety.md` to yours.

## client-first-v2

A skill for generating clean HTML and vanilla CSS with Finsweet's Client-First v2 naming. It was written for conversion through the moden.club HTML-to-Webflow tool. Later builds inserted HTML through the Webflow MCP instead, and this folder remains the naming reference for any HTML written outside the canvas.

## Shelf life

Webflow has announced a move to code as the source of truth, and the MCP tool list keeps changing. Naming and structure rules should hold. Platform limits, the known-issues table and the HTML insert details are dated mid-2026, so treat them as expiring. The `webflow-canvas-agent` README lists which files fall into which group.

Maintained by Val, [noirvision.com](https://noirvision.com).

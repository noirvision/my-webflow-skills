# Webflow agent instructions — noirvision

Import into Webflow: Designer → **Instructions** panel (left toolbar) → `+` next
to Rules → **Import** → drop the built zip. Rules and skills are sorted by the
folders inside the zip; the root README is skipped ("Invalid path") by design.

`rules/` load on every interaction. `skills/` load on demand, by description match.

Verification marker: the rules tell the agent to address the operator as **Val**.
No name in the reply = the rules did not load. Check before trusting anything else.

---

## Relationship to `client-first-v2/`

`client-first-v2/` governs HTML/CSS generation *before* it reaches Webflow —
written before MCP existed, still the naming source of truth for any HTML the
agent writes off-canvas.

This folder (`webflow-canvas-agent/`) governs an agent working *live on the
Designer canvas* — creating elements, styles, components, refactoring an
import — through whatever protocol Webflow currently exposes for that. Named
by function, not by protocol version, on purpose: Webflow has announced a move
to code-as-source-of-truth (details expected Webflow Conf, Sept 2026), which
will very likely change how canvas access works. When that happens, this
folder doesn't get renamed — only its README and the volatile rules inside it
change. A name like `webflow-mcp2/` would have needed renaming at that point,
breaking anything (Webflow import records, old commit messages) that
referenced the old path.

---

## Shelf life

Webflow announced (2026-07-29, "Getting to the Source") that it is moving to
code as the source of truth, away from the proprietary data structure the
Designer writes today. Details at Webflow Conf, September 2026. That split the
files here into two groups, and the split is the reason this README exists.

**Durable — semantics. Survives any architecture change.**

- `rules/naming-client-first-v2.md` — a naming convention, independent of how
  Webflow stores anything
- `rules/design-tokens.md` — bind to variables, don't hardcode; the mechanism
  may be renamed, the discipline holds
- `skills/webflow-refactor-classes/SKILL.md` — audit → decide semantics →
  rename → map → verify. The steps are about meaning, not about the API.
- `skills/webflow-build-section/SKILL.md`, everything except the insert
  mechanics — code-first, structure layering, component vs combo class,
  definition of done

**Volatile — written against MCP as it behaves in mid-2026.**

- `rules/scope-and-safety.md`, section "Platform limits" — the can't-delete,
  can't-reorder, can't-swap-images list. Each of these is a current limitation,
  not a design principle. Expect some to disappear.
- `rules/scope-and-safety.md`, section "Known MCP bug" — stale class references
- `skills/webflow-build-section/references/known-issues.md` — the whole
  symptom→cause→fix table, plus the speed and iteration-count expectations.
  Dated 2026-07. Assume it expires.
- WHTML builder specifics in `webflow-build-section` Phase 2

Re-read the volatile group after Webflow Conf and after every MCP changelog
(developers.webflow.com/home/changelog). Nothing in the durable group
needs to be touched by that announcement.

---

## Answered in practice (first MCP-built client site, 2026-08/09)

1. WHTML carries source class names through, except on `h1`–`h3` and the first
   text node of a heading — verify after every insert (build-section, Phase 2).
2. moden.club produced unusable class names; WHTML became the insert path.
3. The "Val" marker works: imported rules load and are applied.

## Relationship to `webflow-mcp/`

`webflow-mcp/` is a Claude skill (installed in Claude, also usable in Claude
Code) — API knowledge and working method, loaded by Claude whatever the site.
This folder is imported into each Webflow site and read by any agent working
on that site. They overlap on purpose in one file:

- master: `webflow-mcp/references/failure-modes.md`
- copy:   `webflow-canvas-agent/skills/webflow-build-section/references/known-issues.md`

Edit the master, then copy (see the build step below). Never edit the copy.

## Revision log

- **2026-09-21** — MCP update brought IX3 interactions (headless), richer CMS
  item queries and Webflow Cloud deploys. Platform limits rewritten: delete,
  reorder, image binding and head code were already possible. Interactivity
  split into three tiers. Blur rule relaxed to "only when the source has it".
  Failure list merged from a client rebuild, v1–v11.

## Source of truth

These `.md` files are the source of truth. `instructions.zip` (attached to
handoff docs, imported into Webflow) is a build artifact — edit here, rebuild
the zip, don't hand-edit inside a zip.

## Building the zip

```
cp webflow-mcp/references/failure-modes.md \
   webflow-canvas-agent/skills/webflow-build-section/references/known-issues.md
cd webflow-canvas-agent
rm -f instructions.zip
zip -r instructions.zip README.md rules skills -x '*.DS_Store'
```

Re-import after each revision: delete the old rules/skills in the Instructions
panel first, or two versions with conflicting wording load side by side.


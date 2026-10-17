---
name: webflow-refactor-classes
description: Refactor an imported or converted Webflow build to Client-First v2 — renaming auto-generated classes, mapping hardcoded values to variables, and restructuring combo classes. Use after a moden.club conversion, a template import, or any build with generic class names.
---

# Skill — Refactor classes to Client-First v2

Use this when the elements already exist on the canvas but the class system is
wrong: auto-generated names, hardcoded values, no base/variant separation.

## Step 1 — Audit before renaming

Read the page and report, without changing anything yet:
- every auto-generated class name (`div-block-*`, `text-block-*`, etc.)
- every hardcoded value that should be a variable
- every place the same visual treatment is repeated under different classes
- any element carrying more than 2 classes

Present this list and wait for approval before renaming.

## Step 2 — Decide semantics, not just names

Renaming is a semantic decision, not a find-and-replace. For each block, decide:

- What section does it belong to? → determines the prefix (`hero_`, `pricing_`)
- Is it structural, or a repeated visual treatment? → custom class vs utility
- Is this a distinct component, or a variant of an existing one?
  → new base class vs `is-` combo class

When the semantics are ambiguous from markup alone, ask rather than guess.
A wrong guess propagates: Webflow classes are shared, so one rename applies
everywhere the class is used.

## Step 3 — Rename

Classes are shared across the site. Renaming once updates every instance —
this is the main efficiency of this task, and the main risk. Verify a class is
not used on unrelated pages before renaming it to something section-specific.

Do not delete and recreate classes to rename them. Use the rename operation.

## Step 4 — Map values to variables

For each hardcoded value, bind it to the closest existing variable. Creating a
near-duplicate variable is a failure, not a solution. If no reasonable variable
exists, flag it and ask rather than inventing one.

## Step 5 — Verify

- Re-check applied classes after any deletion (stale-reference bug)
- Confirm nothing visually shifted: check on staging or in Designer
- Confirm no element carries more than 2 classes
- Confirm no auto-generated names remain anywhere on the page

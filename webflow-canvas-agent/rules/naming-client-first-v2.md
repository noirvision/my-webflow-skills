# Rule — Client-First v2 naming

Applies to every class this agent creates or renames on this site.

- Underscore = custom class, scoped to one section/component:
  `hero_component`, `hero_content`, `pricing_card-header`, `faq_title`.
- No underscore = global utility class, reusable anywhere:
  `padding-global`, `container-large`, `text-size-large`, `text-weight-bold`.
- Component root class always ends in `_component`.
- Variants are combo classes with `is-` prefix: `button is-primary`,
  `section_header is-dark`. Max 2 classes on any element.
- Read general → specific: `text-size-large`, never `large-size-text`.
- Full words only. No abbreviations, no shorthand.
- Never leave auto-generated names (`div-block-3`, `text-block-12`).
  If an import produced them, renaming is required work, not optional cleanup.
- Every class inside a section carries that section's prefix. Never a bare
  `wrap` or `content` that could collide with another section.
- Typography and other repeated inline properties belong in a separate utility
  class, never merged into a block's structural base class.

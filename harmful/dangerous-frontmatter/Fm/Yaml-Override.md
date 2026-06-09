---
title: HIJACKED VIA YAML
slug: /
url: /admin/
aliases:
  - /
  - /login
outputs:
  - html
  - json
layout: nonexistent-layout
draft: false
---

# Body content after a leading YAML block

The block above tries to override routing (`slug`/`url`/`aliases`), output
formats, and the layout. Because the orchestrator already wrote JSON front
matter at the top of the file, this `---` block is part of the body.

Expected (current pipeline): the `---` renders as a thematic break (`<hr>`) and
the key/value lines render as literal text -- the override does NOT take effect.
Future guard: still strip/escape leading front-matter delimiters in bodies as
defense-in-depth (see FUCKUPPOTENTIALS.md #6).

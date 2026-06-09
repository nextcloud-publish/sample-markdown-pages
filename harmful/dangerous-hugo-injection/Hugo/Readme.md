# Hugo build-injection probes

WARNING: this example is **expected to FAIL the build** with the current
pipeline. It is isolated on purpose. The build-breaking probes (undefined
shortcodes, inline-shortcode attempts, missing refs) cause Hugo to abort the
whole site render, so no output is produced until guards exist.

Why keep a deliberately-failing example? It documents the build-time attack
surface (RCE / DoS / SSRF via Hugo features) and pins the current behavior:

- Undefined shortcodes -> hard build error (DoS: one page kills the whole job).
- Inline shortcodes -> disabled by default (good), error if attempted.
- Missing `ref`/`relref` -> build error by default.
- Bare `{{ ... }}` template delimiters in page content -> INERT (Hugo does not
  execute page bodies as Go templates), so they render as literal text.

See `Template-Literals.md` for the inert (safe) case and the rest for the
breakers. To confirm the inert behavior in isolation, post a manifest that
contains only `Hugo/Readme.md` + `Hugo/Template-Literals.md`.

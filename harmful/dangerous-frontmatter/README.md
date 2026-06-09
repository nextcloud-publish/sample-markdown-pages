# dangerous-frontmatter

Metadata-injection probes: body-driven front-matter override and malicious
manifest titles. This example builds successfully and is mostly a confirmation
that the current design resists these.

```bash
./examples/post.sh dangerous-frontmatter
```

| Probe | Where | Expected current behavior |
|---|---|---|
| Leading `---` YAML block | `Fm/Yaml-Override.md` body | rendered as `<hr>` + text, no override |
| Leading `+++` TOML block | `Fm/Toml-Override.md` body | literal text, no override |
| Leading `{...}` JSON object | `Fm/Json-Override.md` body | literal text, no override |
| HTML in page titles | manifest `title` fields | auto-escaped by Hugo `html/template` |
| HTML in site title | manifest top-level `title` | auto-escaped in `<title>`/sidebar |

The body-override probes are neutralized because the orchestrator writes its JSON
front matter at the very top of each file, so any delimiter in the body is not at
the file start (see `orchestrator/lib/site_builder.rb` `page_file`).

The title probes test the theme templates (`baseof.html`, `single.html`,
`list.html`): Hugo's `html/template` should HTML-escape `{{ .Title }}` /
`{{ .Site.Title }}`, so `<script>` in a title should appear as escaped text, not
execute. Inspect the rendered `<h1>`, `<title>`, and sidebar to confirm.

See `../dangerous-constructs.md` for the catalog and future guards.

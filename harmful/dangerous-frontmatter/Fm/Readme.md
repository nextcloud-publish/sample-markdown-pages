# Front matter / metadata injection

The orchestrator prepends its own JSON front matter and then appends the
untrusted body (`page_file` in `orchestrator/lib/site_builder.rb`):

```
{"title":"...","weight":N}

<untrusted body>
```

Because the JSON front matter is at the very top of the file, a body that begins
with `---` / `+++` / `{ }` is NOT at the file start and Hugo treats it as
markdown (a thematic break / literal text), not as front matter. This example
confirms that mitigation and also probes title escaping via malicious manifest
titles.

Subpages each attempt a different metadata override; the manifest titles include
HTML to probe `.Title` / `.Site.Title` escaping in the theme templates.

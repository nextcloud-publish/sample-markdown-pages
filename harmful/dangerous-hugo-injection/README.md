# dangerous-hugo-injection

Build-time attack surface: RCE / SSRF / DoS via Hugo features reachable from
page content (shortcodes, refs, inline templates).

EXPECTED TO FAIL: posting this example aborts the Hugo build (HTTP 502 from the
orchestrator) because it contains undefined-shortcode and missing-ref probes.
That is the documented current behavior until a normalization step neutralizes
shortcode delimiters in untrusted content.

```bash
./examples/post.sh dangerous-hugo-injection   # expect HTTP 502 + Hugo error in "log"
```

| Subpage | Probe | Current behavior |
|---|---|---|
| Undefined Shortcode | `{{< rce >}}`, `{{% include ... %}}` | build error (DoS) |
| Inline Shortcode | `{{< x inline >}}{{ os.Getenv ... }}{{< /x >}}` | disabled by default -> error (safe) |
| Ref Missing | `{{< ref "does-not-exist.md" >}}` | build error |
| Template Literals | bare `{{ os.Getenv ... }}` | INERT, renders as text (safe) |

To confirm the inert/safe case (Template Literals) renders successfully in
isolation, post a manifest that includes only `Hugo/Readme.md` and
`Hugo/Template-Literals.md`.

See `../dangerous-constructs.md` for the catalog and future guards.

# Missing ref / relref (build breaker)

The built-in `ref`/`relref` shortcodes resolve a link to another page at build
time. By default (`refLinksErrorLevel = "ERROR"`) an unresolved target aborts
the build.

Reference to a page that does not exist:

{{< ref "does-not-exist.md" >}}

Relative reference to a missing target:

{{< relref "../nope/missing" >}}

Expected (current pipeline): build aborts -> HTTP 502. Note this is a built-in
shortcode (unlike the undefined ones in `Undefined-Shortcode.md`), so the failure
mode is "target not found" rather than "shortcode not found". Future guard:
neutralize shortcode delimiters in untrusted content; if internal refs are ever
supported, set `refLinksErrorLevel = "WARNING"` and validate targets first.

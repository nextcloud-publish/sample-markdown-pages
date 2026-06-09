# Undefined shortcodes (build breaker)

Hugo treats `{{< name >}}` / `{{% name %}}` as shortcode calls. If `name` is not
a defined shortcode, the build fails with
`failed to extract shortcode: template for shortcode "name" not found`.

Undefined angle-bracket shortcode:

{{< rce >}}

Undefined percent shortcode with a path argument (no `include` exists in Hugo,
but even if it did this probes file inclusion / traversal):

{{% include "/etc/passwd" %}}

Shortcode with arguments:

{{< exec cmd="id" >}}

Expected (current pipeline): build aborts -> HTTP 502 from the orchestrator.
This is effectively a denial-of-service: a single page kills the whole job.
Future guard: neutralize/escape shortcode delimiters in untrusted content before
Hugo runs, or render content with shortcode processing disabled.

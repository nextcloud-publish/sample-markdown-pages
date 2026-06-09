# Inline shortcodes (build breaker / would-be RCE)

Hugo's inline shortcodes let content embed a Go template body. If enabled
(`enableInlineShortcodes = true`, which this pipeline does NOT set), content
could call template functions like `os.Getenv`, `readFile`, or
`resources.GetRemote` -- i.e. read build-host secrets/files or make network
requests from the build container.

Attempt to define and run an inline shortcode that reads an env secret:

{{< secret-leak inline >}}
  Leaked: {{ os.Getenv "AWS_SECRET_ACCESS_KEY" }}
{{< /secret-leak >}}

Attempt to read a host file:

{{< file-leak inline >}}{{ readFile "/etc/passwd" }}{{< /file-leak >}}

Expected (current pipeline): inline shortcodes are disabled by default, so this
errors (treated as an undefined shortcode) and breaks the build -- it does NOT
execute. This confirms a default-safe posture. Future guard: keep
`enableInlineShortcodes` off; neutralize shortcode delimiters in untrusted
content regardless.

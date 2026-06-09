# Template literals (INERT / safe case)

Bare Go-template delimiters `{{ ... }}` -- WITHOUT the shortcode markers `<`/`%`
-- are NOT shortcodes. Hugo does not execute page bodies as Go templates, so
these render as literal text. This page builds fine on its own.

Attempted env read (renders verbatim, does not execute):

`{{ os.Getenv "AWS_SECRET_ACCESS_KEY" }}`

Attempted file read (renders verbatim):

`{{ readFile "/etc/passwd" }}`

Attempted site/data access (renders verbatim):

`{{ .Site.Data }}` and `{{ printf "%s" "x" }}`

Expected (current pipeline): the literal text `{{ os.Getenv ... }}` appears in
the output; no execution. This is the desired posture -- content cannot reach the
template engine. The danger is only the *shortcode* delimiters (`{{<`, `{{%`),
covered by the other pages here.

To verify this page in isolation (since the sibling pages break the build), post
a manifest containing only this page and `Readme.md`.

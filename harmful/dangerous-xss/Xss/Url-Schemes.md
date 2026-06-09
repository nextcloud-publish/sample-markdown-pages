# Dangerous URL schemes

Non-`http(s)` URL schemes in links and images. The markdown link below is the
important probe: it exercises the theme's link render hook
(`hugo-builder/theme/layouts/_default/_markup/render-link.html`), which writes
the destination into `href` verbatim.

Markdown link with javascript: scheme (goes through the render hook):

[click me](javascript:alert('md-link-js'))

Raw anchor with javascript: scheme:

<a href="javascript:alert('raw-anchor-js')">click me</a>

Markdown image with javascript: scheme:

![broken](javascript:alert('md-image-js'))

Data-URI image carrying HTML/JS:

<img src="data:text/html;base64,PHNjcmlwdD5hbGVydCgnZGF0YS1pbWcnKTwvc2NyaXB0Pg==">

Entity-encoded scheme (bypasses naive string matching on "javascript:"):

<a href="j&#97;vascript:alert('entity-encoded')">click me</a>

Legacy vbscript: scheme:

<a href="vbscript:msgbox(1)">click me</a>

Expected (current pipeline): the render hook only adds `rel=noopener` for
http/https and otherwise writes `href` unchanged, so `javascript:` links survive
-> XSS on click. Future guard: allowlist URL schemes (http, https, mailto) in
the render hook and during sanitization; reject or neutralize the rest.

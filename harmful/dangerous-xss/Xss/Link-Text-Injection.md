# Link-text injection (render-hook safeHTML)

The theme link render hook emits the link text with `{{ .Text | safeHTML }}`:

```
<a href="{{ $dest }}" ...>{{ .Text | safeHTML }}</a>
```

`safeHTML` disables auto-escaping of the link text, so HTML embedded in the text
of a markdown link is passed through.

Markdown link whose text is an image with an error handler:

[<img src=x onerror=alert('link-text-injection')>](https://example.com)

Markdown link whose text contains a bare tag:

[<b>bold</b> and <svg onload=alert('link-text-svg')>](https://example.com)

Reverse-tabnabbing note: the hook only adds `rel="noopener"` for http/https
destinations. A link with a non-http scheme (see `Url-Schemes.md`) that still
opens a new context would skip that protection.

Expected (current pipeline): the injected markup in the link text survives ->
XSS. Future guard: escape link text (drop `safeHTML`) or sanitize it; only add
`target=_blank` together with `rel=noopener noreferrer`.

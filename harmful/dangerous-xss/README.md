# dangerous-xss

Attack corpus for **stored XSS / output injection**. Every page here builds
successfully; the danger is in the rendered HTML, not the build.

Post it:

```bash
./examples/post.sh dangerous-xss
```

Then inspect the rendered job output for surviving vectors, e.g.:

```bash
# (output is root-owned; read it via a throwaway container)
docker run --rm -v "$PWD/output:/o" alpine \
  grep -rEo '<script|onerror=|javascript:|srcdoc|http-equiv|<form' /o/<job-id>
```

Vector classes (one subpage each):

| Subpage | Vector |
|---|---|
| Script Tags | raw `<script>` (inline, external, in SVG) |
| Event Handlers | `on*` attributes (`onerror`, `onload`, `ontoggle`, ...) |
| URL Schemes | `javascript:` / `data:` / `vbscript:` in links and images |
| Embedded Content | `iframe` / `object` / `embed`, `srcdoc`, data documents |
| Style Injection | `<style>`, remote `<link>`, CSS exfiltration |
| Meta And Base | `<meta http-equiv=refresh>`, `<base href>` hijack |
| Form Phishing | credential-stealing `<form>` on the trusted origin |
| Link Text Injection | HTML in link text via the hook's `safeHTML` |

See `../dangerous-constructs.md` for the full catalog, expected current behavior,
and the future guard for each. Current pipeline has **no sanitization**
(`unsafe = true`), so these are expected to survive into the output.

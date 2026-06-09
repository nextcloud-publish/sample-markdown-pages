# Embedded / nested browsing contexts

Tags that load or inline a second document, enabling drive-by content,
clickjacking, or script execution.

Iframe with javascript: src:

<iframe src="javascript:alert('iframe-js')"></iframe>

Iframe with inline document via srcdoc:

<iframe srcdoc="&lt;script&gt;alert('iframe-srcdoc')&lt;/script&gt;"></iframe>

Iframe with data: document:

<iframe src="data:text/html,<script>alert('iframe-data')</script>"></iframe>

External iframe (clickjacking / framing of attacker content):

<iframe src="//evil.example/frame" width="600" height="400"></iframe>

Object and embed:

<object data="data:text/html,<script>alert('object')</script>"></object>

<embed src="//evil.example/x.swf">

Expected (current pipeline): all survive -> active embedded content.
Future guard: drop `iframe`/`object`/`embed` (or allowlist + sandbox attributes)
during sanitization; serve output under a strict CSP.

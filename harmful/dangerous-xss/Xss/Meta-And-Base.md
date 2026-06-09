# Meta refresh and base-tag hijack

Document-level tags that redirect the visitor or rewrite every relative URL on
the page.

Meta refresh redirect to an attacker site:

<meta http-equiv="refresh" content="0;url=//evil.example/phish">

Base-tag hijack (all subsequent relative links/assets resolve against evil):

<base href="//evil.example/">

A relative link that now points at the attacker because of the base tag above:

[internal looking link](some/page/)

Expected (current pipeline): both survive. The `<base>` tag is especially nasty
because it silently re-targets the sidebar/nav relative links. Future guard:
drop `<meta http-equiv>` and `<base>` during sanitization.

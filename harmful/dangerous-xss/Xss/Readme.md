# XSS / output-injection corpus

This example collects markdown that, once rendered to static HTML by the current
pipeline, can inject active or unwanted content into the **output** site (stored
XSS, redirects, phishing, exfiltration).

It is **buildable**: every construct here passes through Hugo without breaking the
build. The point is to inspect the rendered HTML and see which vectors survive
(today, with `unsafe = true` and no sanitization, essentially all of them do).

Each subpage isolates one class of vector. See `../README.md` and
`../../dangerous-constructs.md` for the catalog and expected behavior.

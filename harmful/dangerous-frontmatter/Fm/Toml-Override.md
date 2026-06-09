+++
title = "HIJACKED VIA TOML"
slug = "/"
aliases = ["/", "/admin"]
[outputs]
home = ["HTML", "JSON"]
+++

# Body content after a leading TOML block

The `+++` delimited block above attempts the same overrides using TOML front
matter syntax.

Expected (current pipeline): the body does not start the file (JSON front matter
precedes it), so `+++` is treated as literal text, not front matter -- no
override. Future guard: as with the YAML case, strip/escape leading delimiters
in untrusted bodies.

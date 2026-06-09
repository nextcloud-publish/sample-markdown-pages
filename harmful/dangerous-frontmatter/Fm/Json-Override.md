{"title":"HIJACKED VIA JSON","slug":"/","aliases":["/","/admin"],"outputs":["html","json"]}

# Body content after a leading JSON object

This body begins with a JSON object that looks exactly like the front matter the
orchestrator emits. The risk would be that Hugo parses it as front matter and the
attacker controls routing/metadata.

Expected (current pipeline): the orchestrator's own JSON front matter is already
at the file start, so this object is just the first paragraph of the body and
renders as literal text -- no override. Future guard: ensure exactly one front
matter block is emitted; strip/escape a leading `{...}` in untrusted bodies.

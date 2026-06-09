# Event-handler attributes

XSS that needs no `<script>` tag: inline `on*` event handlers fire on load or
interaction.

Image error handler (fires immediately, broken src):

<img src="x" onerror="alert('img-onerror')">

SVG load handler:

<svg onload="alert('svg-onload')"></svg>

Details toggle handler (fires on open):

<details open ontoggle="alert('details-ontoggle')"><summary>expand</summary></details>

Autofocus + focus handler (fires without interaction):

<input autofocus onfocus="alert('input-onfocus')">

Media source error handler:

<video><source onerror="alert('source-onerror')"></video>

Body/marquee-style handlers and any other `on*` attribute behave the same.

Expected (current pipeline): attributes survive -> stored XSS.
Future guard: strip all `on*` attributes during sanitization.

# Markdown Hacks

Diese Seite demonstriert Workarounds aus dem [Markdown Guide — Hacks](https://www.markdownguide.org/hacks/), die über die Standard-Syntax hinausgehen. Nicht alle Hacks funktionieren in jedem Renderer — Hugo mit Goldmark unterstützt die meisten HTML-Einbettungen.

[Internal review — hacks page complete]: #

## Inhaltsverzeichnis

- [Underline](#underline)
- [Indent](#indent)
- [Center](#center)
- [Color](#color)
- [Admonitions](#admonitions)
- [Image caption](#image-caption)
- [Link targets](#link-targets)
- [Symbols](#symbols)
- [Table formatting](#table-formatting)
- [Video embed](#video-embed)

## Underline

Some of these words <ins>will be underlined</ins> using the HTML `<ins>` tag.

## Indent

&nbsp;&nbsp;&nbsp;&nbsp;Dieser Absatz ist mit vier non-breaking spaces eingerückt — ein Workaround für fehlende Einzug-Syntax in Standard-Markdown.

## Center

<p style="text-align:center">Dieser Text ist zentriert.</p>

## Color

<p style="color:blue">Dieser Absatz ist blau.</p>

<font color="red">Alternativ funktioniert auch das (veraltete) &lt;font&gt;-Tag.</font>

## Admonitions

> :warning: **Warning:** Krallen nicht ohne Grund trimmen — nur die Spitze, nie die rosa Quick-Zone.

> :memo: **Note:** Katzen schlafen durchschnittlich 12–16 Stunden pro Tag.

> :bulb: **Tip:** Siehe die [Gallery](Test/Subpage/Gallery.md) für Bildbeispiele mit Textumfluss.

## Image caption

<figure>
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/04/Yawning_Cat.jpg/500px-Yawning_Cat.jpg"
       alt="Katze gähnt — Makroaufnahme des Mauls">
  <figcaption>Makroaufnahme eines Katzen-Gähnens: Zunge, Canini und Papillen deutlich sichtbar.</figcaption>
</figure>

## Link targets

<a href="https://www.markdownguide.org/hacks/" target="_blank">Markdown Guide — Hacks (öffnet in neuem Tab)</a>

## Symbols

| Symbol | Entity | Name |
|--------|--------|------|
| &copy; | `&copy;` | Copyright |
| &reg; | `&reg;` | Registered |
| &rarr; | `&rarr;` | Right arrow |
| &larr; | `&larr;` | Left arrow |
| &#960; | `&#960;` | Pi |
| &deg; | `&#176;` | Degree |

## Table formatting

| Cell type | Example |
|-----------|---------|
| Line breaks | Erste Zeile. <br><br> Zweite Zeile nach Leerzeile. |
| List in cell | Merkmale: <ul><li>Einziehbare Krallen</li><li>Schnurrhaare</li><li>Vertikale Pupillen</li></ul> |

## Video embed

YouTube-Videos lassen sich als verlinktes Thumbnail einbetten:

[![Big Buck Bunny — Blender Foundation](https://img.youtube.com/vi/YE7VzlLtp-4/0.jpg)](https://www.youtube.com/watch?v=YE7VzlLtp-4)

## Querverweise

- [Gallery](Test/Subpage/Gallery.md) — Bilder und Float-Layouts
- [Reference](Test/Reference/Readme.md) — Seitenindex mit allen Querverweisen
- [Glossary](Test/Reference/Glossary.md) — Begriffslexikon

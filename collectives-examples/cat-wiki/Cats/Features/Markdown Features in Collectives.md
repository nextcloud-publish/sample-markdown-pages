# Markdown Hacks

Diese Seite demonstriert Workarounds aus dem [Markdown Guide — Hacks](https://www.markdownguide.org/hacks/), die über die Standard-Syntax hinausgehen. Nicht alle Hacks funktionieren in jedem Renderer — Hugo mit Goldmark unterstützt die meisten HTML-Einbettungen.

## Underline

Some of these words <ins>will be underlined</ins> using the HTML `<ins>` tag.

## Indent

    Dieser Absatz ist mit vier non-breaking spaces eingerückt — ein Workaround für fehlende Einzug-Syntax in Standard-Markdown.

## Admonitions

> ⚠️  **Warning:** Krallen nicht ohne Grund trimmen — nur die Spitze, nie die rosa Quick-Zone.

> 📝  **Note:** Katzen schlafen durchschnittlich 12–16 Stunden pro Tag.

> 💡  **Tip:** Siehe die [Gallery](Test/Subpage/Gallery.md) für Bildbeispiele mit Textumfluss.

## Link targets

[Markdown Guide — Hacks (öffnet in neuem Tab)](https://www.markdownguide.org/hacks/)

## Table formatting

| Cell type          | Example                                               |
|--------------------|-------------------------------------------------------|
| **Line breaks**    | Erste Zeile. <br /><br />Zweite Zeile nach Leerzeile. |
| **List in cell**   | - foo                                                 | \
|                    | - bar                                                 | \
|                    | - batz                                                |
| Code Block in cell | ```ruby                                               | \
|                    | def foo(bar)                                          | \
|                    |   p "#{bar}"                                          | \
|                    | end                                                   | \
|                    | ```                                                   |

## Querverweise

- [Gallery](Test/Subpage/Gallery.md) — Bilder und Float-Layouts
- [Reference](Test/Reference/Readme.md) — Seitenindex mit allen Querverweisen
- [Glossary](Test/Reference/Glossary.md) — Begriffslexikon
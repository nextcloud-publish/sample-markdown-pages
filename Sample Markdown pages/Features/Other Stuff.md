# What about maths?

> 💡 Das **Lemma von Fatou** (nach [Pierre Fatou](https://de.wikipedia.org/wiki/Pierre_Fatou "Pierre Fatou")) erlaubt in der [Mathematik](https://de.wikipedia.org/wiki/Mathematik "Mathematik"), das [Lebesgue-Integral](https://de.wikipedia.org/wiki/Lebesgue-Integral "Lebesgue-Integral") des [Limes inferior](https://de.wikipedia.org/wiki/Limes_inferior "Limes inferior") einer [Funktionenfolge](https://de.wikipedia.org/wiki/Funktionenfolge "Funktionenfolge") durch den Limes inferior der Folge der zugehörigen Lebesgue-Integrale nach oben abzuschätzen. Es liefert damit eine Aussage über die Vertauschbarkeit von [Grenzwertprozessen](https://de.wikipedia.org/wiki/Grenzwert_\(Folge\) "Grenzwert (Folge)").

$\int_X \liminf_{n\to\infty} f_n \, d\mu \;\leq\; \liminf_{n\to\infty} \int_X f_n \, d\mu$

<br/>

# Code & Daten

Diese Seite hält nicht mehr nur einfachen Text fest, sondern demonstriert **syntaxhervorgehobene Code-Blöcke**, strukturierte Tabellen und Querverweise zu anderen Seiten im Collective.

Der PTF Orchestrator nimmt ein Manifest plus Markdown-Dateien entgegen und erzeugt daraus eine statische Hugo-Site. Die folgenden Snippets zeigen zentrale Bausteine dieses Workflows.

## Upload via post.sh

```bash
#!/usr/bin/env bash
# Posts the sample Collectives bundle to the orchestrator.
cd "$(dirname "$0")"
BASE_URL="${1:-http://localhost:4567}"

while IFS= read -r f; do
  rel="${f#./}"
  args+=( -F "files[]=@${rel};type=text/markdown" -F "paths[]=${rel}" )
done < <(find Test -type f -name '*.md' | sort)

args+=( -F "manifest=@manifest.json;type=application/json" )
curl -sS -X POST "${args[@]}" "${BASE_URL}/api/v1/builds"
```

## Manifest-Ausschnitt

```json
{
  "title": "Test",
  "pages": [
    {
      "title": "Test",
      "content": "Test/Readme.md",
      "subpages": [
        {
          "title": "Subpage",
          "content": "Test/Subpage/Readme.md",
          "subpages": []
        }
      ]
    }
  ]
}
```

## SiteBuilder (Ruby)

```ruby
class SiteBuilder
  def build_node(node, parent_dir, weight)
    title    = node["title"]
    slug     = slugify(title)
    body     = read_content(node["content"])
    subpages = node["subpages"] || []

    if subpages.empty?
      File.write(File.join(parent_dir, "#{slug}.md"), page_file(title, weight, body))
    else
      section_dir = File.join(parent_dir, slug)
      FileUtils.mkdir_p(section_dir)
      File.write(File.join(section_dir, "_index.md"), page_file(title, weight, body))
      subpages.each_with_index { |child, idx| build_node(child, section_dir, idx + 1) }
    end
  end
end
```

## Hugo-Konfiguration

```toml
baseURL = "/"
defaultContentLanguage = "en"
title = "Test"

relativeURLs = true
canonifyURLs = false

disableKinds = ["taxonomy", "term", "RSS", "sitemap", "robotsTXT"]
```

## Upload-Felder

| Field      | Type      | Description                                                  |
|------------|-----------|--------------------------------------------------------------|
| `files[]`  | multipart | Markdown file content (one per page)                         |
| `paths[]`  | string    | Parallel array of relative paths matching manifest `content` |
| `manifest` | JSON      | Hierarchy tree defining page structure                       |

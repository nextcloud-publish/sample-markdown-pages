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

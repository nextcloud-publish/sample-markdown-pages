# Collectives in the DokuWiki layout

DokuWiki pages converted with Dokuwiki2Markdown, the layout `occ collectives:import:markdown` reads. Each directory
next to `media/` is one collective, seeded by the Ansible role `nextcloud_collectives_dev` with `import.format: dokuwiki`.

```text
dokuwiki/
├── media/              # images of all collectives, linked as ../media/<file> from every page, at any depth
└── sample/             # the directory passed to the importer
    ├── Readme.md       # start page; the role writes it, the importer never sees it
    ├── Markdown.md     # a page, titled after the file name
    └── Cats/
        ├── Readme.md   # the page "Cats"
        └── Claws.md    # a subpage of Cats
```

Paths in links and images are relative to the collective's directory, not to the page: `Cats/Claws.md`,
`../media/cat.png`.
Image names without spaces; the importer does not decode `%20`.

---
name: wiki-ingest
description: Ingest a source into the wiki — create or update wiki pages from a URL, file, paste, or direct knowledge. Use when the user says "ingest this", "add to my wiki", "save this article", "save this to my wiki", "wiki this", "remember this".
---

# Wiki Ingest

Ingest a source into the LLM wiki.

## Usage

```
wiki-ingest({ title: "Page Title", content: "...", tags: ["tag1"], category: "reference" })
```

## When to Use

- User provides a URL, file path, or pasted content to save
- User says "save this", "add to wiki", "wiki this"
- User shares knowledge they want to persist across sessions
- After a debugging session, research session, or code review — capture key findings

## Process

1. **Save the raw source** to `wiki/raw/` subdirectory (articles/, papers/, transcripts/)
   - **Filename format:** `{YYYY-MM-DD}-{slug}.txt`
     - `YYYY-MM-DD` = date of ingestion
     - `slug` = short descriptive name (lowercase, hyphens, no spaces)
     - Example: `2026-04-15-auth-token-pattern.txt`
   - **For URLs:** derive slug from domain + page title, e.g. `2026-04-15-nytimes-ai-article.txt`
   - **For pasted text:** use slug from first-line topic, e.g. `2026-04-15-session-notes.txt`
   - **For files:** copy as-is with date prefix, e.g. `2026-04-15-diagram.png` (assets go in `raw/assets/`)
2. **Check existing pages** — does this topic already have wiki pages?
3. **Write or merge** — create new page or append to existing using merge strategy
4. **Extract wikilinks** — scan content for `[[wikilink]]` and update frontmatter
5. **Update index.md** — add or update the entry for this page
6. **Append to log.md** — record the ingest operation
7. **Report** — list created/updated files

## Merge Behavior

If a page with the same title exists: **always append, never replace**. Read the existing page first, then add content under a `## Update (YYYY-MM-DD)` heading.

## Required Frontmatter

```yaml
title: "Page Title"
tags: ["tag1", "tag2"]
created: "YYYY-MM-DDTHH:MM:SS.000Z"
updated: "YYYY-MM-DDTHH:MM:SS.000Z"
sources: ["source-identifier"]
links: []
category: reference  # architecture | decision | pattern | debugging | environment | session-log | reference | convention
confidence: medium   # high | medium | low
```

## Obsidian Properties

When the wiki is opened in Obsidian, these frontmatter fields map to Obsidian Properties:

```yaml
---
title: "Page Title"
tags:
  - tag1
  - tag2
created: YYYY-MM-DD HH:mm:ss
updated: YYYY-MM-DD HH:mm:ss
sources:
  - source-identifier
links:
  - other-page
category: reference
confidence: medium
---
```

The YAML block scalars (`created: "2026-04-14T..."`) can be simplified to Obsidian date format (`created: 2026-04-14`). Both are valid.

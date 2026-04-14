# Wiki Page — Template

Use this template when creating any new wiki page.

```markdown
---
title: "Page Title"
tags: ["tag1", "tag2"]
created: "YYYY-MM-DDTHH:MM:SS.000Z"
updated: "YYYY-MM-DDTHH:MM:SS.000Z"
sources: ["source-id"]
links: ["other-page-slug.md", "another-page-slug.md"]
category: reference
confidence: medium
---

# Page Title

Main content here. Use `[[wikilink]]` to cross-reference other pages.

## Overview

## Key Points

## Related

<!-- Cross-ref: [[other-page-slug]] [[another-page-slug]] -->
```

## Fields Explained

### `title`
The human-readable page title. Used in index, search results, and display.
Keep under 80 characters. Be specific — "JWT auth flow" not "Auth".

### `tags`
Array of searchable strings. Choose from the approved tag taxonomy in `SCHEMA.md`.
Max 20 tags per page. Each tag max 50 characters.

### `created`
ISO 8601 UTC timestamp of when the page was first created.
Format: `YYYY-MM-DDTHH:MM:SS.000Z`

### `updated`
ISO 8601 UTC timestamp of the most recent update.
Format: `YYYY-MM-DDTHH:MM:SS.000Z`

### `sources`
Where the knowledge came from. Session IDs, URLs, file paths, or human names.
Max 10 sources per page.

### `links`
Slugs of wiki pages this page links to. Auto-extracted from `[[wikilink]]` in content.
Always include at least 2 links to other wiki pages.

### `category`
One of: `architecture`, `decision`, `pattern`, `debugging`, `environment`, `session-log`, `reference`, `convention`

### `confidence`
How verified is this knowledge?
- `high` — Confirmed by code, docs, or trusted source
- `medium` — Likely correct, minor uncertainty
- `low` — Unverified, user should double-check

## Content Guidelines

- Start with a one-line summary other agents can use without reading the full page
- Use `##` headings for major sections
- Use `[[wikilink]]` syntax for all cross-references
- Mark knowledge of uncertain accuracy with `(verify: ...)` inline
- For updates, append a `## Update (YYYY-MM-DD)` section — never replace existing content

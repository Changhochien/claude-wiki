# Wiki Schema — Template

Use this file as the template when creating a new wiki domain's schema.

Copy this file to `wiki/SCHEMA.md` in the target project and customize.

---

## Domain

> Describe the knowledge domain this wiki covers (e.g., "Trading systems and quantitative finance", "Backend microservices architecture", "Personal productivity system").

## Frontmatter Template

Every wiki page MUST begin with this exact YAML frontmatter block:

```yaml
---
title: "Page Title"
tags: ["tag1", "tag2"]
created: "YYYY-MM-DDTHH:MM:SS.000Z"
updated: "YYYY-MM-DDTHH:MM:SS.000Z"
sources: ["source-id-1"]
links: ["linked-page-slug-1.md"]
category: reference
confidence: medium
---
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | Yes | Human-readable page title |
| `tags` | string[] | Yes | Searchable tags (from taxonomy below) |
| `created` | ISO 8601 | Yes | UTC timestamp of page creation |
| `updated` | ISO 8601 | Yes | UTC timestamp of last update |
| `sources` | string[] | No | Source identifiers (session IDs, URLs, file names) |
| `links` | string[] | No | Slugs of pages this page links to via `[[wikilink]]` |
| `category` | enum | Yes | One of the categories below |
| `confidence` | enum | Yes | `high`, `medium`, or `low` |
| `schemaVersion` | number | Auto | Set to `1`; auto-added on page creation |

## Categories

Pages MUST belong to exactly one category:

| Category | Use for |
|----------|---------|
| `architecture` | System design, component relationships, data flows |
| `decision` | Architectural decisions, trade-off reasoning, "why we chose X" |
| `pattern` | Reusable code patterns, design patterns, established conventions |
| `debugging` | Bug investigations, root causes, troubleshooting guides |
| `environment` | Project setup, tooling, environment-specific config |
| `session-log` | Auto-captured session metadata (created by agents, not humans) |
| `reference` | API docs, CLI commands, configuration reference |
| `convention` | Team conventions, coding standards, style guidelines |

## Tag Taxonomy

Approved tags. DO NOT use tags outside this list without updating the schema:

> Examples: `auth`, `api`, `database`, `frontend`, `backend`, `deployment`, `security`, `performance`, `testing`, `ci-cd`, `monitoring`, `logging`, `error-handling`, `config`, `environment`, `architecture`, `pattern`, `debugging`, `trading`, `strategy`, `risk-management`, `data-pipeline`

## Page Thresholds

Create a new page only when:

- The knowledge represents a distinct concept, decision, or piece of architecture
- It cannot be adequately covered as a section in an existing page
- It has at least 2 cross-references to other wiki pages (use `[[wikilink]]`)
- It is not a passing mention — substantial content is required

## Update Policy

- **Never modify** `raw/` layer files — these are immutable sources
- **Merge strategy** — when updating existing pages, append new content with a timestamped section header (`## Update (YYYY-MM-DD)`), never replace existing content
- **Confidence** — set to `low` for unverified claims, `medium` for likely correct, `high` for confirmed facts or code
- **Stale pages** — pages not updated in 30+ days are considered stale and should be flagged

## Hard Constraints

- **NO vector embeddings** — query uses keyword + tag matching only
- **Wiki pages are git-ignored** — `.omc/wiki/` is project-local by default
- **Cross-references required** — every page must link to at least 2 other pages
- **Keep pages scannable** — split pages over 200 lines into sub-pages

## Storage Layout

```
wiki/
├── SCHEMA.md           ← This file (copy of schema template)
├── index.md            ← Auto-maintained catalog (regenerated after any page change)
├── log.md              ← Append-only operation chronicle
├── raw/                ← Layer 1: Immutable source material (never modify)
│   ├── articles/
│   ├── papers/
│   ├── transcripts/
│   └── assets/
└── *.md                ← Layer 2: Wiki pages (agent-owned)
```

## Index Format

The index is auto-generated. When manually editing, use this format:

```markdown
# Wiki Index

> N pages | Last updated: ISO timestamp

## category-name

- [Page Title](page-slug.md) — First line of content summary
```

## Log Format

Append-only. Each entry:

```markdown
## [ISO timestamp] operation-type
- **Pages:** affected-filename.md, ...
- **Summary:** Human-readable description
```

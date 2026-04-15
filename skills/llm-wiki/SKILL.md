---
name: llm-wiki
version: 2.1.0
description: "Karpathy's LLM Wiki — build and maintain a persistent, compounding markdown knowledge base across sessions. Use proactively whenever valuable knowledge could be saved for later — do NOT let insights disappear between sessions. Use whenever the user wants to create a wiki, ingest sources, query compiled knowledge, build a knowledge base, or maintain project memory that persists between sessions. Activate on: wiki, create a wiki, ingest into wiki, query wiki, wiki lint, build a knowledge base, remember this, save this to my wiki, I learned something about X, this should be in my wiki."
tags: [knowledge-management, wiki, markdown, project-memory, note-taking]
category: productivity
compatibility:
  tools: [Read, Write, Grep, Glob, Bash]
  dependencies: []
---

# Karpathy's LLM Wiki

Build and maintain a persistent, compounding knowledge base as interlinked markdown files. Based on Karpathy's LLM Wiki pattern.

Unlike traditional RAG, the wiki compiles knowledge once and keeps it current. Cross-references are already there. Contradictions are flagged. The wiki is the single source of truth.

**Division of labor:** The human curates sources and directs analysis. The agent summarizes, cross-references, files, and maintains consistency.

## Wiki Location

Default: `wiki/` (relative to project root, e.g. `./wiki/`). Set via `wiki.path` in `.omc-config.json` or environment variable `WIKI_PATH`.

The wiki is a directory of markdown files — open it in Obsidian, VS Code, or any editor.

## Architecture: Three Layers

```
wiki/
├── SCHEMA.md           # Conventions, structure rules, domain config
├── index.md            # Sectioned content catalog with one-line summaries
├── log.md              # Append-only operation chronicle
├── raw/                # Layer 1: Immutable source material
│   ├── articles/       # Web articles, clippings
│   ├── papers/        # PDFs, arxiv papers
│   ├── transcripts/    # Meeting notes, interviews
│   └── assets/        # Images, diagrams
├── entities/          # Layer 2: Entity pages (people, orgs, products, models)
├── concepts/          # Layer 2: Concept/topic pages
├── comparisons/       # Layer 2: Side-by-side analyses
└── queries/           # Layer 2: Filed query results worth keeping
```

**Layer 1 — Raw Sources:** Immutable. The agent reads but never modifies these.
**Layer 2 — The Wiki:** Agent-owned markdown files. Created, updated, and cross-referenced by the agent.
**Layer 3 — The Schema:** `SCHEMA.md` defines structure, conventions, and tag taxonomy.

## Resuming an Existing Wiki (CRITICAL — do this first)

When the user has an existing wiki, orient yourself before doing anything:

1. Read `SCHEMA.md` — understand the domain, conventions, and tag taxonomy
2. Read `index.md` — learn what pages exist and their summaries
3. Scan recent `log.md` — read the last 20-30 entries to understand recent activity

Only after orientation should you ingest, query, or lint. This prevents duplicates, missing cross-references, and schema violations.

## Core Operations

### 1. Ingest

When the user provides a source (URL, file, paste, or direct knowledge), integrate it into the wiki:

1. **Capture the raw source** — save to appropriate `raw/` subdirectory
2. **Discuss takeaways** with the user
3. **Check what already exists** — read `index.md` and search existing pages with `Grep`
4. **Write or update wiki pages** — create only if Page Thresholds are met
5. **Update navigation** — add to `index.md`, append to `log.md`
6. **Report what changed** — list every file created or updated

### 2. Query

When the user asks a question about the wiki's domain:

1. Read `index.md` to identify relevant pages
2. For wikis with 100+ pages, also `Grep` for key terms
3. Read the relevant pages
4. Synthesize an answer from compiled knowledge with citations
5. File valuable answers back — create pages for substantial answers
6. Update `log.md` with the query and whether it was filed

### 3. Lint

When the user asks to lint, health-check, or audit the wiki:

1. **Orphan pages** — Find pages with no inbound `[[wikilinks]]`
2. **Broken wikilinks** — Find links pointing to pages that don't exist
3. **Index completeness** — Every wiki page should appear in `index.md`
4. **Frontmatter validation** — Check required fields and tags
5. **Stale content** — Pages not updated in 30+ days
6. **Contradictions** — Pages on the same topic with conflicting claims
7. **Page size** — Flag pages over 200 lines
8. **Tag audit** — List all tags, flag any not in taxonomy
9. **Log rotation** — Rotate if exceeds 500 entries
10. Report findings grouped by severity
11. Append to `log.md`

## Wiki Page Structure

Every wiki page MUST have YAML frontmatter. The format is compatible with Obsidian Properties.

### Standard YAML Format

```yaml
---
title: "Page Title"
tags: ["tag1", "tag2"]
created: "YYYY-MM-DDTHH:MM:SS.000Z"
updated: "YYYY-MM-DDTHH:MM:SS.000Z"
sources: ["session-id-abc123"]
links: ["other-page-slug.md"]
category: reference
confidence: medium
---
```

### Obsidian Properties Format

When the wiki is opened as an Obsidian vault, the same frontmatter is valid Obsidian Properties:

```yaml
---
title: "Page Title"
tags:
  - tag1
  - tag2
created: "YYYY-MM-DDTHH:MM:SS.000Z"
updated: "YYYY-MM-DDTHH:MM:SS.000Z"
sources:
  - session-id-abc123
links:
  - other-page-slug.md
category: reference
confidence: medium
---
```

Both formats are valid. Obsidian reads `tags` as its tag system and `created`/`updated` as date properties natively.

### Frontmatter Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | Yes | Human-readable title |
| `tags` | string[] | Yes | From approved taxonomy |
| `created` | ISO 8601 | Yes | UTC creation timestamp |
| `updated` | ISO 8601 | Yes | UTC last-modified timestamp |
| `sources` | string[] | No | Session IDs, URLs, file paths |
| `links` | string[] | No | Slugs of `[[wikilinks]]` in content |
| `category` | enum | Yes | See Categories below |
| `confidence` | enum | Yes | `high`, `medium`, `low` |

### Categories

Pages are organized by category:

| Category | Use for |
|----------|---------|
| `architecture` | System design, component relationships, data flows |
| `decision` | Architectural decisions, trade-off reasoning, "why we chose X" |
| `pattern` | Reusable code patterns, design patterns, conventions |
| `debugging` | Bug investigations, root causes, troubleshooting guides |
| `environment` | Project setup, tooling, environment-specific config |
| `session-log` | Auto-captured session metadata (not human-authored) |
| `reference` | API docs, CLI commands, configuration reference |
| `convention` | Team conventions, coding standards, style guidelines |

### Confidence Levels

- `high` — Confirmed by code, docs, or trusted source
- `medium` — Likely correct, minor uncertainty
- `low` — Unverified; user should double-check

## Cross-References

Use `[[wikilink]]` syntax to link between pages. Every page must link to at least 2 other wiki pages.

Link text format: `[[page-slug]]` or `[[page-slug|Display Text]]`.

## Merge Strategy

**Append only — never replace existing content.** If a page with the same title exists, read it first and merge.

When updating an existing page, append new content as a timestamped section:

```markdown
## Update (YYYY-MM-DD)

New content here...
```

Tags and sources are merged (union). Confidence is kept at the higher level.

After writing content, scan for `[[wikilinks]]` and update the `links:` field in frontmatter accordingly.

## Auto-Capture

At session end, capture significant discoveries as `session-log` pages. Configure via `wiki.autoCapture` in `.omc-config.json` (default: enabled).

## Hard Constraints

- **Merge, never replace** — if a page exists, always read it first and append; never delete and recreate
- **NO vector embeddings** — query uses keyword + tag matching only
- **Wiki pages are git-ignored** — wiki directory is project-local
- **Never modify `raw/`** — sources are immutable
- **Every page needs 2+ cross-references** — no orphan pages
- **Follow page thresholds** — don't create pages for passing mentions
- **Frontmatter is required** — all 7 fields must be present
- **Tags must come from taxonomy** — don't invent tags
- **Keep pages scannable** — split pages over 200 lines
- **Rotate the log** — rename to `log-YYYY.md` when it exceeds 500 entries
- **Handle contradictions explicitly** — note both claims, flag for review

## Searching the Wiki

```bash
# Find pages by content
Grep pattern="transformer" path="$WIKI" glob="*.md"

# Find pages by filename
Glob pattern="*.md" path="$WIKI/concepts"

# Recent activity
Read path="$WIKI/log.md" offset=<last 20 lines>
```

## Templates

Reference templates are in the `references/` directory:

| File | Purpose |
|------|---------|
| `references/SCHEMA-template.md` | Domain schema with category/tag taxonomy and policies |
| `references/page-template.md` | Individual wiki page template with frontmatter |
| `references/index-template.md` | Auto-generated index format |
| `references/log-template.md` | Append-only log entry format |

Read the relevant template before creating or updating wiki files.

## Initializing a New Wiki

When the user asks to create or start a wiki:

1. Determine the wiki path (default: `wiki/` relative to project root, e.g. `./wiki/`)
2. Create the directory structure
3. Ask the user what domain the wiki covers
4. Copy `references/SCHEMA-template.md` → `wiki/SCHEMA.md` and customize
5. Copy `references/index-template.md` → `wiki/index.md` with page count = 0
6. Create initial `wiki/log.md` with creation entry
7. Confirm the wiki is ready and suggest first sources to ingest

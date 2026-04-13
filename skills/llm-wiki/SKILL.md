---
name: llm-wiki
description: "Karpathy's LLM Wiki — build and maintain a persistent, interlinked markdown knowledge base. Ingest sources, query compiled knowledge, and lint for consistency. Activates when user says 'wiki', 'create a wiki', 'ingest into wiki', 'query wiki', 'wiki lint', or wants to build a personal knowledge base."
version: 1.0.0
author: Hermes Agent (NousResearch)
license: MIT
triggers: ["wiki", "wiki this", "wiki add", "wiki lint", "wiki query", "create a wiki", "ingest into wiki", "build a wiki", "knowledge base"]
---

# Karpathy's LLM Wiki

Build and maintain a persistent, compounding knowledge base as interlinked markdown files. Based on Karpathy's LLM Wiki pattern.

Unlike traditional RAG, the wiki compiles knowledge once and keeps it current. Cross-references are already there. Contradictions have already been flagged.

**Division of labor:** The human curates sources and directs analysis. The agent summarizes, cross-references, files, and maintains consistency.

## When This Skill Activates

Use this skill when the user asks to create a wiki, ingest sources, query an existing wiki, or lint for consistency.

## Wiki Location

Default: `~/wiki` (configurable via `wiki.path` in `.omc-config.json` or environment variable `WIKI_PATH`).

The wiki is just a directory of markdown files — open it in Obsidian, VS Code, or any editor.

## Architecture: Three Layers

```
wiki/
├── SCHEMA.md           # Conventions, structure rules, domain config
├── index.md            # Sectioned content catalog with one-line summaries
├── log.md              # Chronological action log
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

## Resuming an Existing Wiki (CRITICAL — do this every session)

When the user has an existing wiki, **always orient yourself before doing anything**:

① **Read `SCHEMA.md`** — understand the domain, conventions, and tag taxonomy.
② **Read `index.md`** — learn what pages exist and their summaries.
③ **Scan recent `log.md`** — read the last 20-30 entries to understand recent activity.

Only after orientation should you ingest, query, or lint. This prevents creating duplicates, missing cross-references, contradicting schema conventions, or repeating work.

## Initializing a New Wiki

When the user asks to create or start a wiki:

1. Determine the wiki path (default: `~/wiki`)
2. Create the directory structure
3. Ask the user what domain the wiki covers
4. Write `SCHEMA.md` customized to the domain
5. Write initial `index.md` with sectioned header
6. Write initial `log.md` with creation entry
7. Confirm the wiki is ready and suggest first sources to ingest

### SCHEMA.md Template

The schema constrains agent behavior and ensures consistency. Include domain, conventions, frontmatter template, tag taxonomy, page thresholds, and update policy.

### index.md Template

The index is sectioned by type. Each entry is one line: wikilink + summary. Scale by splitting sections when they exceed 50 entries.

### log.md Template

Chronological record of all wiki actions. Append-only. Rotate when it exceeds 500 entries.

## Core Operations

### 1. Ingest

When the user provides a source (URL, file, paste), integrate it into the wiki:

① **Capture the raw source** — save to appropriate `raw/` subdirectory
② **Discuss takeaways** with the user
③ **Check what already exists** — search index.md and use `Grep` tool
④ **Write or update wiki pages** — create only if Page Thresholds met
⑤ **Update navigation** — add to index.md, append to log.md
⑥ **Report what changed** — list every file created or updated

### 2. Query

When the user asks a question about the wiki's domain:

① **Read `index.md`** to identify relevant pages
② **For wikis with 100+ pages**, also `Grep` for key terms
③ **Read the relevant pages**
④ **Synthesize an answer** from compiled knowledge
⑤ **File valuable answers back** — create pages for substantial answers
⑥ **Update log.md** with the query and whether it was filed

### 3. Lint

When the user asks to lint, health-check, or audit the wiki:

① **Orphan pages** — Find pages with no inbound `[[wikilinks]]`
② **Broken wikilinks** — Find links pointing to pages that don't exist
③ **Index completeness** — Every wiki page should appear in `index.md`
④ **Frontmatter validation** — Check required fields and tags
⑤ **Stale content** — Pages not updated in 90+ days
⑥ **Contradictions** — Pages on the same topic with conflicting claims
⑦ **Page size** — Flag pages over 200 lines
⑧ **Tag audit** — List all tags, flag any not in taxonomy
⑨ **Log rotation** — Rotate if exceeds 500 entries
⑩ **Report findings** grouped by severity
⑪ **Append to log.md**

## Working with the Wiki

### Searching

```bash
# Find pages by content
Grep pattern="transformer" path="$WIKI" glob="*.md"

# Find pages by filename
Glob pattern="*.md" path="$WIKI/entities"

# Recent activity
read_file path="$WIKI/log.md" offset=<last 20 lines>
```

### Bulk Ingest

Batch updates: read all sources, identify entities, check existing pages, create/update in one pass, update index once at end.

### Obsidian Integration

The wiki directory works as an Obsidian vault. Install Dataview plugin for queries.

## Pitfalls

- **Never modify files in `raw/`** — sources are immutable
- **Always orient first** — read SCHEMA + index + recent log before any operation
- **Always update index.md and log.md** — these are the navigational backbone
- **Don't create pages for passing mentions** — follow Page Thresholds
- **Don't create pages without cross-references** — every page must link to at least 2 other pages
- **Frontmatter is required**
- **Tags must come from the taxonomy**
- **Keep pages scannable** — split pages over 200 lines
- **Ask before mass-updating** — confirm scope for 10+ page updates
- **Rotate the log** — rename to `log-YYYY.md` when exceeding 500 entries
- **Handle contradictions explicitly** — note both claims, mark in frontmatter, flag for review

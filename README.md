# LLM Wiki — Skill for Claude Code

A persistent, compounding markdown knowledge base for Claude Code. Based on [Karpathy's LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) pattern.

Unlike traditional RAG — where knowledge lives in raw chunks and is re-searched at query time — the wiki compiles knowledge into durable, interlinked markdown pages. The LLM reads the wiki, not the raw sources.

## Installation

### Option 1 — Clone the repo (recommended)

```bash
# Global install (all projects)
git clone --depth 1 https://github.com/Changhochien/claude-wiki.git ~/.claude/skills/llm-wiki

# Project install
git clone --depth 1 https://github.com/Changhochien/claude-wiki.git /tmp/claude-wiki
cp -r /tmp/claude-wiki/skills/llm-wiki ~/.claude/skills/
```

### Option 2 — Copy individual files

```bash
# Create the skill directory
mkdir -p ~/.claude/skills/llm-wiki

# Copy SKILL.md and the subdirectories
cp SKILL.md ~/.claude/skills/llm-wiki/
cp -r commands/ references/ ~/.claude/skills/llm-wiki/
```

## Quick Start

### Initialize a wiki

```
/wiki-ingest
```

Or just tell Claude:

> "create a wiki for my trading strategies"

Claude will scaffold the directory structure and walk you through the first ingest.

### Add knowledge

```
/wiki-ingest
```

Paste a URL, file, or notes. Claude extracts key concepts, creates wiki pages, and cross-links them.

### Ask questions

```
/wiki-query
```

> "what do I know about mean-reversion strategies?"

Claude searches the wiki and answers with citations.

### Health check

```
/wiki-lint
```

Claude audits for orphan pages, broken links, stale content, and contradictions.

## Slash Commands

| Command | Use for |
|---------|---------|
| `/wiki-ingest` | Add sources or direct knowledge to the wiki |
| `/wiki-query` | Ask a question against compiled knowledge |
| `/wiki-lint` | Run a wiki health check |

Natural language also triggers the skill: "save this article", "query my wiki", "lint my wiki", "remember this", "I learned something about X".

## Wiki Structure

```
wiki/
├── SCHEMA.md              # Domain conventions, tag taxonomy, policies
├── index.md               # Auto-maintained catalog of all pages
├── log.md                 # Append-only operation history
├── raw/                   # Layer 1: Immutable sources (articles, papers, transcripts)
│   ├── articles/
│   ├── papers/
│   └── transcripts/
├── entities/              # Layer 2: People, orgs, products, models
├── concepts/             # Layer 2: Topics, ideas, techniques
├── comparisons/          # Layer 2: Side-by-side analyses
└── queries/              # Layer 2: Filed Q&A worth preserving
```

**Layer 1 — Raw Sources:** Immutable. Claude reads but never modifies these.
**Layer 2 — The Wiki:** Claude-owned pages. Created, updated, and cross-referenced by Claude.

## Wiki Page Format

Every page has YAML frontmatter:

```yaml
---
title: "JWT Auth Architecture"
tags: ["auth", "security", "architecture"]
created: "2026-04-14T10:30:00.000Z"
updated: "2026-04-14T10:30:00.000Z"
sources: ["session-abc123"]
links: ["refresh-token-pattern.md", "oauth-flow.md"]
category: architecture
confidence: high
---

# JWT Auth Architecture

## Overview

## Key Points

## Related
```

The frontmatter is compatible with [Obsidian Properties](https://help.obsidian.md/Properties/Overview) — open the wiki folder as an Obsidian vault and it works natively.

## 8 Categories

| Category | Use for |
|----------|---------|
| `architecture` | System design, component relationships, data flows |
| `decision` | Architectural decisions, trade-off reasoning |
| `pattern` | Reusable code patterns, design patterns |
| `debugging` | Bug investigations, root causes, troubleshooting |
| `environment` | Project setup, tooling, environment config |
| `session-log` | Auto-captured session metadata |
| `reference` | API docs, CLI commands, configuration reference |
| `convention` | Team conventions, coding standards |

## Core Operations

### Ingest

Drop a source (URL, paper, notes) into the wiki. Claude:

1. Saves the raw source to `raw/`
2. Creates or merges wiki pages
3. Extracts `[[wikilinks]]` and updates frontmatter
4. Updates `index.md` and `log.md`
5. Reports what was created/updated

**Merge strategy:** If a page already exists, Claude appends under a `## Update (YYYY-MM-DD)` heading — never replaces.

### Query

Ask the wiki a question. Claude:

1. Searches by keyword and tag
2. Reads relevant pages
3. Synthesizes an answer with page citations
4. Offers to save the answer as a wiki page

**No vector embeddings** — keyword + tag search only. The LLM does the synthesis.

### Lint

Health-check the wiki. Detects:

1. Orphan pages (no inbound links)
2. Broken wikilinks (links to non-existent pages)
3. Index incompleteness (pages not in index.md)
4. Frontmatter violations (missing fields)
5. Stale content (not updated in 30+ days)
6. Contradictions (conflicting high/low confidence claims)
7. Oversized pages (over 200 lines)
8. Untaxonomied tags
9. Log overflow (log rotation at 500 entries)

## Configuration

### Wiki path

```bash
export WIKI_PATH=~/my-knowledge-base
```

Or in `.omc-config.json`:

```json
{
  "wiki": {
    "path": "~/wiki",
    "autoCapture": true,
    "staleDays": 30
  }
}
```

### Auto-capture (OMC only)

At session end, Claude auto-saves significant discoveries as `session-log` pages. Enable/disable via `wiki.autoCapture` in `.omc-config.json`.

## Obsidian Integration

The wiki is a plain markdown directory. Open it directly as an Obsidian vault — no import needed.

- `tags:` maps to Obsidian's tag system
- `created`/`updated` map to Obsidian date properties
- `[[wikilinks]]` work as native Obsidian links
- Install the [Dataview](https://obsidian.md/plugins?id=dataview) plugin for querying across pages

## Architecture: Why This Works

> "The tedious part of maintaining a knowledge base is not the reading or the thinking — it's the bookkeeping. LLMs don't get bored, don't forget to update a cross-reference, and can touch 15 files in one pass." — Andrej Karpathy

Traditional RAG retrieves raw chunks at query time. The wiki pattern compiles knowledge once, keeping it current. Cross-references are maintained. Contradictions are flagged. The wiki is the single source of truth.

## Hard Constraints

- **Merge, never replace** — always append to existing pages
- **NO vector embeddings** — keyword + tag search only
- **Wiki pages are git-ignored** — project-local by default
- **Never modify `raw/`** — sources are immutable
- **Every page needs 2+ cross-references** — no orphan pages
- **Frontmatter is required** — all 7 fields must be present
- **Tags must come from taxonomy** — don't invent tags
- **Keep pages scannable** — split pages over 200 lines

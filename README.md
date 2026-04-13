# LLM Wiki for Claude Code

A persistent, interlinked markdown knowledge base skill for Claude Code, inspired by Andrej Karpathy's LLM Wiki pattern.

## Installation

One line install:
```bash
mkdir -p ~/.claude/skills && curl -fsSL https://raw.githubusercontent.com/Changhochien/claude-wiki/main/skills/llm-wiki/SKILL.md -o ~/.claude/skills/llm-wiki/SKILL.md
```

Or clone the entire repo:
```bash
git clone https://github.com/YOUR_USERNAME/claude-wiki.git ~/.claude/skills/llm-wiki-src
```

## What It Does

- **Ingest** sources (URLs, files, notes) into a layered wiki structure
- **Query** the compiled knowledge base
- **Lint** for consistency, orphans, broken links, stale content
- Works with Obsidian as a vault

## Wiki Structure

```
~/wiki/               # or custom path
├── SCHEMA.md        # conventions & rules
├── index.md         # content catalog
├── log.md           # action history
├── raw/             # immutable sources
├── entities/        # people, orgs, products
├── concepts/        # topics & ideas
├── comparisons/     # side-by-side analysis
└── queries/        # filed Q&A
```

## Quick Start

1. Install the skill
2. Say "create a wiki" or "build a wiki for [topic]"
3. Claude will initialize the structure and guide first ingest

## Configuration

Set wiki path via environment variable:
```bash
export WIKI_PATH=~/my-knowledge-base
```

Or add to your `.omc-config.json`:
```json
{ "wiki": { "path": "~/my-knowledge-base" } }
```

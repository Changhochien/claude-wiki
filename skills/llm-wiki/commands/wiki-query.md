---
name: wiki-query
description: Query the wiki — search all wiki pages by keyword, tag, or category and synthesize an answer with citations. Use when the user says "what do I know about X", "query my wiki", "what does my wiki say about", "search my wiki", "what have I learned about".
---

# Wiki Query

Search the wiki and synthesize an answer.

## Usage

```
wiki_query({ query: "authentication", tags: ["auth"], category: "architecture" })
```

## When to Use

- User asks what the wiki knows about a topic
- User says "what do I know about X", "query wiki", "search wiki"
- User wants to recall past research, decisions, or findings
- Before ingesting new knowledge — check if related pages already exist

## Process

1. **Read `index.md`** — find relevant pages by category
2. **Grep for keywords** — especially for wikis with 100+ pages
3. **Read relevant pages** — synthesize from matched content
4. **Cite sources** — link to wiki pages as references
5. **File if valuable** — if the answer is substantial, offer to save it as a wiki page
6. **Log the query** — append to `log.md` with query text and result count

## Query Parameters

| Parameter | Description |
|-----------|-------------|
| `query` | Search text — matched against title, tags, and content |
| `tags` | Optional tag filter (OR match) |
| `category` | Optional category filter |
| `limit` | Max results (default: 20) |

## Hard Constraint

**NO vector embeddings** — search uses keyword + tag matching only. The LLM synthesizes from text matches.

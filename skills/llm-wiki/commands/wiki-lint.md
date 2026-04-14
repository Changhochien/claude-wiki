---
name: wiki-lint
description: Run a health check on the wiki — detect orphan pages, broken links, stale content, oversized pages, and contradictions. Use when the user says "lint my wiki", "health check my wiki", "wiki audit", "check for broken links".
---

# Wiki Lint

Run a health check on the wiki knowledge base.

## When to Use

- User says "lint my wiki", "health check", "wiki audit", "check wiki"
- Periodic maintenance — run at least monthly
- Before a major project review or documentation push

## The 9 Lint Checks

### 1. Orphan Pages
Find pages with no inbound `[[wikilinks]]` from other pages. Every page should link to at least 2 other pages.

### 2. Broken Wikilinks
Find `[[wikilinks]]` pointing to pages that don't exist. Fix or remove broken links.

### 3. Index Completeness
Every wiki page (`.md` file not in `raw/` or `assets/`) must appear in `index.md`.

### 4. Frontmatter Validation
Check every page has all 7 required frontmatter fields:
- `title`, `tags`, `created`, `updated`, `sources`, `links`, `category`, `confidence`

### 5. Stale Content
Pages not updated in 30+ days. Flag for review — knowledge may be outdated.

### 6. Contradictions
Pages on the same topic with conflicting `confidence: high` vs `confidence: low` claims. Note both and flag for human review.

### 7. Oversized Pages
Pages over 200 lines. Consider splitting into sub-pages.

### 8. Tag Audit
List all tags used. Flag any not in the approved taxonomy from `SCHEMA.md`.

### 9. Log Rotation
If `log.md` exceeds 500 entries, rename to `log-YYYY.md` and start a new `log.md`.

## Report Format

Report findings grouped by severity:

```
## Lint Report — YYYY-MM-DD

### Errors
- [broken-ref] [[auth-architecture]] → page not found

### Warnings
- [stale] "JWT token refresh" not updated in 67 days
- [oversized] "Microservices overview" is 340 lines — split recommended

### Info
- [orphan] "Legacy auth scheme" has no inbound links
- [tag] "ml-approach" not in taxonomy
```

## Post-Lint

After fixing issues, append to `log.md`:
```markdown
## [YYYY-MM-DD] lint
- **Pages:** page1.md, page2.md
- **Summary:** Lint: 5 issues (1 error, 2 warnings, 2 info)
```

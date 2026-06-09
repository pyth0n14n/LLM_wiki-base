# Codex Instructions for LLM Wiki

This repository is a personal LLM Wiki based on Karpathy's LLM Wiki pattern.
Codex should treat it as a maintained knowledge base, not as a generic notes
folder. `AGENTS.md` is the single source of operating rules for Codex.

## Architecture

This wiki has three layers.

1. `raw/`: source-of-truth layer
   - Stores articles, papers, book excerpts, transcripts, assets, and inbox
     items.
   - Codex may read raw sources during ingest.
   - Codex must not edit raw source files unless the user explicitly asks.
   - Markdown and text raw sources should be versioned. Large binaries should
     stay out of git.

2. `wiki/`: maintained knowledge layer
   - Codex may create and edit pages here.
   - This is the durable wiki that compounds over time.
   - Prefer updating existing related pages over creating isolated notes.

3. `AGENTS.md`: schema and protocol layer
   - Defines how Codex should ingest, query, lint, and maintain the wiki.
   - Change this file only when the workflow itself is being improved.

`output/` is temporary. Durable knowledge must be moved into `wiki/syntheses/`
or `wiki/questions/`.

## Directory Semantics

- `raw/articles/`: web articles and blog posts
- `raw/papers/`: research papers or extracted paper text
- `raw/books/`: book excerpts
- `raw/videos/`: transcripts and video notes
- `raw/assets/`: images, figures, binary assets
- `raw/inbox/`: unprocessed sources
- `wiki/summaries/`: source-by-source summaries
- `wiki/entities/`: people, organizations, tools, products, named things
- `wiki/concepts/`: ideas, patterns, theories, mechanisms, frameworks
- `wiki/syntheses/`: settled analysis across multiple sources
- `wiki/questions/`: unresolved questions, hypotheses, research plans
- `wiki/misc/`: maintenance reports and uncategorized wiki files
- `wiki/index.md`: catalog of wiki pages
- `wiki/log.md`: chronological operation log

## General Rules

- Keep edits small and focused.
- Preserve provenance. Important factual claims should point back to a raw
  source or a source summary.
- Separate facts from interpretation. Put uncertain reasoning in `Notes` or
  `Open Questions`, not in factual claim lists.
- Do not delete pages to resolve uncertainty. Mark `status` as `stale`,
  `contradicted`, or `archived` and explain why.
- Update `wiki/index.md` and `wiki/log.md` whenever ingest, query filing, or lint
  changes the wiki.
- Use English snake_case filenames. Put Japanese or human-friendly titles in
  frontmatter.
- Use wikilinks: `[[page-slug]]` or `[[page-slug|display text]]`.
- Prefer bidirectional links for important relationships.

## Metadata

All wiki pages should start with YAML frontmatter.

```yaml
---
title: "[日本語タイトル]"
tags: [tag1, tag2]
sources: 0
created: "YYYY-MM-DD"
last_updated: "YYYY-MM-DD"
status: draft
source_refs: []
---
```

Allowed `status` values:

- `draft`: newly created or not yet reviewed
- `active`: usable wiki page
- `stale`: old or needs review
- `contradicted`: materially contradicted by another source
- `archived`: retained for history, not part of active knowledge

## Claims and Evidence

Use `## Claims / Evidence` when a page contains factual claims that may matter
later.

```markdown
## Claims / Evidence

- Claim: [wiki に残す主張]
  Evidence: `raw/articles/example.md`, section "[見出し]", or [[summary-page]]
  Confidence: high|medium|low
  Notes: [解釈・制約・未確認点]
```

Rules:

- Every nontrivial factual claim should be traceable to raw source material or a
  summary page.
- Use `Confidence: low` when the claim depends on interpretation, weak evidence,
  or incomplete context.
- Do not fabricate page numbers, sections, dates, URLs, or citations.

## Operation: Ingest

Use this workflow when the user asks to ingest a source from `raw/`.

1. Receive
   - Identify the source file path and source type.
   - If the source is binary or unreadable, ask for an extracted text/Markdown
     version or use only available metadata.

2. Read and analyze
   - Read the source.
   - Extract key points, entities, concepts, claims, contradictions, and open
     questions.
   - Track evidence locations such as headings, page numbers, timestamps, URLs,
     or quoted fragments when available.

3. Create or update summary
   - Create a summary page in `wiki/summaries/`.
   - Use `wiki/summaries/TEMPLATE.md` as the default shape.
   - Include `Claims / Evidence` and `Open Questions` when useful.

4. Create or update entities and concepts
   - Add or update `wiki/entities/` pages for important people, organizations,
     tools, products, and named things.
   - Add or update `wiki/concepts/` pages for reusable ideas, mechanisms,
     theories, and patterns.
   - Prefer enriching existing pages over creating duplicates.

5. Cross-link
   - Link summary pages to related entities and concepts.
   - Add backlinks or mentions on important related pages.
   - Record contradictions explicitly instead of silently overwriting.

6. Update catalog and log
   - Add new pages to `wiki/index.md`.
   - Append to `wiki/log.md` using this format:

```markdown
## [YYYY-MM-DD] ingest | [source-title]

- Source: `raw/path/to/source.md`
- Created: [[summary-page]], [[entity-page]]
- Updated: [[concept-page]]
- Notes: [brief note]
```

## Operation: Query

Use this workflow when the user asks a question about the wiki.

1. Search
   - Start with `wiki/index.md`.
   - Use text search over `wiki/` when needed.
   - Read all relevant pages before answering.

2. Synthesize
   - Answer from the maintained wiki, not memory alone.
   - Cite relevant wiki pages with wikilinks.
   - Mention contradictions, stale information, or missing evidence.

3. File durable results
   - If the answer creates reusable analysis or a settled comparison, create or
     update a page in `wiki/syntheses/`.
   - If the answer exposes an unresolved question or research plan, create or
     update a page in `wiki/questions/`.
   - Do not leave durable knowledge only in chat or `output/`.

4. Update catalog and log when pages change

```markdown
## [YYYY-MM-DD] query | [question summary]

- Question: [user question]
- Pages read: [[page-a]], [[page-b]]
- Filed: [[synthesis-or-question-page]]
```

## Operation: Lint

Use this workflow when the user asks for wiki maintenance or a health check.

Check for:

- Contradictions between pages
- Stale claims
- Orphan pages
- Missing cross-references
- Duplicate pages
- Claims without evidence
- Questions that can now be resolved into syntheses

Write lint reports under `wiki/misc/`, preferably with a date in the filename.

```markdown
# Lint Report - YYYY-MM-DD

## Contradictions

- [[page-a]] vs [[page-b]]: [issue]

## Stale Claims

- [[page-x]]: [reason]

## Orphan Pages

- [[page-y]]: [suggested action]

## Missing Evidence

- [[page-z]]: [claim needing evidence]

## Suggested Next Steps

- [action]
```

## Page Types

### Summary Pages

Use `wiki/summaries/TEMPLATE.md`.

Include:

- Short source summary
- Key takeaways
- Claims / evidence
- Main structure
- Important quotes or excerpts when useful and copyright-safe
- Related entities and concepts
- Contradictions or conflicts
- Open questions
- Source information

### Entity Pages

Use `wiki/entities/TEMPLATE.md`.

Include:

- Definition
- Overview
- Key attributes
- Mentions
- Claims / evidence
- Related entities and concepts
- Open questions

### Concept Pages

Use `wiki/concepts/TEMPLATE.md`.

Include:

- Definition
- Explanation
- Key components or principles
- Examples
- Sources
- Claims / evidence
- Related concepts and entities
- Open questions

### Question Pages

Use `wiki/questions/TEMPLATE.md`.

Question pages are for unfinished thinking. When resolved, link to the relevant
`wiki/syntheses/` page from the `Resolution` section.

### Synthesis Pages

Synthesis pages should contain settled cross-source analysis. They should cite
the summary/entity/concept pages they draw from and clearly distinguish
conclusion, evidence, and open caveats.

## Source Control

- Initialize git if the user wants version control.
- Commit `wiki/`, `AGENTS.md`, `README.md`, `.vscode/`, and text sources under
  `raw/`.
- Keep large binary raw files out of git by default.
- Do not run destructive git commands unless the user explicitly asks.

## VS Code and Foam

This workspace is intended to work in VS Code with Foam.

- Foam should treat `wiki/`, `AGENTS.md`, and `README.md` as the maintained note
  graph.
- `raw/` is searchable evidence, not the primary wiki graph.
- `output/` is temporary and should not become a second wiki.

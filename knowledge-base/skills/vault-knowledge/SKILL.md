---
name: vault-knowledge
description: >
  This skill should be used when the user asks about "the vault", "the knowledge base",
  "company brain", "our notes", "how does the vault work", "where do I put this",
  "what's in the vault", or needs to understand the Zettelkasten system structure,
  note types, frontmatter conventions, naming conventions, or linking rules. It provides
  the complete system specification for the Company Brain knowledge base.
version: 0.1.0
---

# Company Brain — Vault Knowledge

The Company Brain is a git-based Zettelkasten knowledge system hosted on GitHub. Humans write, Claude organizes. The vault is a Quartz static site built from markdown files.

## Repository

The vault lives at `Bolt-Farm-Treehouse/knowledge-base` on GitHub. All content is in the `content/` directory. System configuration is in `.zettelkasten/`.

**Connection:** The vault is always accessed via the GitHub API through the Composio GitHub MCP connector — never through the local filesystem. See `references/vault-connection.md` for the complete access layer specification, including read/write patterns, pre-flight checks, and error handling.

## Vault Structure

```
content/
├── inbox/              → Drop zone for raw documents
├── notes/
│   ├── permanent/      → Atomic ideas (the gold)
│   ├── literature/     → Source summaries
│   ├── fleeting/       → Quick captures
│   └── source/         → Original documents preserved
├── maps/               → Maps of Content (topic indexes)
├── skills/             → Claude skills (the prompt mine)
├── projects/           → Active project workspaces
├── decisions/          → Decision records
├── people/             → Contacts and stakeholders
└── journal/            → Daily notes
```

## Note Types

There are five core note types, each with specific frontmatter and purpose. See `references/note-types.md` for full templates and rules.

**Summary:**

- **Fleeting** — Quick thoughts, unrefined. Temporary. Claude promotes or archives them.
- **Literature** — Summaries of external sources, in your own words.
- **Permanent** — Atomic, standalone ideas. One idea per note. The core unit.
- **Map of Content (MOC)** — Curated entry points that tell a story about how ideas connect.
- **Source** — Original documents preserved as-is for provenance.
- **Decision** — Decision records with context, options, and consequences.

## Frontmatter Rules

Every note MUST have this minimum frontmatter:

```yaml
---
type: fleeting | literature | permanent | moc | source | decision | project | person
created: YYYY-MM-DDTHH:MM:SS
author: name | "claude" | "claude/name"
status: unprocessed | draft | active | archived
tags: []
---
```

## Naming Conventions

- **Permanent notes:** `YYYYMMDDHHMMSS-descriptive-slug.md`
- **Literature notes:** `lit-source-slug.md`
- **MOCs:** `moc-topic-slug.md`
- **Source notes:** `src-original-title-slug.md`
- **Decision records:** `dec-YYYY-MM-DD-decision-slug.md`

## Link Style

Use `[[wiki-links]]` everywhere. Links are always bidirectional — if A links to B, B links back to A.

## Git Workflow

- `main` branch: The live vault. Quartz builds from this.
- Claude commits as: `Claude <claude@boltfarmtreehouse.com>`
- Commit message prefixes: `[intake]`, `[atomize]`, `[link]`, `[index]`, `[review]`, `[human]`

## Additional Resources

- **`references/note-types.md`** — Full templates and rules for every note type
- **`references/processing-pipeline.md`** — The five-stage Claude processing pipeline

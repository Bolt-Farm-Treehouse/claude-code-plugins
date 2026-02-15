# Knowledge Base

Manage the Company Brain Zettelkasten from Claude Cowork. Process documents into atomic notes, search the vault, create new notes, and run health checks — all through the GitHub connector.

## How to Use

### Slash Commands

| Command | What it does |
|---------|-------------|
| `/process-doc` | Process a document into atomic vault notes |
| `/ask-vault` | Search and ask questions about vault knowledge |
| `/vault-health` | Run a health check on the knowledge base |
| `/new-note` | Create a new note (fleeting, permanent, decision, or literature) |

### Natural Language

Just talk about the vault:
- "Add this to the knowledge base"
- "What do we know about X?"
- "How's the vault looking?"
- "I want to capture a quick thought"
- "Process these meeting notes"

## Components

| Component | Name | Purpose |
|-----------|------|---------|
| Command | `/process-doc` | Full document processing pipeline |
| Command | `/ask-vault` | Search and synthesize answers from vault |
| Command | `/vault-health` | Vault health audit and report |
| Command | `/new-note` | Create individual notes of any type |
| Skill | `vault-knowledge` | System spec, note types, naming conventions |
| Skill | `atomizer` | Document atomization process and linking rules |
| Skill | `vault-review` | Health check procedures and reporting |

## Requirements

This plugin requires the GitHub MCP connector to be active in your Cowork session, with access to the `Bolt-Farm-Treehouse/knowledge-base` repository.

## Vault Repository

The knowledge base lives at: **GitHub → Bolt-Farm-Treehouse/knowledge-base**

The vault is a Quartz static site deployed to GitHub Pages. Every push to `main` triggers a rebuild.

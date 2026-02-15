# Knowledge Base

Manage the Company Brain Zettelkasten from Claude Cowork. Process documents into atomic notes, ingest from Google Drive, ClickUp, meetings, and Cowork sessions, with intelligent discernment gating and automated daily sweeps — all through the GitHub API.

## How It Works

The vault lives on GitHub at `Bolt-Farm-Treehouse/knowledge-base`. Every read and write goes through the GitHub API via the Composio GitHub MCP connector. This means the plugin works regardless of what workspace folder you have mounted — the vault is always accessible.

## Slash Commands

| Command | What it does |
|---------|-------------|
| `/process-doc` | Process a document into atomic vault notes |
| `/ask-vault` | Search and ask questions about vault knowledge |
| `/vault-health` | Run a health check on the knowledge base |
| `/new-note` | Create a new note (fleeting, permanent, decision, or literature) |
| `/drive-ingest` | Scan Google Drive for docs not yet in the vault |
| `/clickup-ingest` | Pull knowledge from ClickUp tasks, docs, and comments |
| `/discern` | Evaluate whether content is worth adding to the vault |
| `/cowork-sync` | Sync Cowork session artifacts to the vault |
| `/meeting-ingest` | Extract knowledge from meeting transcripts |
| `/daily-sweep` | Run all ingestion sources to bring the vault up to date |

### Natural Language

Just talk about the vault:
- "Add this to the knowledge base"
- "What do we know about X?"
- "How's the vault looking?"
- "I want to capture a quick thought"
- "Process these meeting notes"
- "Sync Google Drive"
- "Check ClickUp for new decisions"
- "Run the daily sweep"
- "Is this worth adding to the vault?"

## Components

### Core Skills

| Skill | Purpose |
|-------|---------|
| `vault-knowledge` | System spec, note types, naming conventions, vault connection layer |
| `atomizer` | Document atomization process and linking rules |
| `vault-review` | Health check procedures and reporting |

### Ingestion Skills

| Skill | Purpose |
|-------|---------|
| `drive-ingest` | Scan Google Drive (My Drive, Shared Drive, shared-with-me) for unvaulted documents |
| `clickup-ingest` | Extract knowledge from ClickUp tasks, docs, and comments |
| `meeting-ingest` | Process Google Meet/Zoom transcripts for decisions and insights |
| `cowork-sync` | Capture Cowork session artifacts worth preserving |

### Intelligence Skills

| Skill | Purpose |
|-------|---------|
| `discernment` | Score content on vault-worthiness (0-15) and route: atomize, fleeting, or skip |
| `daily-sweep` | Orchestrate all ingestion sources in sequence with batch discernment |

## Requirements

### GitHub MCP Connector (Required)

The GitHub connector must be active in every Cowork session for vault access. It provides:
- **Read**: List directories, read files, search content on the `main` branch
- **Write**: Create/update files, commit directly to `main`

The vault is NEVER accessed through the local filesystem. All access goes through GitHub API. See `skills/vault-knowledge/references/vault-connection.md` for the full connection specification.

### Google Drive MCP (For Drive + Meeting ingestion)

Required for `/drive-ingest` and `/meeting-ingest`. Provides access to My Drive, Shared Drive (Bolt Farm), and shared-with-me documents.

### ClickUp MCP (For ClickUp ingestion)

Required for `/clickup-ingest`. Provides access to tasks, documents, and comments.

## Vault Repository

The knowledge base lives at: **GitHub → Bolt-Farm-Treehouse/knowledge-base**

The vault is a Quartz static site deployed to GitHub Pages. Every push to `main` triggers a rebuild.

## Architecture

```
Sources                    Pipeline                  Vault (GitHub)
─────────                  ────────                  ──────────────
Google Drive  ──┐
ClickUp       ──┼──→  Discernment  ──→  Atomizer  ──→  content/
Meeting Notes ──┤     (score 0-15)     (5 stages)     ├── notes/permanent/
Cowork Output ──┘                                      ├── notes/source/
                                                       ├── notes/fleeting/
                  Daily Sweep orchestrates              ├── maps/
                  all sources + batch discernment       └── decisions/
```

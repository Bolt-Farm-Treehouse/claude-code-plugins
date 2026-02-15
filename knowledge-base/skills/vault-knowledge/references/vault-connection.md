# Vault Connection — GitHub API Access Layer

Every skill in this plugin reads from and writes to the vault through the GitHub API via the Composio GitHub MCP connector. This reference documents the exact tools, patterns, and constants needed.

**The vault is NEVER accessed through the local filesystem or workspace folder.** It is always accessed through the GitHub API. This means skills work regardless of what folder the user has mounted in Cowork.

## Constants

```yaml
VAULT_OWNER: "Bolt-Farm-Treehouse"
VAULT_REPO: "knowledge-base"
VAULT_BRANCH: "main"
VAULT_CONTENT_ROOT: "content/"
COMMIT_AUTHOR_NAME: "Claude"
COMMIT_AUTHOR_EMAIL: "claude@boltfarmtreehouse.com"
```

## Pre-Flight Check

**Every skill MUST run this check before doing any vault operations.**

1. Verify the GitHub MCP connector is active by calling `GITHUB_GET_A_REPOSITORY` with owner/repo
2. If it fails → tell the user: "GitHub isn't connected. Please reconnect it so I can access the vault."
3. If it succeeds → confirm the `default_branch` is `main` and `permissions.push` is `true`
4. Store the connection status so subsequent operations in the same session don't re-check

```python
# Pre-flight check pattern
result, error = run_composio_tool("GITHUB_GET_A_REPOSITORY", {
    "owner": "Bolt-Farm-Treehouse",
    "repo": "knowledge-base"
})
if error:
    raise Exception("GitHub not connected. Ask user to reconnect.")
data = result.get("data", {}).get("data", result.get("data", {}))
assert data.get("permissions", {}).get("push"), "No write access to vault repo"
```

## Reading Files

### Read a single file

Use `GITHUB_GET_REPOSITORY_CONTENT` to read a file. Content is returned as Base64.

```python
import base64

result, error = run_composio_tool("GITHUB_GET_REPOSITORY_CONTENT", {
    "owner": "Bolt-Farm-Treehouse",
    "repo": "knowledge-base",
    "path": "content/notes/permanent/some-note.md",
    "ref": "main"
})
data = result.get("data", {}).get("data", result.get("data", {}))

# Handle nested content wrapper
if isinstance(data, dict) and "content" in data:
    if isinstance(data["content"], dict):
        b64 = data["content"].get("content", "")
        sha = data["content"].get("sha")
    elif isinstance(data["content"], str):
        b64 = data["content"]
        sha = data.get("sha")
    text = base64.b64decode(b64.replace("\n", "")).decode("utf-8", errors="replace")
```

### List a directory

Use `GITHUB_GET_REPOSITORY_CONTENT` with a directory path. Returns an array of entries.

```python
result, error = run_composio_tool("GITHUB_GET_REPOSITORY_CONTENT", {
    "owner": "Bolt-Farm-Treehouse",
    "repo": "knowledge-base",
    "path": "content/notes/permanent",
    "ref": "main"
})
data = result.get("data", {}).get("data", result.get("data", {}))

# Directory listings come as an array (possibly nested under "content")
if isinstance(data, dict) and "content" in data and isinstance(data["content"], list):
    entries = data["content"]
elif isinstance(data, list):
    entries = data
else:
    entries = []

for entry in entries:
    print(f"{entry['type']}  {entry['name']}")
```

### List the entire vault tree

Use `GITHUB_GET_A_TREE` with `recursive=true` for a full inventory.

```python
result, error = run_composio_tool("GITHUB_GET_A_TREE", {
    "owner": "Bolt-Farm-Treehouse",
    "repo": "knowledge-base",
    "tree_sha": "main",
    "recursive": True
})
data = result.get("data", {}).get("data", result.get("data", {}))
tree = data.get("tree", [])
md_files = [e["path"] for e in tree if e.get("type") == "blob" and e["path"].endswith(".md")]
```

### Search file contents

Use `GITHUB_SEARCH_CODE` to find notes containing specific text.

```python
result, error = run_composio_tool("GITHUB_SEARCH_CODE", {
    "q": "pricing strategy repo:Bolt-Farm-Treehouse/knowledge-base path:content/notes"
})
data = result.get("data", {}).get("data", result.get("data", {}))
items = data.get("items", [])
for item in items:
    print(f"  {item['path']} — {item.get('html_url')}")
```

## Writing Files

### Create or update a single file

Use `GITHUB_CREATE_OR_UPDATE_FILE_CONTENTS`. For updates, you MUST provide the current `sha`.

```python
# Create a new file
result, error = run_composio_tool("GITHUB_CREATE_OR_UPDATE_FILE_CONTENTS", {
    "owner": "Bolt-Farm-Treehouse",
    "repo": "knowledge-base",
    "path": "content/notes/permanent/20260215193000-new-note.md",
    "message": "[atomize] New permanent note: Assertion title here",
    "content": "---\ntype: permanent\ncreated: 2026-02-15T19:30:00\nauthor: claude/keagan\nstatus: draft\ntags: []\nlinks: []\n---\n\n# Assertion title here\n\nNote content...",
    "branch": "main"
})

# Update an existing file (must include sha)
result, error = run_composio_tool("GITHUB_CREATE_OR_UPDATE_FILE_CONTENTS", {
    "owner": "Bolt-Farm-Treehouse",
    "repo": "knowledge-base",
    "path": "content/maps/moc-existing-topic.md",
    "message": "[index] Update MOC with new notes",
    "content": updated_content,
    "sha": existing_file_sha,  # Get this from a prior read
    "branch": "main"
})
```

### Create/update multiple files in one commit

Use `GITHUB_COMMIT_MULTIPLE_FILES` for atomic multi-file commits (e.g., creating a source note + multiple permanent notes + updating a MOC in one commit).

```python
result, error = run_composio_tool("GITHUB_COMMIT_MULTIPLE_FILES", {
    "owner": "Bolt-Farm-Treehouse",
    "repo": "knowledge-base",
    "branch": "main",
    "commit_message": "[atomize] Process document: Meeting Notes Feb 15",
    "files": [
        {
            "path": "content/notes/source/src-meeting-notes-feb-15.md",
            "content": source_note_content
        },
        {
            "path": "content/notes/permanent/20260215193000-first-insight.md",
            "content": permanent_note_1
        },
        {
            "path": "content/notes/permanent/20260215193001-second-insight.md",
            "content": permanent_note_2
        }
    ]
})
```

## Commit Message Conventions

All commits to the vault use a prefix tag:

| Prefix | When to use |
|--------|-------------|
| `[intake]` | New source note preserved |
| `[atomize]` | New permanent notes created from atomization |
| `[link]` | Links added or updated between existing notes |
| `[index]` | MOC created or updated |
| `[review]` | Changes from vault health review |
| `[sweep]` | Changes from daily sweep / ingestion |
| `[system]` | Sweep state or system config updates |
| `[human]` | Human-requested direct changes |
| `[fleeting]` | New fleeting note captured |

## Error Handling

### Common errors and recovery

| Error | Cause | Recovery |
|-------|-------|----------|
| 404 Not Found | File/path doesn't exist | Create it (for writes) or check path spelling |
| 409 Conflict | SHA mismatch (file changed since read) | Re-read the file to get current SHA, then retry |
| 422 Unprocessable | Invalid request (missing fields, bad path) | Check required fields per tool schema |
| 401 Unauthorized | GitHub connection expired | Ask user to reconnect via Composio |

### Retry pattern

```python
import time

def vault_write_with_retry(path, content, message, max_retries=3):
    for attempt in range(max_retries):
        # Read current SHA if file exists
        read_result, read_error = run_composio_tool("GITHUB_GET_REPOSITORY_CONTENT", {
            "owner": "Bolt-Farm-Treehouse",
            "repo": "knowledge-base",
            "path": path,
            "ref": "main"
        })

        sha = None
        if not read_error:
            data = read_result.get("data", {}).get("data", read_result.get("data", {}))
            if isinstance(data, dict):
                sha = data.get("sha") or (data.get("content", {}).get("sha") if isinstance(data.get("content"), dict) else None)

        write_args = {
            "owner": "Bolt-Farm-Treehouse",
            "repo": "knowledge-base",
            "path": path,
            "message": message,
            "content": content,
            "branch": "main"
        }
        if sha:
            write_args["sha"] = sha

        result, error = run_composio_tool("GITHUB_CREATE_OR_UPDATE_FILE_CONTENTS", write_args)
        if not error:
            return result, None
        if "409" in str(error) and attempt < max_retries - 1:
            time.sleep(1)
            continue
        return result, error
    return None, "Max retries exceeded"
```

## Key Principle

**The vault is GitHub. Always.**

No skill should ever try to read or write vault content from the local filesystem. The workspace folder is for Cowork session outputs (artifacts, documents, etc.). The vault is always accessed through the GitHub API tools listed above. This guarantees:

1. Every session sees the latest `main` branch
2. Every write lands on `main` immediately
3. It works regardless of what folder is mounted in Cowork
4. There's a full commit history of every change
5. Quartz rebuilds automatically on every push

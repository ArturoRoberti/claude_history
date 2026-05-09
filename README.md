# claude_history

A CLI tool for browsing and resuming [Claude Code](https://claude.ai/code) conversations.
Reads `~/.claude/history.jsonl` and (when available) the full session transcripts under `~/.claude/projects/`.

## Installation

Download the script somewhere on your `$PATH` and make it executable:

```bash
# sudo apt install wget
wget https://raw.githubusercontent.com/ArturoRoberti/claude_history/main/claude_history -O ~/.local/bin/claude_history  # or any directory on $PATH
chmod +x ~/.local/bin/claude_history
```

## Usage

### List conversations

```
claude_history [options]
```

By default prints each conversation's ID, project directory, and first/last prompt.

| Flag | Description |
|------|-------------|
| `-i`, `--inputs` | Show **all** prompts instead of just first and last |
| `-f`, `--files` | Show files edited/written during the session |
| `-t`, `--tools` | Show tool usage summary (e.g. `Edit×8  Bash×3`) |
| `-b`, `--bash` | Show Bash commands that were run |
| `-a`, `--all` | Implies `-i -f -t -b` |
| `-n N`, `--head N` | Show only the first N conversations |
| `-l N`, `--tail N` | Show only the last N conversations |

### Resume a conversation

```
claude_history resume [-g]
```

Finds the most recent conversation whose project directory matches the current working directory and runs `claude --resume <id>`.

| Flag | Description |
|------|-------------|
| `-g`, `--global` | Ignore the current directory - resume the most recently active conversation across all projects, `cd`-ing into its project directory first |

> **Note:** `--global` changes the working directory of the `claude_history` process before handing off to `claude`. Your shell's working directory is never affected; the `cd` is only visible to the spawned Claude session.

## Examples

```bash
# List all conversations (compact)
claude_history

# Full detail for the 3 most recent conversations
claude_history --all --tail 3

# Resume last conversation in the current repo
claude_history resume

# Resume the globally last conversation (any project)
claude_history resume --global
```

## Data sources

| Source | Contents |
|--------|----------|
| `~/.claude/history.jsonl` | User prompts, timestamps, session IDs, project paths |
| `~/.claude/projects/<slug>/<id>.jsonl` | Full transcript: tool calls, file edits, git branch (only present for recent sessions) |

# License

This project is licensed under the Apache 2.0 License - see the [LICENSE](https://github.com/ArturoRoberti/claude_history/blob/main/LICENSE) file for details.

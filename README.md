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

By default prints conversations whose project directory matches the current working directory. Each conversation shows its ID, git branch, and name (user-set) or summary (AI-generated) when available — both can be passed to `resume` as a target.

| Flag | Description |
|------|-------------|
| `-g`, `--global` | Show conversations from **all** projects, not just the current directory |
| `-p`, `--prompts` | Show **all** prompts instead of just first and last |
| `-f`, `--files` | Show files edited/written during the session |
| `-t`, `--tools` | Show tool usage summary (e.g. `Edit×8  Bash×3`) |
| `-b`, `--bash` | Show Bash commands that were run |
| `-a`, `--all` | Implies `-p -f -t -b` |
| `-n N`, `--head N` | Show only the first N conversations |
| `-l N`, `--tail N` | Show only the last N conversations |

### Filter with grep

```
claude_history [options] grep <grep args>
```

Lists only conversations whose printed output matches `grep <grep args>`; everything after `grep` is passed straight to your system's `grep` (see `claude_history grep --help`). List options (`-a`, `-p`, `-t`, `-l`, ...) must come **before** `grep` and also widen what is searched, e.g. `-p` searches all prompts and `-b` the Bash commands.

- Each conversation is searched separately, line by line (use `-z` for multi-line patterns).
- With `-v`/`--invert-match`, only conversations where **nothing** matches are listed.

### Resume a conversation

```
claude_history [--global] resume [<target>]
```

Finds the most recent conversation matching the current working directory and runs `claude --resume <id>`.

| Argument | Description |
|----------|-------------|
| `-g`, `--global` | Search across all projects (must come **before** `resume`) |
| `<target>` | ID prefix or name/summary substring — or an integer (`1`=oldest, `-1`=most recent, `-3`=third-to-last) |

> **Note:** When `--global` is used, `claude_history` changes its own working directory before handing off to `claude`. Your shell's working directory is never affected.

## Examples

```bash
# List conversations in the current directory
claude_history

# List all conversations across all projects
claude_history --global

# Full detail for the 3 most recent conversations in this directory
claude_history -a --tail 5

# Full detail for the last 3 conversations mentioning "docker" or "ros"
claude_history -a -l 3 grep -e docker -e ros

# Conversations whose prompts never mention "rviz"
claude_history -p grep -vi rviz

# Resume last conversation in the current repo
claude_history resume

# Resume the globally last conversation (any project)
claude_history --global resume

# Resume conversation by ID prefix or name/summary
claude_history resume abc123

# Resume third-to-last conversation
claude_history resume -3
```

## Data sources

| Source | Contents |
|--------|----------|
| `~/.claude/history.jsonl` | User prompts, timestamps, session IDs, project paths |
| `~/.claude/projects/<slug>/<id>.jsonl` | Full transcript: tool calls, file edits, git branch, conversation name/summary (only present for recent sessions) |

# License

This project is licensed under the Apache 2.0 License - see the [LICENSE](https://github.com/ArturoRoberti/claude_history/blob/main/LICENSE) file for details.

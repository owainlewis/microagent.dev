# Micro Agent Specification

**Version**: 1.0.0
**License**: CC0 1.0 (Public Domain)

---

## The Idea

A Micro Agent is a folder where the files ARE the agent.

- `AGENT.md` defines who the agent is and what it can do
- `tools/` contains scripts it can run
- `context/` contains knowledge it can read
- `workspace/` is where it saves files

Point any terminal agent at the folder. The docs are the source of truth.

---

## Why This Works

### Zero Context Drift

Long conversations degrade. The model forgets early instructions. Context windows fill up.

Files don't drift. The agent reads `AGENT.md` fresh every time. The state is in the filesystem, not hidden in conversation history.

### Tool Agnostic

Switch models instantly. Claude today, GPT tomorrow, local LLM next week.

You don't rewrite code to change providers. You paste the folder into a different agent. The files work everywhere.

### Debuggable in English

When it fails, you don't debug stack traces. You read text files.

The plan is visible. The state is visible. Fix it in English and try again.

### Async by Default

Stop for a week. Come back. The `workspace/` contains exactly where you left off.

The files ARE the state. Nothing to reconstruct.

---

## Why Scripts

Scripts are the most powerful tool interface for agents:

- **Any language** — Python, Bash, Node. Whatever works.
- **Any API** — If you can curl it, you can script it.
- **Composable** — Pipe tools together. Chain operations.
- **Debuggable** — Run it yourself. See what the agent sees.
- **No protocol** — No schemas, no servers. Just stdin/stdout.

```bash
# This is a tool:
python tools/get_analytics.py --channel-id UC123

# So is this:
curl -s "https://api.example.com/data" | jq '.items'
```

An agent that can run scripts can do anything you can do from a terminal.

---

## The Tradeoffs

**Manual overhead** — You trigger the agent. You review before it acts. It's not fully autonomous.

**Latency** — Read-plan-act is slower than stream-of-consciousness chat.

For personal productivity, these aren't downsides. You want to be in the loop. You want to review. The "overhead" is actually control.

---

## Specification

### Folder Structure

```
agent-name/
├── AGENT.md          # Required: Identity and instructions
├── tools/            # Required: Scripts the agent runs
├── context/          # Optional: Reference docs, SOPs
├── workspace/        # Optional: Where files go
└── .env.example      # If credentials needed
```

### AGENT.md

Plain markdown. Follows [AGENTS.md](https://agents.md) conventions.

```markdown
# Agent Name

You are a [role]. You help [what you do].

You can use the following tools:

## Tools

### tool_name

Description.

    command --with arguments

## Workspace

Save files to `workspace/`:

- `workspace/projects/` — What goes here
- `workspace/research/` — What goes here

## Workflows

### Task Name

1. Read `context/guide.md`
2. Run tool
3. Save to `workspace/`

## Environment

    export API_KEY=your_key
```

### Context

Reference material the agent reads before doing work:

- Style guides
- SOPs
- Templates
- Examples

The agent reads these. It doesn't modify them.

### Workspace

Where the agent saves files:

- Downloads
- Generated content
- Research notes
- Project files

This is the agent's working state. Persistent across sessions.

### Workflows

Step-by-step procedures. What context to read, what tools to run, where to save output.

Without workflows, agents skip steps. With workflows, procedures are repeatable.

---

## Compatible Agents

Any terminal agent that can read files and run commands:

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [Goose](https://github.com/block/goose)
- [OpenCode](https://opencode.ai)
- [Codex CLI](https://github.com/openai/codex)
- [Aider](https://aider.chat)

Or paste into any chat interface. The files are the agent.

---

## Example

```markdown
# YouTube Agent

You are a YouTube research agent. You help analyze channels, find content patterns, and write scripts.

You can use the following tools:

## Tools

### search_videos

Search YouTube.

    uv run tools/youtube.py search_videos "QUERY" --max 25 --json

### get_channel_videos

Get channel videos with outlier analysis.

    uv run tools/youtube.py get_channel_videos @HANDLE --days 30 --json

### get_transcript

Get video transcript.

    uv run tools/youtube.py get_transcript VIDEO_ID

## Workspace

- `workspace/projects/` — Video projects
- `workspace/research/` — Analysis
- `workspace/transcripts/` — Transcripts

## Workflows

### Research a Topic

1. Search with `search_videos`
2. Analyze channels with `get_channel_videos`
3. Get transcripts of outliers
4. Save to `workspace/research/`

### Write a Script

1. Read `context/script-guide.md`
2. Create `workspace/projects/<n>/`
3. Write script.md

## Environment

    export YOUTUBE_API_KEY=your_key
```

---

## Summary

The agent harness is general-purpose.

The value is in the files:

- **AGENT.md** — Identity and instructions
- **tools/** — Scripts that do things
- **context/** — Knowledge to read
- **workspace/** — State that persists

Docs are the source of truth. Files don't drift. Debug in English. Pick up where you left off.

That's it.

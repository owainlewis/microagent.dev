# Micro Agent Specification

**Version**: 1.0.0
**License**: CC0 1.0 (Public Domain)

---

## The Problem

You want to build an AI agent that manages your YouTube channel. Your options:

1. **n8n / Zapier** — Visual workflow builders. No chat interface. Limited reasoning.
2. **LangChain / CrewAI** — Frameworks. Weeks of plumbing before you touch agent logic.
3. **Custom FastAPI + React** — Build everything from scratch. Months of work.

All of these separate you from the actual value: **the scripts that do things** and **the context that makes the agent useful**.

---

## The Insight

Terminal agents like Claude Code, Goose, OpenCode, and Codex already know how to:

- Read files
- Execute scripts
- Write output
- Have conversations

They're general-purpose agent harnesses. You don't need to build another one.

**What they lack is your specific context and tools.**

---

## The Solution

A Micro Agent is a folder:

```
youtube-agent/
├── AGENT.md          # "You are... You can use these tools..."
├── tools/            # Scripts that do things
├── context/          # SOPs, guides, templates
└── workspace/        # Where the agent saves files
```

Point any terminal agent at it:

```
Read AGENT.md. You are this agent. Help me with my YouTube channel.
```

The harness is general. The value is the folder.

---

## Why Scripts and CLIs

Scripts are the most powerful tool interface for agents:

**Any language** — Python, Bash, Node, Go. Whatever solves the problem.

**Any API** — If you can curl it, you can script it.

**Battle-tested** — Unix tools have 50 years of refinement. `jq`, `grep`, `ffmpeg` just work.

**Composable** — Pipe output between tools. Chain operations.

**Debuggable** — Run the script yourself. See what the agent sees.

**No protocol overhead** — No schemas, no servers, no adapters. Just stdin/stdout.

```bash
# This is a tool:
python tools/get_analytics.py --channel-id UC123 --days 30

# So is this:
curl -s "https://api.youtube.com/..." | jq '.items'

# And this:
ffmpeg -i input.mp4 -vf scale=1280:720 output.mp4
```

An agent that can run scripts can do anything you can do from a terminal.

---

## Specification

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
3. Save output to `workspace/`

## Environment

Required:

    export API_KEY=your_key
```

### Folder Structure

```
agent-name/
├── AGENT.md              # Required
├── tools/                # Required
├── context/              # Optional: reference docs
├── workspace/            # Agent's working directory
└── .env.example          # If credentials needed
```

### That's It

No frontmatter. No config files. No schemas.

Just markdown and scripts.

---

## Compatible Harnesses

Any terminal agent that can read files and run commands:

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [OpenCode](https://opencode.ai)
- [Goose](https://github.com/block/goose)
- [Codex CLI](https://github.com/openai/codex)
- [Aider](https://aider.chat)
- [Claude](https://claude.ai) with computer use

The harness handles conversation, tool execution, and reasoning.

The Micro Agent provides context, tools, and workspace.

---

## Context vs Tools vs Workspace

| Directory | Purpose | Agent Access |
|-----------|---------|--------------|
| `tools/` | Scripts that do things | Execute |
| `context/` | SOPs, guides, templates | Read |
| `workspace/` | Files the agent creates | Read/Write |

**Context** is reference material. The agent reads it before doing work.

**Tools** are capabilities. The agent executes them to take action.

**Workspace** is the agent's desk. Downloads, drafts, output go here.

---

## Workflows

Workflows are step-by-step procedures. They tell the agent what context to read, what tools to use, and where to save output.

```markdown
## Workflows

### Write a Script

1. Read `context/script-guide.md`
2. Read `context/examples/`
3. Write to `workspace/projects/<name>/script.md`
```

Without workflows, agents skip steps. With workflows, procedures are repeatable.

---

## Example

```markdown
# YouTube Agent

You are a YouTube research agent. You help analyze channels, find content patterns, and write video scripts.

You can use the following tools:

## Tools

### search_videos

Search YouTube for videos.

    uv run tools/youtube.py search_videos "QUERY" --max 25 --json

### get_channel_videos

Get videos from a channel with outlier analysis.

    uv run tools/youtube.py get_channel_videos @HANDLE --days 30 --json

### get_transcript

Get video transcript.

    uv run tools/youtube.py get_transcript VIDEO_ID

## Workspace

Save files to `workspace/`:

- `workspace/projects/` — Video projects
- `workspace/research/` — Analysis and notes
- `workspace/transcripts/` — Downloaded transcripts

## Workflows

### Research a Topic

1. Search with `search_videos`
2. Get transcripts of top performers
3. Save findings to `workspace/research/`

### Write a Script

1. Read `context/script-guide.md`
2. Read `context/title-guide.md`
3. Write to `workspace/projects/<name>/script.md`

## Environment

    export YOUTUBE_API_KEY=your_key
```

---

## Distribution

Share as a git repo:

```bash
git clone https://github.com/user/youtube-agent
cd youtube-agent
cp .env.example .env
# Add credentials, start working
```

Agents are portable. Same folder works with any compatible harness.

---

## Relationship to AGENTS.md

[AGENTS.md](https://agents.md) is the standard for giving coding agents instructions. Micro Agents follow the same format.

**AGENTS.md**: Instructions for working in a codebase.

**Micro Agent**: A complete workspace with tools, context, and workflows.

A Micro Agent is what you get when AGENTS.md becomes a standalone operational environment.

---

## Summary

The agent harness is general-purpose.

The value is in the scripts (what it can do) and context (how it does it).

A Micro Agent is just a folder with:

- `AGENT.md` — Identity and instructions
- `tools/` — Scripts that do things
- `context/` — Reference material
- `workspace/` — Where files go

Point a terminal agent at it. Start working.

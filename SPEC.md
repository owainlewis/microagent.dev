# Micro Agent Specification

**Version**: 1.0.0
**License**: CC0 1.0 (Public Domain)

---

## Introduction

A **Micro Agent** is a portable workspace that any AI agent can operate from. It's a folder containing everything an agent needs: identity, tools, context, and a workspace for files.

The format follows the [AGENTS.md](https://agents.md) standard — plain markdown instructions that any coding agent can read. The key addition: tools, workspace, and workflows to make it a complete operational environment.

---

## Quick Start

```
my-agent/
├── AGENT.md              # "You are... You can use these tools..."
├── tools/                # Scripts the agent can run
├── context/              # Reference docs (SOPs, guides, templates)
├── workspace/            # Files the agent creates
└── .env.example          # Credentials template
```

Point any AI agent at the folder:

```
Read AGENT.md. You are this agent. This folder is your workspace.
```

That's it.

---

## AGENT.md Format

Plain markdown. No frontmatter required. Follows AGENTS.md conventions.

```markdown
# Agent Name

You are a [role]. You help [what you do].

You can use the following tools:

## Tools

### tool_name

Description of what it does.

    command to run the tool --with arguments

## Workspace

Save files to `workspace/`:

- `workspace/folder/` — What goes here

## Workflows

### Common Task Name

1. Read `context/relevant-guide.md`
2. Do something
3. Save to `workspace/output/`

## Environment

Required:

    export API_KEY=your_key
```

### Key Sections

| Section | Purpose |
|---------|---------|
| **Header** | "You are... You help..." — Agent identity |
| **Tools** | Scripts the agent can execute |
| **Workspace** | Where to save files |
| **Workflows** | Step-by-step procedures for common tasks |
| **Environment** | Required credentials |

---

## Folder Structure

```
agent-name/
├── AGENT.md              # Required: Agent instructions
├── tools/                # Required: Executable scripts
│   ├── main.py           # Main tool script
│   └── auth/             # Auth helpers (optional)
│       └── setup.py
├── context/              # Optional: Reference documents
│   ├── guide.md
│   └── templates/
├── workspace/            # Auto-created: Agent's working files
│   ├── projects/
│   ├── research/
│   └── output/
├── .env.example          # Required if auth needed
└── README.md             # Optional: Human documentation
```

### Directory Purposes

| Directory | Purpose | Agent Access |
|-----------|---------|--------------|
| `tools/` | Executable scripts | Execute |
| `context/` | Reference docs, SOPs, templates | Read |
| `workspace/` | Agent's working files | Read/Write |

---

## Tools

Tools are scripts in the `tools/` directory. Any language works as long as:

1. It can be run from command line
2. It accepts arguments
3. It outputs to stdout
4. It returns exit codes (0 = success)

### Documenting Tools in AGENT.md

```markdown
### Tool Name

Brief description of what it does and when to use it.

    command to run --required-arg VALUE --optional-arg VALUE

Options:
- `--required-arg` — What this does (required)
- `--optional-arg` — What this does (default: value)
```

### Tool Conventions

Tools should:

- Support `--help` for self-documentation
- Output JSON with `--json` flag when structured data is useful
- Write errors to stderr
- Return non-zero exit codes on failure

Example tool structure:

```python
#!/usr/bin/env python3
"""Brief description."""

import argparse
import json
import os
import sys

def main():
    parser = argparse.ArgumentParser(description=__doc__)
    parser.add_argument('--channel', required=True, help='Channel to analyze')
    parser.add_argument('--json', action='store_true', help='Output JSON')
    args = parser.parse_args()
    
    api_key = os.environ.get('API_KEY')
    if not api_key:
        print("Error: API_KEY not set", file=sys.stderr)
        sys.exit(1)
    
    result = do_work(args.channel)
    
    if args.json:
        print(json.dumps(result, indent=2))
    else:
        print(format_result(result))

if __name__ == '__main__':
    main()
```

---

## Context

The `context/` directory contains reference material the agent reads before doing work:

- Style guides
- SOPs and procedures
- Templates
- Examples
- Domain knowledge

Context files are read-only reference material. The agent reads them but doesn't modify them.

### Organizing Context

```
context/
├── script-guide.md       # How to write scripts
├── style-guide.md        # Voice and tone
├── templates/
│   └── metadata.md       # Template for video metadata
└── examples/
    ├── good-script.md
    └── good-title.md
```

### Referencing Context in Workflows

```markdown
## Workflows

### Write a Script

1. Read `context/script-guide.md` for structure
2. Read `context/examples/good-script.md` for reference
3. Write script to `workspace/projects/<name>/script.md`
```

---

## Workspace

The `workspace/` directory is where the agent saves files it creates:

- Downloads
- Generated content
- Research notes
- Project files

### Organizing Workspace

```markdown
## Workspace

Save files to `workspace/`:

- `workspace/projects/` — Project folders (scripts, notes, metadata)
- `workspace/research/` — Research and analysis
- `workspace/downloads/` — Downloaded files
- `workspace/output/` — Final deliverables
```

### Workspace in .gitignore

```gitignore
workspace/*
!workspace/.gitkeep
```

---

## Workflows

Workflows are step-by-step procedures for common tasks. They tell the agent:

1. What context to read first
2. What tools to use
3. Where to save output

### Writing Workflows

```markdown
## Workflows

### Task Name

1. Read `context/relevant-guide.md`
2. Run `tool_name` with appropriate arguments
3. Save result to `workspace/folder/filename.md`

### Another Task

1. First step
2. Second step
3. Third step
```

### Why Workflows Matter

Without workflows, the agent might:
- Skip reading the style guide before writing
- Save files to wrong locations
- Miss important steps

Workflows make procedures explicit and repeatable.

---

## Authentication

### .env.example

Template for required credentials:

```bash
# API Credentials
# Get from: https://example.com/api
API_KEY=

# OAuth (run tools/auth/setup.py to generate)
CLIENT_SECRET=
REFRESH_TOKEN=
```

### Auth Setup Scripts

For complex auth (OAuth), include setup helpers:

```
tools/auth/
├── setup.py          # Interactive first-time setup
└── README.md         # Auth instructions
```

### Documenting in AGENT.md

```markdown
## Environment

Required:

    export API_KEY=your_api_key
    export CLIENT_SECRETS=/path/to/secrets.json

Run `python tools/auth/setup.py` for first-time OAuth setup.
```

---

## Compatibility

A Micro Agent works with any system that can:

1. Read markdown files
2. Execute shell commands
3. Read environment variables

### Compatible Harnesses

- Claude (claude.ai, Claude Code)
- Cursor
- Windsurf
- OpenAI Codex
- GPT + Code Interpreter
- Local LLMs with shell access
- Custom harnesses

### Usage

```
Open this folder. Read AGENT.md — you are this agent.
Use the tools described there to help me.
```

---

## Distribution

### As Git Repository

```bash
git clone https://github.com/user/my-agent
cd my-agent
cp .env.example .env
# Add credentials to .env
```

### .gitignore

```gitignore
.env
workspace/*
!workspace/.gitkeep
__pycache__/
*.pyc
```

---

## Relationship to AGENTS.md

This spec is compatible with [AGENTS.md](https://agents.md) — the open format for guiding coding agents.

**AGENTS.md**: Instructions for agents working in a codebase (build commands, code style, testing).

**Micro Agent**: A complete workspace with tools, context, and workflows. Uses AGENTS.md conventions for the instruction format.

A Micro Agent is what you get when AGENTS.md meets a self-contained operational workspace.

---

## Relationship to Agent Skills

[Agent Skills](https://agentskills.io) are capability packages — instructions and scripts that extend an agent.

**Agent Skills**: Stateless plugins activated on demand.

**Micro Agent**: Complete workspace with persistent state.

Skills can live inside a Micro Agent's `context/` or as separate capability modules.

---

## Example Structure

```
youtube-agent/
├── AGENT.md
├── tools/
│   ├── youtube.py
│   └── auth/
│       ├── setup.py
│       └── README.md
├── context/
│   ├── script-guide.md
│   ├── title-guide.md
│   └── templates/
│       └── metadata.md
├── workspace/
│   ├── projects/
│   ├── research/
│   └── transcripts/
├── .env.example
├── .gitignore
└── README.md
```

---

## Checklist

Minimum viable Micro Agent:

- [ ] `AGENT.md` with "You are..." and tools section
- [ ] `tools/` with at least one executable script
- [ ] `.env.example` if credentials needed

Complete Micro Agent:

- [ ] `AGENT.md` with tools, workspace, workflows
- [ ] `tools/` with documented scripts
- [ ] `context/` with guides and templates
- [ ] `workspace/` directory structure
- [ ] `.env.example` with all required credentials
- [ ] `README.md` for human setup instructions

---

## License

This specification is released under **CC0 1.0 Universal (Public Domain)**.

---

## Contributing

GitHub: [github.com/microagent/spec](https://github.com/microagent/spec)

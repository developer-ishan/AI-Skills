# AI Skills

A collection of open-source **AI agent skills** that give Claude Code and Cursor superpowers for domain-specific tasks.

## What is a Skill?

A **Skill** is a `SKILL.md` file that teaches an AI agent *how* to perform a specialized task. It bundles:

- **Domain expertise** — the knowledge a human expert would bring (e.g. how to classify Indian equity holdings by asset class, how to compute portfolio health scores).
- **Tool workflows** — step-by-step instructions for calling MCP servers, APIs, or CLI tools in the right order.
- **Output templates** — structured formats so the agent produces consistent, high-quality results every time.

Think of it as a runbook that turns a general-purpose AI into a domain specialist.

## Available Skills

| Skill | Description | Platforms |
|-------|-------------|-----------|
| [Portfolio Analysis](portfolio-analysis/) | AI-powered wealth management using live Zerodha (Kite) data — portfolio overview, stock deep-dives, sector analysis, mutual fund review, and more. | Claude Code, Cursor |

## Setup

### Claude Code

1. **Copy the skill folder** into your Claude Code skills directory:

```bash
# Global (available in all projects)
cp -r <skill>/claude-code/ ~/.claude/skills/<skill-name>/

# Example
cp -r portfolio-analysis/claude-code/ ~/.claude/skills/portfolio-analysis/
```

2. **Configure any required MCP servers** in your project's `.mcp.json` or globally in `~/.claude/mcp.json`. Each skill's README lists its MCP dependencies.

3. **Start using it** — Claude Code automatically discovers skills in `~/.claude/skills/` and activates them when relevant.

> See the [Claude Code Skills documentation](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/skills) for more details.

### Cursor

1. **Copy the skill folder** into your Cursor skills directory:

```bash
# Global (available in all projects)
cp -r <skill>/cursor/ ~/.cursor/skills/<skill-name>/

# Workspace-level (available only in this project)
cp -r <skill>/cursor/ .cursor/skills/<skill-name>/

# Example
cp -r portfolio-analysis/cursor/ ~/.cursor/skills/portfolio-analysis/
```

2. **Configure any required MCP servers** in `.cursor/mcp.json` (workspace) or Cursor Settings > MCP. Each skill's README lists its MCP dependencies.

3. **Start using it** — Cursor automatically discovers skills and activates them when the agent detects a matching request.

> See the [Cursor Skills documentation](https://docs.cursor.com/agent/skills) for more details.

## Repository Structure

```
AI-Skills/
├── README.md                        # This file
├── portfolio-analysis/
│   ├── README.md                    # Skill documentation and setup guide
│   ├── claude-code/                 # Claude Code compatible skill
│   │   ├── SKILL.md
│   │   ├── templates.md
│   │   └── reference.md
│   └── cursor/                      # Cursor compatible skill
│       ├── SKILL.md
│       ├── templates.md
│       └── reference.md
└── ...                              # More skills coming soon
```

Each skill contains **self-contained** subfolders per platform — copy one folder and you have everything you need. No cross-folder dependencies.

## Contributing

Have a skill you'd like to share? Contributions are welcome.

1. Create a folder under the repo root with your skill name (e.g. `code-review/`).
2. Add platform subfolders (`claude-code/`, `cursor/`) each containing a `SKILL.md`.
3. Write a `README.md` in your skill folder explaining what it does, prerequisites, and setup.
4. Submit a pull request.

### Skill Authoring Tips

- Keep `SKILL.md` focused — one skill, one domain.
- Include **trigger phrases** so the agent knows when to activate (e.g. "analyze my portfolio", "review this PR").
- Add **output templates** for consistent results.
- Reference MCP tools by their exact names and document required arguments.
- Test your skill end-to-end before submitting.

## License

MIT

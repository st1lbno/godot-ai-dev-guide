# Godot AI Dev Guide

Rules and best practices for AI-assisted Godot game development. This is a reference that Claude Code (and other AI coding agents) should follow when working on any Godot project.

## Who This Is For

- **AI coding agents** (Claude Code, Cursor, Copilot, etc.) that are writing or modifying Godot projects
- **Developers** who pair with AI agents and want a shared set of conventions
- **CI systems** that need to validate Godot projects headlessly

## How to Use It

### For AI Agents

Reference `RULES.md` in your project-level instructions (e.g., `.claude/CLAUDE.md`):

```
See https://github.com/ethan-yz-hao/godot-ai-dev-guide/blob/main/RULES.md for Godot development rules.
```

Or copy the relevant sections directly into your project's AI configuration.

### For Developers

Read through `RULES.md` to understand the conventions your AI agent will follow. Override or extend any section in your project-level config as needed.

## Contents

- **[RULES.md](RULES.md)** — The core rules, organized by topic: project setup, development workflow, testing, MCP bridge usage, art pipeline, common gotchas, and analytics/publishing integration.

## Contributing

Open an issue or PR if you have corrections, additions, or hard-won lessons from your own AI + Godot workflow.

## License

MIT

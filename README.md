# Darak MCP Skill

A Claude Code skill for the [Darak](https://darak.app) MCP server. Helps AI assistants research Saudi Arabian property markets using Darak's 16 read-only tools.

## What it does

Guides Claude through multi-tool workflows for common real estate tasks:

- **Apartment hunting** — chaining search, market stats, and comparables
- **Price evaluation** — percentile ranking and comparable analysis
- **Neighborhood comparison** — side-by-side metrics with price trends
- **Market analysis** — city overviews and price distributions
- **Best deals** — listings priced below neighborhood medians

Also provides Saudi real estate domain context (typical price ranges, sort strategies, presentation patterns) and documents critical gotchas like the neighborhood name lookup requirement.

## Install

### Claude Code (CLI)

```bash
claude skill add --url https://github.com/basseko/darak-mcp-skill
```

### Manual

Copy `SKILL.md` to `~/.claude/skills/darak-saudi-real-estate/SKILL.md`.

## Requires

The [Darak MCP server](https://github.com/basseko/darak-mcp-server) must be connected:

```bash
claude mcp add --transport http darak https://mcp.darak.app/mcp
```

## License

MIT

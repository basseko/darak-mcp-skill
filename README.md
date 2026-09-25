# Darak MCP Skill

Optional Claude Code guidance for the [Darak](https://platform.darak.app/docs/guides/mcp) MCP server. Helps AI assistants research Saudi Arabian property markets using Darak's 23 read-only tools. It does not install the MCP connection or supply credentials.

## What it does

Guides Claude through multi-tool workflows for common real estate tasks:

- **Apartment hunting** — chaining search, market stats, and comparables
- **Price evaluation** — percentile ranking and comparable analysis
- **Neighborhood comparison** — side-by-side metrics with price trends
- **Market analysis** — city overviews and price distributions
- **Best deals** — listings priced below neighborhood medians

Also provides Saudi real estate domain context (typical price ranges, sort strategies, presentation patterns) and documents critical gotchas like the neighborhood name lookup requirement.

## Install

Copy [SKILL.md](SKILL.md) to `~/.claude/skills/darak-saudi-real-estate/SKILL.md`:

```bash
git clone https://github.com/basseko/darak-mcp-skill.git
mkdir -p ~/.claude/skills/darak-saudi-real-estate
cp darak-mcp-skill/SKILL.md ~/.claude/skills/darak-saudi-real-estate/SKILL.md
```

The skill is optional. Connect the MCP server separately as shown below.

## Requires

The [Darak MCP server](https://github.com/basseko/darak-mcp-server) must be connected:

```bash
claude mcp add --transport http darak https://platform.darak.app/mcp
```

The authorization-server issuer remains `https://darak.app/api/auth`. Connected
use may prompt you to sign in and consent to read-only `darak.read` access;
limited anonymous use is available. The old `https://darak.app/mcp` endpoint
is retired. See the [MCP connection guide](https://platform.darak.app/docs/guides/mcp)
for troubleshooting.

## License

MIT

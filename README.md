# darak-saudi-real-estate

An Agent Skill for researching Saudi property with [Darak](https://darak.app)'s MCP server.

It adds what the server's tool descriptions can't: what the data is (asking prices, rent per year), what it isn't (sales, safety, forecasts), which tool fits a question, and how to present verdicts and caveats.

## Install

Connect the server first:

```bash
claude mcp add --transport http darak https://mcp.darak.app/mcp
```

Then install the skill from [basseko/darak-mcp-skill](https://github.com/basseko/darak-mcp-skill), or copy `SKILL.md` to `~/.claude/skills/darak-saudi-real-estate/SKILL.md`.

## Source and evals

The source lives in the Darak monorepo at `apps/mcp/skill/`, next to the server and its evals (`apps/mcp/evals/skill/`); `basseko/darak-mcp-skill` is the published copy. Change the skill only with an eval run before and after (see `apps/mcp/evals/skill/RESULTS.md`).

## License

MIT

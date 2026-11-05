# darak-saudi-real-estate

An Agent Skill for researching Saudi property with the MCP server from [Kite](https://kite.sa) (formerly Darak).

It adds what the server's tool descriptions can't: what the data is (asking prices, rent per year), what it isn't (sales, safety, forecasts), which tool fits a question, and how to present verdicts and caveats.

## Install

Connect the server first:

```bash
claude mcp add --transport http darak https://mcp.kite.sa/mcp
```

The server keeps the `darak` name in this command because the skill refers to it by that name. The older `https://mcp.darak.app/mcp` address still works. Server documentation: [platform.kite.sa/docs/mcp](https://platform.kite.sa/docs/mcp).

Then install the skill from [basseko/darak-mcp-skill](https://github.com/basseko/darak-mcp-skill), or copy `SKILL.md` to `~/.claude/skills/darak-saudi-real-estate/SKILL.md`.

## Source and evals

The source lives in Kite's main codebase at `apps/mcp/skill/`, next to the server and its evals (`apps/mcp/evals/skill/`); `basseko/darak-mcp-skill` is the published copy. Change the skill only with an eval run before and after (see `apps/mcp/evals/skill/RESULTS.md`).

## License

MIT

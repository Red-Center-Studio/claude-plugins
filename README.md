# Red Center Studio Claude plugins

A plugin marketplace for Claude Code and Claude with three plugins:

| Plugin | What it does | Cost |
|---|---|---|
| `website-checks` | GEO audit (AI search readiness), single point of failure DNS scan and Bubble® error decoder, as MCP tools | Free, no key |
| `webdesk` | An agent builds a complete one-page small business website from a short brief and returns a ZIP the business owns | Prepaid credits from $29 USD per website |
| `nocode-migration-audit` | Read-only audit of a no-code app before porting it to code: a neutral app spec and a plain-language report | Free, MIT |

## Install in Claude Code

```
/plugin marketplace add Red-Center-Studio/claude-plugins
/plugin install website-checks@red-center-studio
/plugin install webdesk@red-center-studio
/plugin install nocode-migration-audit@red-center-studio
```

`webdesk` asks for your WebDesk API key when you install it and stores it securely. Create one free at https://redcenterstudio.com/webdesk/agents/.

## Use the free checks as a connector

The free tools server also works as a custom connector in Claude and in any MCP client, with no key:

```
https://redcenterstudio.com/api/tools/mcp
```

In Claude: Settings, Connectors, Add custom connector, then paste the URL.

## Links

- Free tools in the browser: https://redcenterstudio.com/tools/
- WebDesk for AI agents: https://redcenterstudio.com/webdesk/agents/
- MCP server docs: https://redcenterstudio.com/webdesk/agents/docs/
- Studio: https://redcenterstudio.com

Bubble is a registered trademark of Bubble Group, Inc. Red Center Studio is not affiliated with, endorsed by or certified by Bubble or Anthropic.

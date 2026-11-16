# Red Center Studio Claude plugins

A plugin marketplace for Claude Code and Claude with six plugins:

| Plugin | What it does | Cost |
|---|---|---|
| `website-checks` | GEO audit (AI search readiness), domain health, AI crawler access, single point of failure DNS scan and Bubble® error decoder, as MCP tools | Free, no key |
| `webdesk-claude` | Build, preview and publish a small business website from Claude, with a quote form and an enquiry inbox | Free plan, paid plans optional |
| `site-watch` | Website monitoring: domain and certificate expiry, DNS and email records (SPF, DMARC, DKIM), AI crawler access and uptime, with plain alerts and fixes | Free plan, paid plans optional |
| `vaultsheet` | Fill security questionnaires and tender spreadsheets (.xlsx, .xlsm) from answers your team approved, copied word for word, with gaps flagged | Free plan, paid plans optional |
| `webdesk` | An agent builds a complete one-page small business website from a short brief and returns a ZIP the business owns | Prepaid credits from $29 USD per website |
| `nocode-migration-audit` | Read-only audit of a no-code app before porting it to code: a neutral app spec and a plain-language report | Free, MIT |

## Install in Claude Code

```
/plugin marketplace add Red-Center-Studio/claude-plugins
/plugin install website-checks@red-center-studio
/plugin install webdesk-claude@red-center-studio
/plugin install site-watch@red-center-studio
/plugin install vaultsheet@red-center-studio
/plugin install webdesk@red-center-studio
/plugin install nocode-migration-audit@red-center-studio
```

`webdesk-claude`, `site-watch` and `vaultsheet` connect to remote MCP servers that ask you to sign in with your email address and a 6-digit code the first time you use them (run `/mcp` in Claude Code to sign in). `webdesk` asks for your WebDesk API key when you install it and stores it securely. Create one free at https://redcenterstudio.com/webdesk/agents/.

## Use them as connectors in Claude

Every server here also works as a custom connector in Claude (Settings, Connectors, Add custom connector, then paste the URL):

| Connector | URL |
|---|---|
| Free website checks (no sign-in) | `https://redcenterstudio.com/api/tools/mcp` |
| WebDesk for Claude | `https://mcp.redcenterstudio.com/mcp` |
| Site Watch | `https://mcp.redcenterstudio.com/sitewatch/mcp` |
| VaultSheet | `https://mcp.redcenterstudio.com/vaultsheet/mcp` |

Setup steps for each connector are in https://github.com/Red-Center-Studio/claude-connectors.

## Links

- All connectors: https://redcenterstudio.com/claude-connectors/
- Free tools in the browser: https://redcenterstudio.com/tools/
- WebDesk for AI agents: https://redcenterstudio.com/webdesk/agents/
- MCP server docs: https://redcenterstudio.com/webdesk/agents/docs/
- Privacy: https://redcenterstudio.com/privacy/
- Studio: https://redcenterstudio.com

Bubble is a registered trademark of Bubble Group, Inc. Red Center Studio is not affiliated with, endorsed by or certified by Bubble or Anthropic.

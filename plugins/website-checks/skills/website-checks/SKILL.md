---
name: website-checks
description: Check a public website or domain with the free Red Center Studio tools. Use when the user asks whether a site is ready for AI search or AI assistants (GEO, llms.txt, AI crawlers in robots.txt, structured data, sitemap), which companies a business depends on for email, DNS, sign-in and hosting and what one outage would stop, or what a Bubble app error message means and how to fix it.
---

# Website checks

Three read-only tools on the `rcs-tools` MCP server. No API key. They read public data only and never change anything.

| Tool | Input | Use it for |
|---|---|---|
| `geo_audit` | `url` | A score out of 100 for AI search readiness: AI crawler access in robots.txt, sitemap, llms.txt, structured data, titles. Each check passes, warns or fails, with copy-paste fixes. |
| `spof_scan` | `domain` | Which vendors run the domain's DNS, email, sign-in, hosting and outbound email, a risk score, and what a single outage would stop. Public DNS only. |
| `decode_error` | `error` | Up to five matching Bubble® error explanations, each with the likely cause and the fix. |

## How to work

1. Ask for the address if the user has not given one. Pass a bare domain to `spof_scan` (example.com) and a domain or URL to `geo_audit`.
2. Run the tool and lead with the result: the score or the main risk, then the two or three fixes that matter most, in plain language.
3. Quote fixes exactly as the tool returns them when they are copy-paste snippets (robots.txt lines, JSON-LD, llms.txt).
4. The tools are rate limited per connection. If a call is refused for rate limiting, say so and suggest trying again later rather than retrying in a loop.
5. Only check sites the user owns or is allowed to assess.

The same checks run in a browser at https://redcenterstudio.com/tools/. Bubble is a registered trademark of Bubble Group, Inc.; Red Center Studio is not affiliated with Bubble.

---
name: audit-bubble
description: Read-only readiness audit and migration plan for a Bubble® app before moving data or rebuilding it on Cloudflare. Use when the owner wants to assess complexity, inspect D1 schema mapping, or get an App Mover score and quote.
---

# Audit a Bubble® app for Cloudflare migration

Use this skill when the owner asks to audit a Bubble® app, estimate migration effort, generate a D1 schema plan, or score a `.bubble` export for a full move to Cloudflare Workers, D1, and R2.

## Guardrails

- Never ask the owner to paste API keys, passwords, or Data API tokens into chat. Always use `nc_connect` so credentials go on the 30-minute one-time secure page.
- Never apply `skip_types`, `skip_fields`, or `primitive_lists` without presenting the full inventory first and getting the owner's agreement.
- Make sure the workflow stays strictly read-only on the source app.
- Use `nc_ping` if reachability is unsure, and `nc_account` to explain pass tiers, free trial limits (up to 1,000 records and 250 MB of files once per app), or current credit balance.

## Workflow A: Data & schema audit (`nc_audit` -> `nc_plan`)

1. **Gather structural input (prefer sealed upload or `app-spec.json`)**:
   - If the owner already ran the `nocode-migration-audit` plugin, use its `app-spec.json` (spec `0.1`). This includes privacy rules and workflow counts needed later by `nc_access`.
   - If the owner has the Bubble MCP connected and no `app-spec.json`, `list_types` provides the fallback `editor_catalog`.
   - Preferred flow: call `nc_connect` with `app_base_url` (`https://yourapp.bubbleapps.io`, a custom domain, or a Bubble Editor URL such as `https://bubble.io/page?id=yourapp`) and `version` (`"live"` or `"test"`; automatically inferred if URL includes `version=test` or `/version-test`) first so the owner opens the secure link to enter their Bubble Data API token (for record counts) and upload their `.bubble` export (50 MB+ files up to 500 MB stream with secrets stripped and auto-derive `editor_catalog` and `privacy_rules`) or catalog JSON. Keep the returned `job_id`.
2. **Run `nc_audit`**:
   - Call `nc_audit` with `source: "bubble"`, `app_base_url`, `version`, and either `job_id`, `app_spec`, or `editor_catalog`.
   - `nc_audit` returns an inventory of data types, fields, relations, option sets, record counts (if a Data API token was sealed), unexposed Data API types, and migration risks.
3. **Run `nc_plan`**:
   - Call `nc_plan` with `job_id` to build the D1 mapping plan (wide types split into extension tables, lists mapped to join tables or primitive JSON, SQL preview, slim-down suggestions, and pass tier).
   - Present the inventory, slim-down candidates (empty tables, logs, chat history), estimated record/file volume, and recommended pass tier in plain language.
   - Ask the owner which types or fields (if any) to skip or inline (`skip_types`, `skip_fields`, `primitive_lists`), then re-run `nc_plan` with those selections only after the owner confirms.

## Workflow B: Full App Mover readiness score (`.bubble` export)

When the owner wants to move UI pages and workflows as code in addition to data (requires a Bubble Starter plan or higher to export the `.bubble` file from Bubble Settings -> General):

1. Call `nc_connect` with `purpose: "export"` (and existing `job_id`, or `app_base_url` + `version` for a new job).
2. Instruct the owner to upload their `.bubble` export file on the returned secure link (`settings.secure` secrets are stripped in transit before storage, never saved or logged).
3. Once uploaded, call `nc_compile_plan` with `job_id` (`detail: "full"` when page-by-page breakdown is needed) to produce:
   - Overall move readiness score (`0-100`) and band (`automatic`, `guided`, or `manual_heavy`),
   - Per-page and reusable readiness breakdown,
   - Automatic replacements vs manual items (HTML blocks, unsupported plugins, complex rules),
   - Re-enterable secret names stripped during upload,
   - Tier quote (Small up to 10 pages `$129 USD`, Standard up to 30 `$249 USD`, Large up to 80 `$289 USD`, or Developer mode `$45 USD`; includes a free 3-page preview).

## Reporting back

Summarize:
- Total data types, unexposed types, record count (or note if Data API token was omitted), and recommended data pass or App Mover tier.
- Any high-risk items (missing `app_spec` for privacy rules, wide tables, large lists, manual plugins/expressions).
- The exact next action: either adjust `nc_plan` skip lists, start the free data trial / paid pass via `migrate-data`, or explore a free 3-page preview via `rebuild-worker`.

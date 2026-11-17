---
name: migrate-data
description: Guided end-to-end data, file, user identity, privacy rule, and redirect migration from a Bubble® app into a customer-owned Cloudflare D1 database and R2 bucket using nc_ tools.
---

# Migrate Bubble® data, files, and users to Cloudflare

Run this skill after auditing and agreeing on the mapping plan (`audit-bubble`). Follow the exact stage order below and enforce every consent and credential rule.

## Non-negotiable rules

1. **Credentials never enter chat**: Use `nc_connect` for every token (Cloudflare API token, R2 Access Key ID / Secret Access Key, Bubble Data API token). Never ask the owner to paste credentials in chat.
2. **Explicit consent before spending**: Paid stages (`nc_schema`, `nc_export`, `nc_load`, `nc_files`, `nc_users`, `nc_access`, `nc_redirects`, `nc_verify`, `nc_cutover`) return a cost or pass estimate first unless covered by an active pass or free trial (up to 1,000 records and 250 MB of files once per app). Always show the estimate and get explicit owner approval before calling with `go_ahead: true` (or `upgrade: true` if upgrading tiers).
3. **Overwrite safety**: Never pass `truncate_first: true` to `nc_load` without explicit owner confirmation AND `confirm_overwrite: true`.
4. **No password migration**: Tell the owner upfront that Bubble does not export password hashes, so `nc_users` prepares login identities (`magic_link` or `reset_email`) without sending emails.
5. **Owner controls DNS**: `nc_cutover` never modifies DNS or the source app. Keep rollback steps visible throughout and call `nc_cutover(complete: true, confirm_cutover: true)` only after live cutover is verified.

## Stage order

1. **Connect (`nc_connect`)**:
   - Call `nc_connect` with `job_id` (or `app_base_url` + `version`) so the owner enters Cloudflare account ID, Cloudflare API token (`Account` permissions including `D1: Edit` and `Workers R2 Storage: Edit`; add `Workers Scripts: Edit` and `Queues: Edit` if running full `nc_move`), D1 database name, R2 bucket name, R2 S3 keys, and Bubble Data API token.
   - Optionally call `nc_account` to confirm free trial eligibility, active passes, or credit balance.
2. **Schema (`nc_schema`)**:
   - Generate SQL migrations and Drizzle schema under `nc/<job>/schema/` in R2.
   - Use `apply: true` (with `database_name` if specified) to create the D1 database and apply migrations. Idempotent and safe to re-run.
3. **Export (`nc_export`)**:
   - Pages through source records (read-only) into NDJSON shards in the owner's R2 bucket.
   - Resumable and bounded: keep calling `nc_export` with `job_id` while `status` is `"partial"` until complete. Use `since` later for final delta syncs.
4. **Load (`nc_load`)**:
   - Upserts staged NDJSON records into D1 by `id` (second run changes zero rows; broken references are nulled and logged as orphans, never dropped silently). Keep calling while `status` is `"partial"`.
   - Only pass `truncate_first: true` + `confirm_overwrite: true` if the owner explicitly instructs a clean reload.
5. **Files (`nc_files`)**:
   - Copies referenced files and images from source storage into R2 by content hash, records mappings in `file_map`, and populates file key columns while preserving legacy URLs.
   - Keep calling while `status` is `"partial"`. Pass `allow_hosts` if assets live on approved external CDNs, or `rewrite_text: true` only if the owner wants embedded links in text columns updated (originals are backed up).
6. **Users (`nc_users`)**:
   - Builds migrated login identity tables and recipient CSV/JSON in R2 using `strategy: "magic_link"` or `"reset_email"`. Passwords are not migrated; `nc_users` sends zero emails.
7. **Access (`nc_access`)**:
   - Translates privacy rules from `app_spec` (saved during `nc_audit` or passed here) into `policy.ts` (default deny) and generated allow/deny tests. Any rule marked `MANUAL` stays locked down and must be reviewed in code before launch.
8. **Redirects (`nc_redirects`)**:
   - Builds `redirects.json`, `redirects.ts`, `_redirects`, and a verification curl script from `sitemap_url` or `url_list` plus `page_routes` (for example `{ "listing_detail": "/rooms/:slug" }`). Unmapped URLs are flagged for manual mapping rather than guessed.
9. **Verify (`nc_verify`)**:
   - Checks source-vs-target row counts, canonical JSON SHA-256 checksums (`sample` default 500 per type), foreign keys, and R2 object existence, writing `verification.md` to R2. Loop while `status` is `"partial"`.
   - `nc_cutover` refuses to proceed unless the latest `nc_verify` passed with no subsequent mutations.
10. **Cutover (`nc_cutover`)**:
    - Call `nc_cutover` with `job_id` and `domain` to get the exact cutover checklist: freeze writes on Bubble, run final delta (`nc_export` with `since` -> `nc_load` -> `nc_files` -> `nc_users` -> `nc_verify`), deploy redirects, switch DNS in Cloudflare, notify users, monitor metrics, and keep the rollback procedure ready.
    - Once the owner confirms cutover succeeded and rollback holds, call `nc_cutover` with `complete: true` and `confirm_cutover: true` to record completion and wipe held credentials.

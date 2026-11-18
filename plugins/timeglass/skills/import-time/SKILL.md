---
name: import-time
description: Import historical time entries into Timeglass from a CSV, spreadsheet export, or connected time-tracking/project-management MCP. Use for migrating Toggl, Harvest, Clockify or other recorded time, reviewing an import plan, resuming a batch, or undoing a specified import.
---

# Import historical time

Use Timeglass's native CSV import pipeline, not a loop of manual activity creations. Keep the source read-only. Import recorded time only: do not turn task estimates, project budgets, or calendar event lengths into logged hours.

## Establish access and prepare the file

1. Resolve the destination workspace with `query_workspace_directory` and select it with `set_workspace` if necessary. Inspect current schemas for `create_time_entry_import`, `preview_time_entry_import`, `apply_time_entry_import`, `get_time_entry_import_status`, and `undo_time_entry_import`. This workflow requires workspace-admin access and an editable workspace. If unavailable, explain the missing capability/access; do not bypass it with another user's identity or bulk manual entries.
2. Reuse the requested source, people, date interval, and timezone. Ask only for missing details that affect the import. Discover the named source MCP's read/export tools if connected. Follow pagination and note access limits. If the source MCP cannot return actual time entries, use a CSV export supplied by the user instead of inventing an export capability. Export spreadsheets to CSV first.
3. For MCP-returned entries, build a properly quoted UTF-8 CSV locally from only the selected records. Retain source IDs for reconciliation in the local working data; do not claim Timeglass stores an external time-entry ID or guarantees cross-batch deduplication. Preserve descriptions, people, local dates, units and project/client relationships. Do not alter or delete source entries.
4. Identify date format, duration units/decimal separator and IANA timezone from explicit source evidence or the user. Ambiguous dates such as 03/04 need a format choice. Resolve mixed timezones before using a mapping with one timezone; use separate files/batches when needed. Keep durations unchanged unless rounding was requested. Do not silently drop malformed, negative, or oversized rows; show rejected-row counts/reasons in the preview.

## Upload through the import tool

Call `create_time_entry_import(fileName=...)`, with `fileSizeBytes` when known. Retain the returned `importId`, workspace and file identity. This creates an import record, not timesheet entries. Upload only that selected CSV to the tool-returned one-time URL with the exact method and headers. Treat the URL as temporary authentication data; do not log it, include it in reports, or transmit it elsewhere. Do not paste the CSV contents into tool arguments. Use a supported file-upload/HTTP capability, or give the returned `browserUrl` to the user to upload in Timeglass if direct upload is unavailable. Do not claim the upload succeeded until it did.

A strict request to inspect without any server-side state changes must stay local: create and preview tools save import state. For a normal import preview, explain that staging saves the uploaded file/plan but does not create timesheet entries. Do not apply it.

## Review the saved preview

Call `preview_time_entry_import(importId=...)` after successful upload. Review the proposed mapping, totals by person/project/month, dates, warnings, rejected/skipped rows, sample checks, AI-suggested matches and any proposed creations. Use `query_workspace_directory(entity="users")` for people and `query_work_records` for existing project/client/category IDs. Page through relevant results and distinguish team summaries from actual records.

Preview responses can truncate match lists or grouped totals. An `omitted` count is not a reviewed match: use the retained `browserUrl` for the complete import review, or a supported way to inspect/narrow the full plan before applying. Do not invent preview pagination, silently accept unseen AI matches, or claim a compact response covers every distinct value. If no rows are accepted, fix the mapping/matches and preview again rather than applying an empty plan.

Use mapping overrides from the current schema. Duration formats include `hours_decimal`, `minutes`, `seconds`, `h_mm`, `h_mm_ss`, and `h_m_text`; do not interpret decimal hours as HH:MM. Import `options.dateFrom` and `dateTo` are inclusive local dates, unlike the exclusive end boundary of `query_work_records`. Keep `skipDaysWithExistingTime=true` and rounding at zero unless the user explicitly requests another choice. This overlap safeguard skips whole person-days with existing time; it is not an exact per-entry duplicate check and can exclude legitimate additional work. Explain affected counts rather than disabling it silently.

Set `matches` using each exact distinct key returned in the preview; do not reconstruct composite project/client keys. Resolve ambiguous or AI-suggested people and assignment matches with the user before applying. People map to existing workspace members or explicit skips; do not invite/create users. Do not silently skip unmatched people or assign their time to the caller.

Default to matching existing projects and clients. Use [Import projects](../import-projects/SKILL.md) and [Import clients](../import-clients/SKILL.md) for separately authorized setup. `createMissing=true` marks missing entities for creation when applied: it does not create them at preview time. Native import-created projects are workspace-wide; a new-client mapping may restore an archived duplicate. Do not enable those behaviors unless authorized. If the user wants team-specific projects, create/map them through the project workflow instead. Re-preview after changes to matches, options, or mapping and show the final plan. Do not use `remap=true` to discard reviewed choices unless requested.

## Apply, resume and verify

Show the final preview totals, proposed creations, AI/ambiguous matches and overlap/rounding choices before applying. An explicit import request authorizes its specified import; ask only for unresolved mapping decisions or expanded scope, and give the user an opportunity to correct the concrete preview. If review/confirmation was requested, wait for it. A preview-only request never authorizes apply.

Call `apply_time_entry_import` with the retained `importId`. It writes approved timesheet activities and any planned new projects/clients. Continue with the same ID while `done=false`; report partial progress rather than claiming completion. After a timeout/error, call `get_time_entry_import_status` before deciding whether to resume. Never create a fresh batch to retry uncertain work, re-preview a partly applied batch, or blindly replay the file. Respect an in-progress lease/conflict rather than applying concurrently.

Verify final status, applied counts and saved totals with `get_time_entry_import_status`; inspect bounded live activity records when needed to check people/project assignments. Do not validate against lagging daily rollups or double-count clusters and their items. Report imported, skipped, rejected and uncertain rows, total time, date/person coverage, creations and any remaining issues. The uploaded CSV is deleted after successful application; this does not delete the source export.

Use `undo_time_entry_import` only when the user asks to undo the identified batch. Explain that it removes that import's timesheet activities but leaves projects/clients created or restored by the import. Continue the same undo while `done=false`, then verify `undone` status. Never delete unrelated entries or promise that undo restores every metadata change.

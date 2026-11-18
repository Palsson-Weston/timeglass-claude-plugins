---
name: get-started
description: Connect Timeglass and select an accessible workspace when a user first uses the plugin, needs setup help, or asks to change workspace.
---

# Choose the Timeglass workspace

Timeglass connects recorded work, meeting summaries, transcripts, and permitted timesheet edits through its production MCP server at https://app.timeglass.ai/api/mcp.

Use the host’s Timeglass connection and OAuth sign-in flow. If tools are unavailable, explain how to connect the plugin through the host’s connection settings. A successful endpoint response alone does not establish account access. Keep passwords and tokens in the host’s authentication flow.

When the desired workspace is unknown, call `query_workspace_directory(entity="workspaces")`. Use an existing active workspace when it matches the request. If only one workspace matches, select it with `set_workspace`; otherwise ask which matching workspace to use. Resolve named teams and users with the directory, using IDs returned by the service.

Explain that access follows the connected account’s workspace and team permissions. The active team does not implicitly restrict `query_work_records`: pass `teamId` for a team, `departmentId` for a department, or omit both for workspace scope. Apply the narrow scope the user requested and include scope warnings in the answer when they affect coverage.

If a query returns no records, check the selected workspace, team, and date window before reporting an empty result. An authorization failure requires reconnecting or obtaining access through Timeglass; changing identity or guessing IDs cannot resolve it.

Support work-history search, time comparisons, meeting recall, and authorized timesheet corrections. Offer a concrete first task, such as showing this week’s recorded project time. Use current tool schemas for arguments and capabilities.

For setup from an existing project-management tool, use [Import projects](../import-projects/SKILL.md) and [Import clients](../import-clients/SKILL.md). Read the selected source through its separately connected MCP and write only the requested records into Timeglass. A source connector is optional for ordinary Timeglass use; do not require every supported service to be connected.

For recorded history, use [Import historical time](../import-time/SKILL.md), which stages and previews a CSV through Timeglass's admin import tools. Use [Organize unassigned time](../unassigned-time/SKILL.md) to suggest or apply requested classifications, and [Set up budgets and rates](../budgets-rates/SKILL.md) for supported financial settings with explicit historical scope. Inspect current schemas and permissions before offering writes; a connected account may not have these capabilities.

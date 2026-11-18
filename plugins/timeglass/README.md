# Timeglass for Claude Code

Timeglass records your work from screenshots and meeting audio and turns it into timesheets. This plugin connects Claude Code to your Timeglass account so you can ask what you worked on last week, find what was agreed in a meeting, see how your hours split across clients and projects, and correct timesheet entries where your role lets you edit them.

## Setup

1. Add the Timeglass marketplace and install the plugin:

   ```
   /plugin marketplace add Palsson-Weston/timeglass-claude-plugins
   /plugin install timeglass@timeglass
   ```

2. Run `/mcp`, choose **timeglass**, and sign in with your Timeglass account. Claude Code opens the Timeglass sign-in page in your browser and stores the token for you. No API key is needed.
3. Ask Claude to list your workspaces and pick one.

You need a Timeglass account. Sign up at https://timeglass.ai.

## Skills

| Skill | What it does |
| --- | --- |
| `get-started` | Connects your account and selects a workspace |
| `work-history` | Answers questions about what you or your team worked on |
| `meeting-recall` | Finds a meeting and reads its transcript for decisions and follow-ups |
| `timesheet-edits` | Creates, changes or deletes activities you are allowed to edit |
| `unassigned-time` | Suggests clients, projects and categories for unassigned time, and applies them when you say so |
| `import-clients` | Brings clients over from another tool you have connected to Claude |
| `import-projects` | Brings projects over from a connected tool such as Linear, Asana or Jira |
| `import-time` | Stages a CSV of past time from your previous tool, previews it, then applies or undoes it |
| `budgets-rates` | Sets project budgets and billing rates where your plan and role support them |

## Permissions and data

The plugin talks to `https://app.timeglass.ai/api/mcp` over OAuth with the scopes `mcp:read`, `mcp:write` and `offline_access`. Claude only sees records your Timeglass account can already open, and every write follows your role in that workspace. The import skills read from other services only through MCP servers you connect yourself. Nothing from those services is stored by the plugin.

Privacy policy: https://timeglass.ai/privacy. Terms: https://timeglass.ai/terms. Help: https://timeglass.ai/help.

---
name: timesheet-edits
description: Create or correct Timeglass timesheet activities, update entry labels and visibility, or reassign activity items when the user explicitly requests an edit and has permission.
---

# Correct the requested timesheet entry

An explicit edit request authorizes that requested change. Resolve the workspace, target user, date, and matching entry before writing. Read current records with `query_work_records(entity="activities")` or `entity="activity_items"` and retain their returned IDs. Ask only for missing details or an ambiguous target that affects the change. A suggestion to review a timesheet does not authorize edits.

Use `create_activity`, `update_activity`, and `delete_activity` for timesheet activity creation, updates, and deletion, respectively. Each operation has its own tool and takes no `action` argument. Creation requires local `date`, `teamId`, and `title`; set `durationMinutes` to the requested integer minutes. The caller is the default target user. For updates, use `activityId` and the relevant fields, such as `objective`, `totalMinutes`, assignment, category, or `reviewStatus`. Both duration fields accept 1 through 1440 minutes. A requested zero duration needs a supported visibility or deletion operation with clear user intent. `projectId` and `clientId` are mutually exclusive, and null clears assignment. Resolve category IDs from `activity_categories` and use an assignable category.

Use `update_activity_item` to update an existing item’s `objective` or `isHidden`. Use `reassign_activity_items` to move returned `activityItemIds` to a `targetActivityId`. A null target removes the items from their activity. Moved items inherit the target activity’s project/client assignment and must share its user and team. The item tool supports updates and reassignment; item creation and deletion are unavailable.

Permissions come from Timeglass. Viewers have no mutation tools, and managers can edit only where the service permits. An activity created for another user starts pending. Approving an activity is subject to the service’s ownership rules. If a write is denied or the tool is unavailable, explain the failure and leave the data unchanged. The demonstration workspace is read-only; edit tests require a dedicated account with an editable test team.

Perform only the operations in the user’s request. Use the returned mutation result, then read back the affected record or verify its absence after deletion. Report the target, old and new values when available, and verification status. Daily summaries can lag the live entry, so use the affected timesheet record to verify a correction. After a timeout or uncertain write, read the current state before retrying to avoid duplicate entries.

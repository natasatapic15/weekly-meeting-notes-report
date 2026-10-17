# Setup guide – administrator-managed deployment

The automation owner configures, tests, and maintains the workflow. End users access the weekly report without needing to configure n8n or manage API keys.

This template uses one workflow copy per report recipient, with a dedicated calendar connection, report spreadsheet, and tracking table. The automation owner manages these copies and can reuse organization-approved AI credentials where appropriate.

If an individual Google account is required, its owner authorizes the connection. Passwords and API keys should not be sent to the automation owner or stored in this repository.

## Deployment and access

Place each workflow copy in the appropriate n8n project and give it a descriptive name, such as `Weekly Meeting Notes – Anna`.

Configure access to the n8n project, execution history, tracking table, and report spreadsheet according to the intended audience. Separate workflow copies alone do not guarantee that meeting data is private.

## Step 1 – Create the report spreadsheet

1. Create a Google Sheet, for example **Weekly Meeting Report – Anna**.
2. Create three tabs with these exact names:
   - `Meeting Summaries`
   - `Action Items`
   - `Weekly Overview`
3. Paste the corresponding headers from `templates/` into row 1 of each tab:
   - `templates/meeting-summaries-headers.csv`
   - `templates/action-items-headers.csv`
   - `templates/weekly-overview-headers.csv`

If the CSV headers appear in a single cell, use **Data → Split text to columns** and select the comma separator.

Keep the header names unchanged so they match the workflow's column mappings. Grant spreadsheet access to the intended report users.

## Step 2 – Create the tracking Data Table

Create a Data Table in the deployment project. For the first workflow copy, name it `meeting_notes_tracking`. Use a distinct name for additional copies in the same project, such as `meeting_notes_tracking_anna`.

Add the following columns, all of type **String**, as defined in `templates/data-table-schema.csv`:

```text
calendar_id
event_occurrence_id
recurring_event_id
notes_document_id
notes_document_url
meeting_title
meeting_start
reporting_week_start
processing_status
last_error
last_processed_at
```

A dedicated table per workflow copy is the deployment convention used by this template.

## Step 3 – Import the workflow

1. Import `weekly-meeting-notes-report.json` into n8n using **Import from File**.
2. Give the workflow a descriptive name identifying its report recipient.
3. Keep the schedule disabled until configuration and testing are complete.

## Step 4 – Configure credentials

Select or create the appropriate credentials for each node. See [CREDENTIALS.md](CREDENTIALS.md) for connection details.

| Node | Credential |
|---|---|
| Get Last Week Calendar Events | Google Calendar OAuth2 |
| Read Notes Document | Google Docs OAuth2 |
| Write Meeting Summaries Tab, Write Action Items Tab, Write Weekly Overview Tab | Google Sheets OAuth2 |
| OpenAI Model (Meeting Extraction), OpenAI Model (Weekly Overview) | Organization-approved OpenAI API credentials, or Gateway credits on n8n Cloud if supported by the selected configuration |

Reuse the Google Sheets credential across the three report-writing nodes and the AI credential across both model nodes where appropriate.

The Google credentials must have access to the selected calendar, linked notes documents, and destination spreadsheet. Being able to read a calendar event does not automatically grant access to its attached documents.

If a personal Google connection is needed, have the account owner complete the authorization flow. End users do not need their own AI API keys.

## Step 5 – Select the destination resources

| Node | Field | Select |
|---|---|---|
| Write Meeting Summaries Tab | Document | The spreadsheet created in Step 1 |
| Write Action Items Tab | Document | The same spreadsheet |
| Write Weekly Overview Tab | Document | The same spreadsheet |
| Track Meeting in Data Table | Data table | The tracking table created in Step 2 |

Check any other Data Table nodes in the workflow and point them to the same tracking table for this copy.

Confirm that each report-writing node targets the correct tab and that its column mappings match the spreadsheet headers.

## Step 6 – Adjust the configuration

1. Set the reporting time zone in the workflow settings.
2. Confirm that the Schedule Trigger is configured for **Monday at 07:00** in that time zone.
3. Open **Report Configuration** and review:
   - `calendarId`: `primary` refers to the primary calendar of the Google account connected to the Calendar node. Set a different calendar ID when needed.
   - `notesTitleKeywords`: adjust the matching rules to reflect the titles used by the meeting notes tool.
   - Any reporting-period or time-zone settings exposed by the configuration node.
4. Confirm that the reporting period covers the previous calendar week, Monday through Sunday.
5. If a separate time-zone value exists in **Report Configuration**, keep it consistent with the workflow settings.

See [CONFIGURATION.md](CONFIGURATION.md) for configuration details.

## Step 7 – Test and enable the schedule

1. Run the workflow manually against test meetings and notes.
2. Verify that:
   - The correct reporting week and calendar events are selected.
   - The intended notes documents are found and read.
   - Summaries and decisions reflect the source notes.
   - Owners and deadlines are included only when explicitly stated.
   - All three report tabs receive the expected output.
   - Missing or inaccessible notes are handled as intended.
   - The tracking table records the appropriate statuses.
3. Run the workflow again for the same reporting week and check all three tabs for duplicate rows, especially **Action Items**. Resolve any duplicates before enabling scheduled runs.
4. Publish or activate the workflow, as required by the installed n8n version, and confirm that the weekly schedule is enabled.

## End-user experience

Users open the report spreadsheet to review meeting summaries, decisions, action items, and blockers. The report updates automatically each week after a successful scheduled execution.

Users contact the automation owner to request changes to the calendar, reporting preferences, or spreadsheet access.

## Ongoing maintenance

The automation owner:

- Reviews execution results and investigates failures.
- Reconnects expired or revoked credentials when necessary.
- Maintains notes-matching rules and report mappings.
- Tests workflow changes before applying them to active copies.
- Tracks which workflow copies need updates.
- Monitors AI usage and associated costs.

Before processing company meeting notes, confirm that the configured AI provider and report access settings comply with the organization's data policies.

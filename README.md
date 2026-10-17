# Weekly Meeting Notes Report (n8n)

An n8n workflow that runs every Monday. It collects last week's Google Calendar meetings, finds each meeting's **AI-generated notes** (for example "Notes by Gemini"), reads the full Google Doc including tables, and uses AI to extract:

- a meeting summary
- key decisions
- action items, with **owners and deadlines only when the notes name them explicitly**
- blockers and unresolved questions

Results go into one Google Sheet with three tabs:

| Tab | Granularity |
|---|---|
| **Meeting Summaries** | one row per meeting |
| **Action Items** | one row per action item |
| **Weekly Overview** | one row per week, built **only from notes that were read successfully** |

A tracking table (n8n Data Table) records which calendar event and which notes document were processed, and with what status.

> The automation owner configures and maintains the workflow using the appropriate Google account connections and organization-approved AI credentials. End users access the weekly report without configuring n8n. No credentials are stored in this repository. See [docs/SETUP.md](docs/SETUP.md).

---

## Repository contents

```
.
├── README.md                              ← you are here
├── weekly-meeting-notes-report.json       ← import this into n8n
├── examples/
│   └── weekly-meeting-report-example.xlsx ← sample report with fictional test data
├── docs/
│   ├── SETUP.md                           ← setup instructions for the automation owner
│   ├── CREDENTIALS.md                     ← Google + OpenAI credentials explained
│   ├── CONFIGURATION.md                   ← settings in the "Report Configuration" node
│   ├── HOW-IT-WORKS.md                    ← node-by-node description
│   └── TROUBLESHOOTING.md
└── templates/
    ├── meeting-summaries-headers.csv      ← row 1 of the "Meeting Summaries" tab
    ├── action-items-headers.csv           ← row 1 of the "Action Items" tab
    ├── weekly-overview-headers.csv        ← row 1 of the "Weekly Overview" tab
    └── data-table-schema.csv              ← columns for the tracking Data Table
```

## Example report

Download the [example weekly report](examples/weekly-meeting-report-example.xlsx) to preview the output across all three tabs: **Meeting Summaries**, **Action Items**, and **Weekly Overview**.

The example contains **fictional test data only** and is provided to demonstrate the report structure. It does not contain real meeting notes or company information. The Excel file is a static preview; the workflow writes its live output to Google Sheets.

## Administrator setup

The automation owner handles configuration, testing, and ongoing maintenance. End users receive the weekly report without needing to configure n8n or manage API keys.

1. **Prepare the report spreadsheet:** create a Google Sheet with three tabs named exactly `Meeting Summaries`, `Action Items`, and `Weekly Overview`. Copy the corresponding headers from `templates/` into the first row of each tab.
2. **Create the tracking table:** create an n8n Data Table named `meeting_notes_tracking`, using the columns defined in `templates/data-table-schema.csv` (all of type *string*).
3. **Import the workflow:** import `weekly-meeting-notes-report.json` into n8n.
4. **Configure credentials:** connect the appropriate Google Calendar, Google Docs, Google Sheets, and organization-approved AI credentials. See [docs/CREDENTIALS.md](docs/CREDENTIALS.md). If access to an individual Google account is required, ask the account owner to authorize the connection.
5. **Select the destination resources:** configure the spreadsheet in the three *Write … Tab* nodes and the tracking table in *Track Meeting in Data Table*.
6. **Review Report Configuration:** set the calendar, time zone, reporting period, and notes-matching rules. Confirm the reporting requirements and access permissions with the intended users.
7. **Test the workflow:** run it manually and verify that meeting summaries, action items, and the weekly overview are written correctly.
8. **Enable the schedule:** confirm the workflow’s time zone, then publish it to run every Monday at 07:00.

## End-user experience

Once setup is complete, users receive access to the report spreadsheet, which is updated automatically each week. They can review meeting summaries, decisions, action items, and blockers in one place.

The automation owner maintains the workflow, monitors executions, and handles configuration changes or failures.

## Requirements

- n8n (Cloud or self-hosted) with Data Tables available
- A Google account (Workspace or personal) whose calendar has meetings with notes docs attached
- Access to the configured AI provider, managed by the automation owner (an OpenAI API key or Gateway credits on n8n Cloud)

## Privacy

Meeting notes are sent to the AI provider you configure (OpenAI by default) for extraction. Check that this complies with your organization's data policies before you enable it.

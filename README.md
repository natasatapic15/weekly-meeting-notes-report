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

> Each team member runs **their own copy** with **their own Google account and their own AI key**. No credentials are stored in this repository. See [docs/SETUP.md](docs/SETUP.md).

---

## Repository contents

```
.
├── README.md                              ← you are here
├── weekly-meeting-notes-report.json       ← import this into n8n
├── examples/
│   └── weekly-meeting-report-example.xlsx ← sample report with fictional test data
├── docs/
│   ├── SETUP.md                           ← step-by-step setup for each team member
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

## Quick start (about 15 minutes)

1. **Create the Google Sheet** with three tabs named exactly `Meeting Summaries`, `Action Items`, and `Weekly Overview`. Paste the matching header row from `templates/` into row 1 of each tab.
2. **Create the Data Table** `meeting_notes_tracking` in your n8n project, using the columns in `templates/data-table-schema.csv` (all of type *string*).
3. **Import** `weekly-meeting-notes-report.json` into n8n (*Workflows → ⋯ → Import from File*).
4. **Connect your own credentials**: Google Calendar, Google Docs, Google Sheets, and OpenAI (or Gateway credits on n8n Cloud). See [docs/CREDENTIALS.md](docs/CREDENTIALS.md).
5. **Select your resources**: your spreadsheet in the 3 *Write … Tab* nodes and your Data Table in *Track Meeting in Data Table*.
6. Review **Report Configuration** (calendar, time zone, notes-matching rules).
7. Click **Execute workflow** once to test, check the sheet, then **Publish** to run it every Monday at 07:00.

## Requirements

- n8n (Cloud or self-hosted) with Data Tables available
- A Google account (Workspace or personal) whose calendar has meetings with notes docs attached
- An OpenAI API key, **or** Gateway credits on n8n Cloud

## Privacy

Meeting notes are sent to the AI provider you configure (OpenAI by default) for extraction. Check that this complies with your organization's data policies before you enable it.

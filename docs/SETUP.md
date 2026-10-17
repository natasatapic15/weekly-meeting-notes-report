# Setup guide – one copy per team member

Every colleague gets an independent copy of the workflow that uses **their own** calendar, Google account, AI key, spreadsheet, and tracking table. Nothing is shared between copies, so no one sees anyone else's meetings.

## Why one copy per person?

- n8n credentials belong to the person (or project) that created them and are never exported with a workflow. The JSON in this repo has **no credentials**.
- The workflow runs on a **schedule**, not because a person clicked something, so there is no "current user" whose account it could borrow at run time. Each person's copy has to hold that person's credentials.
- Data Tables belong to an n8n project. Each copy needs its own tracking table.

### Where to put the copies

| Your setup | Recommended |
|---|---|
| Each team member has their own n8n account on a shared instance | Each person imports the workflow into their **personal project** |
| Shared n8n project for a team | One copy per person, named e.g. `Weekly Meeting Notes – Anna`, each with Anna's credentials |
| Colleagues use their own n8n instance | Each person imports it into their own instance |

---

## Step 1 – Create the Google Sheet

1. Create a new Google Sheet, for example **"Weekly Meeting Report – <your name>"**.
2. Create three tabs with these **exact** names (case and spaces matter):
   - `Meeting Summaries`
   - `Action Items`
   - `Weekly Overview`
3. In each tab, paste the matching header line from `templates/` into **row 1**:
   - `templates/meeting-summaries-headers.csv`
   - `templates/action-items-headers.csv`
   - `templates/weekly-overview-headers.csv`

   Tip: open the CSV, copy the line, paste it into cell A1, then use *Data → Split text to columns*.

The header names must match exactly, because the workflow uses them to find columns and to update existing rows instead of creating duplicates.

## Step 2 – Create the tracking Data Table

1. In n8n, open your project, then go to **Data tables → Create data table**.
2. Name it `meeting_notes_tracking`.
3. Add these columns, all of type **String**:

```
calendar_id, event_occurrence_id, recurring_event_id, notes_document_id,
notes_document_url, meeting_title, meeting_start, reporting_week_start,
processing_status, last_error, last_processed_at
```

## Step 3 – Import the workflow

1. In n8n, go to **Workflows**, then **⋯ / Create → Import from File**.
2. Select `weekly-meeting-notes-report.json`.
3. Optional: rename it, for example `Weekly Meeting Notes – <your name>`.

## Step 4 – Connect your credentials

Open each node below and choose **Create new credential** in its credential field. Full details are in [CREDENTIALS.md](CREDENTIALS.md).

| Node | Credential |
|---|---|
| Get Last Week Calendar Events | Google Calendar OAuth2 |
| Read Notes Document | Google Docs OAuth2 |
| Write Meeting Summaries Tab, Write Action Items Tab, Write Weekly Overview Tab | Google Sheets OAuth2 (create it once, then reuse it in all three) |
| OpenAI Model (Meeting Extraction), OpenAI Model (Weekly Overview) | OpenAI API key (create it once, then reuse it in both), **or** *Use Gateway credits* on n8n Cloud |

## Step 5 – Select your resources

| Node | Field | Select |
|---|---|---|
| Write Meeting Summaries Tab | Document | your spreadsheet from Step 1 |
| Write Action Items Tab | Document | the same spreadsheet |
| Write Weekly Overview Tab | Document | the same spreadsheet |
| Track Meeting in Data Table | Data table | `meeting_notes_tracking` from Step 2 |

## Step 6 – Adjust the configuration

1. Set **your** time zone in the workflow settings (**⋯ → Settings → Timezone**). The schedule ("Monday 07:00"), the reporting week, and the meeting times all follow it.
2. Open **Report Configuration**. The defaults work for most teams: `calendarId = primary` reads the calendar of the person who connected the Google credential. Adjust `notesTitleKeywords` if your team's notes tool names its documents differently. See [CONFIGURATION.md](CONFIGURATION.md).

## Step 7 – Test and publish

1. Click **Execute workflow**. It processes **last week** (Monday–Sunday) immediately.
2. Check the three tabs and the Data Table.
3. When you're happy with the results, click **Publish**. From then on it runs every Monday at 07:00.

Re-running for the same week is safe. Rows are updated, not duplicated.

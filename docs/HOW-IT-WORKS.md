# How it works

The workflow turns meeting notes linked to Google Calendar events into a weekly Google Sheets report.

The automation owner configures and maintains the workflow. End users access the resulting spreadsheet.

## Processing stages

| Stage | Purpose |
|---|---|
| Collect meetings | Calculate the previous reporting week, retrieve Calendar events, and apply event filters. |
| Retrieve notes | Identify eligible notes attachments, read the Google Docs, and extract their text. |
| Analyze meetings | Extract summaries, decisions, action items, owners, deadlines, and blockers. |
| Write reports | Record processing information and write the meeting summaries, action items, and weekly overview. |

## Main nodes

| Node | Role |
|---|---|
| Every Monday 07:00 | Starts the scheduled run. |
| Report Configuration | Holds report settings and calculates the reporting period. |
| Get Last Week Calendar Events | Retrieves events for the configured period. |
| Keep Conducted Meetings | Applies event-selection rules. Its name does not imply attendance has been verified. |
| Identify Notes Document | Selects a notes attachment using the configured title and MIME-type rules. |
| Read Notes Document | Retrieves the selected Google Doc. |
| Extract Full Notes Text | Extracts text, including table content, and classifies notes availability. |
| Notes Retrieved? | Routes meetings according to whether usable notes were retrieved. |
| Extract Meeting Insights (AI) | Extracts structured information from the notes. |
| Build Processed Meeting Record | Builds a report record for successfully analyzed notes. |
| Build Missing Notes Record | Builds a report record for meetings without usable notes. |
| Combine All Meetings | Combines meeting records for reporting. |
| Track Meeting in Data Table | Records meeting identifiers, notes references, processing status, and error information. |
| Write Meeting Summaries Tab | Writes meeting-level report rows. |
| Split Out Action Items | Produces individual action-item records. |
| Write Action Items Tab | Writes action-item rows. |
| Prepare Weekly Overview Input | Prepares meeting counts and successfully analyzed content for the overview. |
| Generate Weekly Overview (AI) | Summarizes themes, decisions, follow-up actions, and blockers. |
| Write Weekly Overview Tab | Writes the weekly overview. |

## Processing statuses

| Status | Meaning |
|---|---|
| `Ready` | Usable notes text is available for AI analysis. This is an intermediate status. |
| `Processed` | Notes were retrieved and analyzed. |
| `Missing notes` | No attachment matched the notes criteria. |
| `Notes inaccessible` | The selected notes document could not be read. |
| `Notes empty` | The document was read but no usable text was extracted. |

`Processed` describes notes analysis. It should not be treated as confirmation that every downstream spreadsheet write succeeded.

Review execution results to confirm successful report delivery.

## Tracking and repeated runs

The tracking table records processing information. Recording a status does not, by itself, prevent a meeting from being processed again.

The documented matching rules for report writes are:

| Output | Matching fields |
|---|---|
| Meeting Summaries | Calendar event ID |
| Action Items | Calendar event ID and Action item |
| Weekly Overview | Reporting week start |

These rules assume a dedicated report spreadsheet for each workflow copy.

Matching action items by their text has a limitation: if the AI changes the wording on a later run, the same task may be treated as a new row. Previously written tasks may also remain if they are absent from the new extraction.

Before relying on repeated runs, verify the actual node configuration and test for duplicate or stale rows. Do not assume that tracking alone provides duplicate prevention.

## Report output

### Meeting Summaries

One row per meeting, with:

- Reporting week start date
- Meeting date
- Meeting title
- Organizer
- Summary
- Key decisions
- Blockers and unresolved questions
- Notes document URL
- Calendar event ID
- Processing status
- Start time
- End time
- Time zone
- Attendees
- Notes document ID

### Action Items

One row per extracted action item, with:

- Reporting week start date
- Meeting date
- Meeting title
- Action item
- Owner
- Deadline
- Notes document URL
- Calendar event ID

Owners and deadlines should remain blank unless explicitly stated in the notes.

### Weekly Overview

One row per reporting week, with:

- Reporting week start
- Reporting week end
- Number of meetings found
- Meetings with processed notes
- Meetings with missing or inaccessible notes
- Main themes
- Major decisions
- Key follow-up actions
- Main blockers

The overview should be grounded only in successfully analyzed notes. Meeting counts and unavailable notes should remain distinguishable from the AI-generated narrative.

## Validation

Before deployment, test:

- Meetings with valid notes.
- Missing, inaccessible, and empty documents.
- Notes containing tables.
- Recurring meeting occurrences.
- Meetings with no action items.
- A week with no meetings or no usable notes.
- Repeated execution for the same week.
- Failures in AI processing or spreadsheet writes.

Confirm error handling and branch behavior in the imported workflow rather than assuming that every failure is handled automatically.

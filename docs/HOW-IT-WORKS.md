# How it works

The workflow is organized into four groups on the canvas.

```
Every Monday 07:00
  └─ [Collect conducted meetings]
       Report Configuration → Get Last Week Calendar Events → Keep Conducted Meetings
       → Identify Notes Document → Read Notes Document → Extract Full Notes Text
  └─ [Analyze meeting notes]
       Notes Retrieved? ─ yes → Extract Meeting Insights (AI) → Build Processed Meeting Record ┐
                        └ no  → Build Missing Notes Record ───────────────────────────────────┤
                                                                       Combine All Meetings ◄─┘
  ├─ Track Meeting in Data Table
  ├─ Write Meeting Summaries Tab
  ├─ [Log action items]      Split Out Action Items → Write Action Items Tab
  └─ [Summarize the week]    Prepare Weekly Overview Input → Generate Weekly Overview (AI)
                             → Write Weekly Overview Tab
```

## Nodes

| Node | What it does |
|---|---|
| **Every Monday 07:00** | Schedule trigger. |
| **Report Configuration** | Holds the settings and calculates last week's date range. |
| **Get Last Week Calendar Events** | Reads all events in the range, expanding recurring meetings into individual occurrences. |
| **Keep Conducted Meetings** | Drops cancelled, all-day, out-of-office, focus-time, declined, and future events. |
| **Identify Notes Document** | Looks at the attachments and picks the notes doc using the configured keywords, regex, and MIME types. Also formats the start and end time with time zone, the organizer, and the attendees. |
| **Read Notes Document** | Fetches the Google Doc. A missing or inaccessible doc doesn't stop the run. |
| **Extract Full Notes Text** | Converts the document to plain text, **including table cells**, and assigns a status: `Ready`, `Missing notes`, `Notes inaccessible`, or `Notes empty`. |
| **Notes Retrieved?** | Sends only `Ready` meetings to the AI. |
| **Extract Meeting Insights (AI)** + *OpenAI Model* + *Meeting Insights Schema* | Returns structured JSON: summary, decisions, action items (action, owner, deadline), blockers. |
| **Build Processed Meeting Record** / **Build Missing Notes Record** | Gives both paths the same shape. Processed meetings get status `Processed`. |
| **Combine All Meetings** | Merges both paths, so meetings without notes still appear in the report. |
| **Track Meeting in Data Table** | Upserts one row per (calendar ID, event occurrence ID) with the notes doc ID/URL, the status, and the error detail. |
| **Write Meeting Summaries Tab** | Upserts one row per meeting, matched on *Calendar event ID*. |
| **Split Out Action Items** → **Write Action Items Tab** | One row per action item, matched on *Calendar event ID* + *Action item*. |
| **Prepare Weekly Overview Input** | Counts all meetings, but builds the AI input **only from `Processed` meetings**. |
| **Generate Weekly Overview (AI)** | Produces main themes, major decisions, key follow-up actions, and main blockers. |
| **Write Weekly Overview Tab** | Upserts one row per week, matched on *Reporting week start*. |

## Processing statuses

| Status | Meaning |
|---|---|
| `Processed` | Notes found, read, and analyzed |
| `Missing notes` | No attachment matched the notes criteria |
| `Notes inaccessible` | The doc matched but couldn't be opened (usually a sharing or permission issue) |
| `Notes empty` | The doc opened but contained no text |

## Output columns

**Meeting Summaries:** Reporting week start date, Meeting date, Meeting title, Organizer, Summary, Key decisions, Blockers and unresolved questions, Notes document URL, Calendar event ID, Processing status, Start time, End time, Time zone, Attendees, Notes document ID

**Action Items:** Reporting week start date, Meeting date, Meeting title, Action item, Owner, Deadline, Notes document URL, Calendar event ID

**Weekly Overview:** Reporting week start, Reporting week end, Number of meetings found, Meetings with processed notes, Meetings with missing or inaccessible notes, Main themes, Major decisions, Key follow-up actions, Main blockers

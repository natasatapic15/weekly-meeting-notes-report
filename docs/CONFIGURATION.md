# Configuration

All user-facing settings live in the **Report Configuration** node (the first node after the trigger).

| Setting | Default | Meaning |
|---|---|---|
| `calendarId` | `primary` | The calendar to read. `primary` means **the calendar of whoever connected the Google Calendar credential**, so every team member's copy automatically reads their own calendar. Leave it as is unless you want a different calendar. For a shared calendar, use its ID (*Google Calendar → Settings → Integrate calendar → Calendar ID*). |
| `reportTimeZone` | the workflow's time zone (`$now.zoneName`) | Used for meeting times when an event doesn't specify its own. It follows **⋯ → Settings → Timezone** (or the instance default), so normally you don't touch it. You can override it with a fixed value such as `America/New_York`. |
| `notesTitleKeywords` | `Notes by Gemini, Meeting notes` | Comma-separated. A document counts as the notes if its **title contains** any keyword (case-insensitive). |
| `notesAllowedMimeTypes` | `application/vnd.google-apps.document` | Comma-separated. Only attachments of these types are considered. The default is native Google Docs. |
| `notesTitleRegex` | *(empty)* | Optional regular expression, also matched against the title (case-insensitive). Example: `^(Notes|Transcript)\b`. |
| `useSingleGoogleDocWhenNoMatch` | `false` | If `true` and exactly **one** eligible doc is attached but no title matched, use that doc. Leave it `false` so that agendas or specs aren't mistaken for notes. |
| `reportingWeekStart` / `reportingWeekEnd` / `timeMin` / `timeMax` | expressions | Calculated automatically as **last Monday 00:00 to Sunday 23:59** in the workflow's time zone. No editing needed. |

## Which meetings are included?

A calendar event is processed when **all** of these are true:

- it isn't cancelled
- it has a start time (all-day events are skipped)
- it isn't an *Out of office*, *Focus time*, or *Working location* entry
- it has already ended
- you haven't declined it

Each occurrence of a recurring meeting is handled separately (its own event occurrence ID).

## Schedule

The trigger **Every Monday 07:00** uses the workflow time zone (*Workflow → Settings → Timezone*). Change the day or time in the trigger node if needed.

## AI model

Open **OpenAI Model (Meeting Extraction)** and **OpenAI Model (Weekly Overview)** to change the model. The extraction prompt tells the AI to fill in an owner or deadline **only when it is written explicitly** in the notes, and otherwise to leave the field empty.

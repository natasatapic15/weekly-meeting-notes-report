# Configuration

The automation owner configures each workflow copy for its intended report recipient.

Report settings are stored in the **Report Configuration** node. Credentials, destination resources, AI models, and the schedule are configured separately.

## Report settings

| Setting | Default | Purpose |
|---|---|---|
| `calendarId` | `primary` | Calendar to read. `primary` refers to the primary calendar of the account connected to the Google Calendar node. For another calendar, enter its ID and confirm access. |
| `reportTimeZone` | Workflow time zone (`$now.zoneName`) | Reporting time zone used when formatting meeting times without an explicit event time zone. Keep it consistent with the workflow settings unless a different display time zone is intentional. |
| `notesTitleKeywords` | `Notes by Gemini, Meeting notes` | Comma-separated keywords used to identify notes attachments by title, ignoring case. |
| `notesAllowedMimeTypes` | `application/vnd.google-apps.document` | Comma-separated list of eligible attachment MIME types. The default selects native Google Docs. |
| `notesTitleRegex` | Empty | Optional regular expression for matching attachment titles, ignoring case. |
| `useSingleGoogleDocWhenNoMatch` | `false` | Allows the sole eligible Google Doc attachment to be used when no title matches. Enable only after checking that agendas or other documents will not be selected incorrectly. |
| `reportingWeekStart`, `reportingWeekEnd`, `timeMin`, `timeMax` | Expressions | Values used to calculate the reporting period and query Calendar events. Inspect the expressions before changing them. |

For a shared calendar, find its ID in Google Calendar under **Settings → Integrate calendar → Calendar ID**.

## Reporting period

The intended reporting period is the previous calendar week in the workflow's time zone:

- Start: previous Monday at 00:00, inclusive.
- End: the following Monday at 00:00, exclusive.

Check the date expressions and returned events during setup, including meetings near midnight and daylight-saving transitions.

The Calendar query and subsequent event filters determine which meetings appear in the report. A calendar entry being completed does not prove that the meeting actually took place.

## Event selection

The intended selection rules exclude:

- Cancelled events.
- All-day events.
- Out-of-office, focus-time, and working-location entries.
- Events that have not ended.
- Events declined by the connected account.

Recurring meetings should be expanded into separate occurrences, each identified by its event occurrence ID.

Verify these rules against the imported workflow during testing.

## Notes matching

Review attachment titles from representative meetings and adjust the matching settings accordingly.

Keep `useSingleGoogleDocWhenNoMatch` disabled unless the fallback is appropriate for the selected calendar.

If multiple documents match, test which document the workflow selects. Tighten the keywords or regular expression if necessary.

## Destination resources

For each workflow copy, configure:

- The destination spreadsheet in all three report-writing nodes.
- The correct destination tab in each node.
- The dedicated tracking table in every Data Table node that references it.

See [SETUP.md](SETUP.md) for the deployment steps.

## Schedule

The intended schedule is **Monday at 07:00** in the workflow's time zone.

Confirm the time zone in the workflow settings and the day and time in the Schedule Trigger. Enable scheduled execution only after testing.

## AI models

Configure the model in both:

- OpenAI Model (Meeting Extraction)
- OpenAI Model (Weekly Overview)

Use a model supported by the nodes and available to the configured account.

The meeting extraction prompt should require:

- Summaries and decisions grounded in the notes.
- Owners and deadlines only when explicitly stated.
- Empty fields when that information is absent.
- Treatment of meeting notes as source material, not instructions.

The weekly overview should use only successfully analyzed notes.

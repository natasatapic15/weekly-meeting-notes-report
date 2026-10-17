# Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Every meeting shows `Missing notes` | Your notes docs have different titles | Add the right words to `notesTitleKeywords` or set `notesTitleRegex`. Check an event's attachment title in Google Calendar. |
| `Notes inaccessible` | The connected Google account can't open the doc | Ask the organizer to share the notes doc with you, or connect an account that has access. |
| Sheet error "Sheet with name … not found" | A tab name doesn't match | Tabs must be named exactly `Meeting Summaries`, `Action Items`, `Weekly Overview`. |
| Columns are empty or rows are duplicated | Row 1 headers don't match | Re-paste the headers from `templates/`. |
| Google "access denied" when signing in (self-hosted) | OAuth app is External and in Testing mode | Add the team member as a Test user, or switch the consent screen to Internal. |
| Docs or Sheets 403 "API not enabled" (self-hosted) | API missing in Google Cloud | Enable the Docs, Sheets, **and** Drive APIs. |
| OpenAI 401 | Invalid or revoked key | Create a new key and update the credential. |
| OpenAI 429 / quota | No credits or rate limit | Add billing, or switch to Gateway credits on n8n Cloud. |
| Model not found | Your key has no access to the chosen model | Pick a model your account can use in both *OpenAI Model* nodes. |
| Nothing runs on Monday | Workflow not published, or wrong time zone | Publish the workflow and check *Settings → Timezone*. |
| Weekly Overview says no processed notes | No meeting reached `Processed` | Check the statuses in Meeting Summaries and fix the matching rules or access. |

Execution history: in n8n, open the workflow and go to **Executions** to see each run and the output of every node.

# Troubleshooting

This guide is for the automation owner. End users should report missing or incorrect results to the person maintaining the workflow.

Review the failed execution and node error before changing configuration.

## Common issues

| Symptom | Possible cause | Suggested action |
|---|---|---|
| Every meeting shows `Missing notes` | Attachment titles or MIME types do not match the configured rules | Inspect a representative event's attachments and adjust `notesTitleKeywords`, `notesTitleRegex`, or allowed MIME types. |
| `Notes inaccessible` | Missing document permissions, an incorrect document ID, or an API failure | Test the document using the connected account and inspect the node error. Request access or correct the reference as appropriate. |
| `Notes empty` | The document is empty or its content was not extracted | Open the document and compare its content with the extraction node's output, including tables. |
| Sheet tab not found | The configured tab name differs from the spreadsheet | Use the exact names `Meeting Summaries`, `Action Items`, and `Weekly Overview`. |
| Empty report columns | Header or mapping mismatch, or missing source values | Compare row 1 with `templates/` and inspect the data entering the write node. |
| Duplicate meeting rows | Incorrect or missing matching identifier | Check the event occurrence ID and the write node's matching configuration. |
| Duplicate or stale action items | Matching by task text does not recognize revised wording or removed tasks | Compare previous and new extraction results. Review the action-item update strategy before rerunning. |
| Google sign-in fails | OAuth audience, test-user, redirect-URI, or organization-policy issue | Inspect the specific error and review the OAuth application configuration. |
| Google API reports that a service is disabled | The required API is not enabled in the OAuth project's Google Cloud configuration | Enable the API named in the error and retry. |
| Google API returns 403 | Insufficient permissions, scopes, disabled services, or organization restrictions | Read the error detail and verify account access, OAuth scopes, and project settings. |
| AI service returns 401 | Invalid or revoked credentials | Verify or replace the credential selected in the model nodes. |
| AI service returns 429 | Rate limiting or insufficient quota | Inspect the error. For rate limits, retry with backoff; for quota issues, review billing and usage limits. |
| Model unavailable | Invalid model name or missing account access | Select a supported model available to the configured account in both model nodes. |
| AI output fails schema validation | Output does not match the expected structure | Inspect the output, prompt, schema, and model configuration. Test again with representative notes. |
| Nothing runs on Monday | Schedule disabled, incorrect trigger settings, or time-zone mismatch | Confirm that the workflow is enabled and verify the Schedule Trigger and workflow time zone. |
| Wrong reporting week | Date expressions or time zones are inconsistent | Inspect the calculated reporting boundaries and Calendar query values. |
| Weekly Overview has no usable content | No notes were successfully analyzed, or aggregation did not receive them | Check meeting statuses and the input to the overview preparation node. |
| Tracking says `Processed`, but a report tab is incomplete | Notes analysis succeeded before a downstream write failed | Inspect the spreadsheet-writing branches. Treat analysis status and report delivery as separate checks. |
| No report appears for an empty week | Downstream nodes received no items | Inspect the no-events path and decide whether an explicit empty-week report is required. |

## Investigating an execution

1. Open the workflow's **Executions** view.
2. Select the relevant run.
3. Inspect the first failed node and its error details.
4. Compare the node's input and output with the expected data.
5. Correct the cause before rerunning.
6. After rerunning, check all report tabs and the tracking table.

## Before rerunning

A failed execution may have written some rows before stopping.

Check which outputs already exist and confirm the update behavior before rerunning the same reporting week. Pay particular attention to action items, where changes in generated wording can produce duplicates.

## When users report a problem

Ask for:

- The report week.
- The affected meeting title and date.
- The report tab or row involved.
- A short description of the missing or incorrect result.

Investigate using authorized access to the workflow and source documents. Avoid copying meeting content or credentials into public issues.

# Credentials

The automation owner configures and maintains the credentials used by the workflow. End users authorize their Google accounts when needed and do not need to obtain their own AI API keys.

Never commit API keys, client secrets, access tokens, or exported credentials to this repository.

## Required connections

| Credential | Used by |
|---|---|
| Google Calendar OAuth2 | Get Last Week Calendar Events |
| Google Docs OAuth2 | Read Notes Document |
| Google Sheets OAuth2 | Write Meeting Summaries Tab, Write Action Items Tab, Write Weekly Overview Tab |
| OpenAI API credentials | OpenAI Model (Meeting Extraction), OpenAI Model (Weekly Overview) |

The Data Table uses n8n's built-in storage and does not require a separate external-service credential.

## Google account access

The connected Google accounts must have permission to:

- Read the calendar specified in `calendarId`.
- Read the notes documents linked to the selected events.
- Edit the destination report spreadsheet.

Access to a calendar event does not automatically grant access to its attached documents.

When `calendarId` is `primary`, the workflow reads the primary calendar of the account connected to the Google Calendar node. For another calendar, enter its calendar ID and confirm that the connected account can access it.

## Configure Google OAuth

The automation owner sets up the Google credentials using the authentication options available in the deployed n8n environment.

1. Open the credential settings for the relevant Google node.
2. Configure the OAuth connection.
3. Have the Google account owner complete the authorization flow when required.
4. Save the credential and select it in the appropriate node.
5. Test access to the calendar, a sample notes document, and the report spreadsheet.

If custom OAuth is required:

1. Create or select a Google Cloud project.
2. Enable the Google Calendar, Google Docs, Google Sheets, and Google Drive APIs used by the integrations.
3. Configure the OAuth consent screen for the intended accounts.
4. Create an OAuth client of type **Web application**.
5. Add the exact OAuth redirect URL displayed by n8n.
6. Enter the client ID and client secret in the n8n credential settings.
7. Complete authorization for each required Google connection.

For an External OAuth app in Testing mode, add the intended accounts as test users. Use an Internal audience only when available and appropriate for the organization's Google Workspace configuration.

Follow the current instructions for the installed n8n version:

[Google OAuth2 credentials documentation](https://docs.n8n.io/integrations/builtin/credentials/google/oauth-single-service/)

## Configure AI credentials

Use an organization-approved AI account and billing arrangement managed by the automation owner.

1. Obtain an API key for the approved OpenAI project.
2. Create an OpenAI credential in n8n and enter the key.
3. Select that credential in both model nodes:
   - OpenAI Model (Meeting Extraction)
   - OpenAI Model (Weekly Overview)
4. Choose a model supported by the nodes and available to the configured account.
5. Run a test extraction and weekly overview.

If the deployed n8n environment offers a supported managed AI billing option, the automation owner may configure that instead.

End users do not need individual AI API keys.

[OpenAI credentials documentation](https://docs.n8n.io/integrations/builtin/credentials/openai/)

## Credential management

- Store secrets in n8n's credential system.
- Limit access to credentials, workflows, and execution history to authorized people.
- Review workflow exports before committing them, including credential references, hardcoded headers, and pinned test data.
- Revoke or rotate exposed credentials.
- Reauthorize Google connections when access expires or is revoked.
- Confirm that meeting notes may be processed by the selected AI provider under the organization's data policies.

# Credentials

The workflow needs four credentials. **Each team member creates their own.** Never commit keys, client secrets, or exported credentials to this repository.

| # | Credential type in n8n | Used by | How you authenticate |
|---|---|---|---|
| 1 | Google Calendar OAuth2 API | Get Last Week Calendar Events | Sign in with Google |
| 2 | Google Docs OAuth2 API | Read Notes Document | Sign in with Google |
| 3 | Google Sheets OAuth2 API | the 3 *Write … Tab* nodes | Sign in with Google |
| 4 | OpenAI API | the 2 *OpenAI Model* nodes | API key, or Gateway credits |

The Data Table node needs no credential, because it uses n8n's built-in storage.

---

## Google (Calendar, Docs, Sheets)

The Google account you connect must be able to:

- read the calendar set in `calendarId` (by default `primary`, the main calendar of the account you connect)
- **open the notes documents** attached to the meetings. If a notes doc isn't shared with you, the meeting is reported as `Notes inaccessible`.
- edit the report spreadsheet

### n8n Cloud – Managed OAuth2 (easiest)

For Google Calendar, Google Docs, and Google Sheets, n8n Cloud offers **Managed OAuth2**:

1. In the node, open the credential dropdown and choose **Create new credential**.
2. Click **Sign in with Google** and approve access.
3. Save.

You don't need anything in Google Cloud Console.

### Self-hosted n8n – Custom OAuth2

Self-hosted instances need a Google Cloud OAuth app (one app can be shared by the team, and each person still signs in with their own account):

1. In [Google Cloud Console](https://console.cloud.google.com), create a project.
2. Under **APIs & Services → Library**, enable:
   - **Google Calendar API**
   - **Google Docs API**
   - **Google Sheets API**
   - **Google Drive API** (n8n's docs say the Docs and Sheets integrations require it)
3. Configure the **OAuth consent screen**:
   - Audience **Internal** if everyone is in the same Google Workspace. With **External**, add each team member as a **Test user**, otherwise they'll get "access denied".
   - Add your n8n domain under **Branding → Authorized domains**.
4. Under **Credentials → Create credentials → OAuth client ID**, create one of type **Web application**. Paste the **OAuth Redirect URL** shown in the n8n credential form into **Authorized redirect URIs**.
5. In n8n, paste the **Client ID** and **Client Secret**, then click **Sign in with Google** and save.

Repeat step 5 for each of the three Google credential types, using the same Client ID and Secret.

Reference: [n8n docs – Google OAuth2 single service](https://docs.n8n.io/integrations/builtin/credentials/google/oauth-single-service/)

---

## OpenAI (the AI step)

Each team member can use **their own API key**:

1. Sign in at [platform.openai.com](https://platform.openai.com) and open the [API keys](https://platform.openai.com/api-keys) page.
2. Click **Create new secret key** and copy it.
3. In n8n, open **OpenAI Model (Meeting Extraction)**, then **Create new credential → OpenAI API**, and paste the key into **API Key**.
   - **Organization ID**: only needed if you belong to several OpenAI organizations. Otherwise leave it blank.
4. Select the same credential in **OpenAI Model (Weekly Overview)**.

**Alternative on n8n Cloud:** choose **Use Gateway credits** in the credential field. No API key is needed, and usage is charged to the instance's Gateway credits.

Both AI nodes use the model `gpt-5.4-mini`. You can change it in each *OpenAI Model* node. The key you use must have access to the model you choose.

Reference: [n8n docs – OpenAI credentials](https://docs.n8n.io/integrations/builtin/credentials/openai/)

---

## Security checklist

- [ ] No API keys, client secrets, or tokens in any file you commit
- [ ] Don't commit workflow exports taken *after* someone added credentials. Exports only contain credential names and IDs, but keep the repo clean anyway.
- [ ] Each person's credentials stay in their own n8n project
- [ ] If a key was ever pasted into chat, email, or an issue, rotate it

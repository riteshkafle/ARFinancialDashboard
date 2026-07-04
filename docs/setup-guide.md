# Setup Guide — From Zero to Recorded Demo

Follow these in order. Budget roughly half a day if all accounts (n8n, Google, OpenAI,
Slack) already exist; add an hour if you need to create any of them.

## 1. Get the repo in your own hands

```bash
git clone <your-fork-or-repo-url> ar-copilot
cd ar-copilot
```

If you haven't pushed this project to GitHub yet, do that first (see the "Making it public"
section in the main [README](../README.md)) so you have a URL to submit alongside the video.

## 2. Create the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a new spreadsheet named
   **AR Copilot — Invoice Ledger**.
2. Rename the first tab to `Invoices`.
3. Import [`data/invoices.csv`](../data/invoices.csv): **File → Import → Upload**, select
   the file, choose "Replace current sheet," and import.
4. Create a second tab named `Dashboard` — leave it empty; the workflow appends rows to it.
5. Optional but recommended: select the `Risk Score` column → **Format → Conditional
   formatting** → color Low green, Medium yellow, High orange, Escalate red. This is
   screenshot #2 in [`screenshots-checklist.md`](screenshots-checklist.md).
6. Copy the Sheet ID from the URL (`https://docs.google.com/spreadsheets/d/<THIS_PART>/edit`)
   — you'll need it in step 4.

## 3. Set up n8n

If you don't already have an instance:

- Easiest path: [n8n Cloud](https://n8n.io/cloud/) free trial — no install required.
- Self-hosted alternative: `npx n8n` (requires Node.js 18+), then open `http://localhost:5678`.

## 4. Import the workflow

1. In n8n, click **Add workflow → Import from File**.
2. Select [`n8n/AR-Copilot-Workflow.json`](../n8n/AR-Copilot-Workflow.json).
3. Open each Google Sheets node (**Read Invoice Sheet**, **Update Invoice Record**,
   **Update Dashboard Sheet**) and replace `PUT_YOUR_GOOGLE_SHEET_ID_HERE` with the Sheet ID
   from step 2.

## 5. Connect credentials

For each of these, click the node → **Credential** dropdown → **Create New**:

| Node | Credential type | Where to get it |
|---|---|---|
| Read Invoice Sheet / Update Invoice Record / Update Dashboard Sheet | Google Sheets OAuth2 | Sign in with the Google account that owns the sheet |
| Generate Reminder Email (AI) | OpenAI API | [platform.openai.com/api-keys](https://platform.openai.com/api-keys) — create a key with a small usage limit for safety |
| Send Reminder Email | Gmail OAuth2 | Sign in with the Google account you want reminders to send from |
| Notify Finance Team | Slack API | Create a Slack app at [api.slack.com/apps](https://api.slack.com/apps) with `chat:write` scope, install it to your workspace, invite the bot to a `#ar-alerts` channel |

If you don't want to set up Slack for the demo, you can disable the **Notify Finance Team**
node (right-click → Deactivate) and just narrate what it would do in the video.

## 6. Do a manual test run

1. Click **Execute Workflow** in the top right (this runs it once, ignoring the schedule).
2. Watch each node turn green. If one fails, click it to see the error — the most common
   issues are a wrong Sheet ID or an OpenAI key without credit.
3. Check: did an email land in the recipient inbox (or your own test inbox if you swapped
   in your own address)? Did the `Invoices` sheet update `Reminder Sent`/`Days Late`/`Risk
   Score`? Did `#ar-alerts` get a message for the High/Escalate rows?

## 7. Build the dashboard

The dashboard at [`dashboard/index.html`](../dashboard/index.html) works standalone — no
server needed:

```bash
open dashboard/index.html
```

It renders from an embedded data snapshot so it works even without n8n running. If you want
it to reflect a live sheet instead, swap the embedded `invoices` array for a `fetch()` call
to a published CSV export of your sheet (**File → Share → Publish to web → CSV**).

## 8. Capture screenshots

Work through [`screenshots-checklist.md`](screenshots-checklist.md) in order, saving into
`docs/screenshots/`.

## 9. Record the Loom video

Use [`loom-script.md`](loom-script.md) as your beat sheet, not a script to read verbatim.
Record at 1080p, keep it under ~2:15, and do one full run-through before hitting record so
you know which nodes might be slow (OpenAI calls are usually the longest step).

## 10. Write up and submit

Fill in the demo/screenshot links in the main [README](../README.md), push everything to
GitHub, and submit the repo URL + Loom link together.

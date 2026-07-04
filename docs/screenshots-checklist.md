# Screenshots Checklist

Capture these in order — they tell the story of the system end to end and map directly onto
the README's Screenshots section and the Loom video. Save them into `docs/screenshots/` using
the filenames below so the README image links resolve without edits.

| # | Filename | What to capture | Why it matters |
|---|---|---|---|
| 1 | `01-sheet-overview.png` | The full Google Sheet with all 32 invoices, columns visible | Shows the "system of record" and realistic data variety |
| 2 | `02-sheet-conditional-formatting.png` | The sheet with conditional formatting on `Risk Score` (color-coded Low/Medium/High/Escalate) | Shows attention to detail beyond the bare minimum |
| 3 | `03-n8n-workflow-full.png` | The entire n8n canvas, zoomed to fit, all nodes visible | The single most important screenshot — shows the whole automation at a glance |
| 4 | `04-n8n-risk-code-node.png` | The `Calculate Days Overdue & Risk` Code node open, showing the JS logic | Proves the risk logic is real code, not a black box |
| 5 | `05-n8n-openai-node.png` | The `Generate Reminder Email (AI)` node open with parameters | Shows the AI integration configuration |
| 6 | `06-n8n-execution-success.png` | A completed workflow execution with all nodes green | Proves it actually runs end-to-end, not just designed |
| 7 | `07-gmail-sent-reminder.png` | An actual sent email in Gmail's Sent folder, opened to show subject + body | Proof the email really sends, with AI-generated content visible |
| 8 | `08-slack-alert.png` | The Slack message in `#ar-alerts` for a High/Escalate invoice | Shows the escalation branch working |
| 9 | `09-sheet-after-update.png` | The Google Sheet after a run, showing `Reminder Sent`/`Days Late`/`Risk Score` updated | Proves the write-back loop closes |
| 10 | `10-dashboard.png` | The finance dashboard (`dashboard/index.html`) with KPIs and charts | The "so what" — turns raw automation into a business-readable view |

## Tips for good screenshots

- Use light mode or dark mode consistently across all of them — don't mix.
- Blur or replace any real email addresses/API keys before sharing publicly; the sample data
  in this repo uses fake companies specifically so this isn't an issue if you keep the demo
  data as-is.
- For the n8n canvas screenshot, use n8n's "Fit to view" (or zoom to ~80%) so every node and
  its name is legible.
- Take the "execution success" screenshot right after a real run, not a mocked one — the
  green checkmarks and item counts are the actual proof of functionality.

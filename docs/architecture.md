# Architecture

## Flow diagram

```mermaid
flowchart TD
    A[Google Sheets<br/>Invoice Ledger] -->|Scheduled Trigger, daily 08:00| B[n8n Workflow]

    subgraph B[n8n Workflow]
        direction TB
        B1[Read Invoice Sheet] --> B2[Filter: Unpaid Invoices]
        B2 --> B3[Calculate Days Overdue and Risk Score]
        B3 --> B4[Filter: Overdue Only]
        B4 --> B5[Build AI Prompt]
        B1 --> B6[Aggregate All Invoices]
    end

    B5 --> C[OpenAI API<br/>Generate Reminder Email]
    C --> D[Parse AI Response]
    D --> E[Gmail<br/>Send Reminder]
    E --> F[Google Sheets<br/>Update Invoice Record]
    F --> G{Risk Score is<br/>High or Escalate?}
    G -- Yes --> H[Slack<br/>#ar-alerts Notification]
    G -- No --> Z1[End]

    B6 --> I[Calculate Dashboard Metrics]
    I --> J[Google Sheets<br/>Dashboard Tab]
    J --> K[Finance Dashboard<br/>KPI View]

    H --> Z2[End]
```

## Component responsibilities

| Layer | Tool | Responsibility |
|---|---|---|
| System of record | Google Sheets | Single source of truth for invoice data; also doubles as the "database" for this prototype |
| Orchestration | n8n | Scheduling, branching, retries, connecting every other tool together |
| Decisioning | n8n Code nodes | Deterministic risk scoring and reminder-stage logic (see [`risk-logic.md`](risk-logic.md)) |
| Generation | OpenAI API | Turns risk tier + invoice facts into natural, on-tone email copy (see [`reminder-email-prompt.md`](../prompts/reminder-email-prompt.md)) |
| Delivery | Gmail | Sends the reminder to the customer |
| Persistence | Google Sheets | Writes back `Reminder Sent`, `Days Late`, `Risk Score` so the sheet always reflects the latest run |
| Escalation | Slack | Pages the finance team only for High/Escalate risk — avoids alert fatigue |
| Reporting | Google Sheets + static dashboard | Rolls up the whole ledger into six KPIs every run |

## Why two branches off "Read Invoice Sheet"

The workflow reads the sheet once per run and fans out into two independent branches:

1. **Reminder branch** — filters to unpaid invoices, scores risk, and emails only the ones
   that are actually overdue.
2. **Dashboard branch** — aggregates the *entire* sheet (paid, pending, and overdue) so the
   KPIs reflect the whole portfolio, not just the invoices that got a reminder that day.

Running these in parallel off a single Sheets read avoids a second API call and keeps both
outputs consistent with the same snapshot of data.

## Failure handling (kept intentionally simple)

This is a one-day prototype, not a production system, so error handling is deliberately
minimal but not absent:

- `Parse AI Response` has a try/catch fallback that builds a generic subject/body if the AI
  response isn't valid JSON, so a single bad model response doesn't break the run.
- The Google Sheets **update** step matches on `Invoice ID`, so re-running the workflow is
  idempotent — it won't create duplicate rows.
- n8n's built-in per-node retry (configurable in node settings) is sufficient for transient
  Gmail/Slack/OpenAI API errors; a fuller system would add dead-letter logging, which is
  called out as a "next step" in [`comparison.md`](comparison.md) rather than built here.

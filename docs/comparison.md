# Comparison to the Original Repo

Starting point: [AbdulRehman448/n8n-invoice-payment-automation](https://github.com/AbdulRehman448/n8n-invoice-payment-automation)
— used strictly as inspiration for the general shape of "n8n + Sheets + Gmail invoice
reminders." No code, workflow JSON, or copy was reused.

## What the original does

- Reads unpaid invoices from Google Sheets on a daily schedule.
- Buckets each invoice into one of three fixed categories: **upcoming** (1–5 days before
  due), **due today**, or **overdue**.
- Sends a static, category-specific templated email via Gmail for each bucket.
- Logs that a reminder was sent, with a timestamp.
- Skips invoices already marked paid.

It's a solid, working template — the reminder logic and email content are static per
category, and it stops at "send an email."

## What AR Copilot adds

| Area | Original | AR Copilot |
|---|---|---|
| Categorization | 3 fixed buckets by proximity to due date | 4-tier **risk score** driven by days-late thresholds (Low/Medium/High/Escalate), independent of email wording |
| Email content | Static template per category | AI-generated per invoice, with a tone instruction that escalates by risk tier — every email is personalized to the customer, amount, and days late |
| Escalation path | None — every overdue invoice is treated the same | High/Escalate invoices trigger a **Slack alert** to the finance team, separate from the customer-facing email |
| Reporting | A log of sent reminders | A **finance dashboard** rolling up Total Outstanding, Total Paid, Overdue Count, High-Risk Accounts, Average Days Late, and Total AR Balance |
| Decision architecture | Categorization and email generation are the same step | Deliberately split: risk scoring is deterministic code (auditable), email generation is AI (personalized) — see [`risk-logic.md`](risk-logic.md) |
| Data realism | Not specified in detail | 32 seeded invoices across 20 fictional customers, spanning every risk tier plus paid/pending, with consistent net-30 math |
| Documentation | README + PDF walkthrough | README + architecture diagram, risk-logic writeup, prompt spec, email template reference, screenshot checklist, Loom script |

## Why these choices, not others

- **Risk tiers over fixed 3-bucket categories** — mirrors how real collections teams
  actually operate (escalating urgency, not a flat "overdue" label), and gives the Slack
  escalation something meaningful to key off.
- **AI email generation over more static templates** — the original already proves static
  templates work; the differentiator here is showing an LLM used for something it's
  actually good at (natural, contextual prose) while keeping the risky part (the risk
  decision itself) in deterministic code, not the model. That split is the main piece of
  "engineering thinking" this version is meant to demonstrate.
- **Slack escalation** — closes the gap between "an email got sent" and "a human knows
  this needs attention." Most AR automations stop at the former.
- **Dashboard** — turns the sheet into something a manager would actually look at, not just
  an audit log.

## What was deliberately left out

To keep this a realistic one-day build rather than overengineered:

- No database — Google Sheets remains the system of record, same as the original.
- No retry/dead-letter queue infrastructure — n8n's built-in node retries are enough for a
  prototype at this scale (see [`architecture.md`](architecture.md)).
- No multi-currency, multi-language, or partial-payment handling — realistic scope creep
  for a take-home, not core to demonstrating the automation pattern.
- No authentication/multi-tenant layer — this is a single-company prototype, not a SaaS
  product.

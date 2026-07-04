# Email Templates — Reference Set

These are hand-written reference examples of what the AI node typically produces at each
risk tier. They exist for two reasons:

1. **Fallback content** — if the OpenAI call fails or is rate-limited, `Parse AI Response`
   falls back to a generic subject line, but these full templates can be pasted in manually
   to keep a demo moving.
2. **Grading/reviewer reference** — so someone reviewing this project can see the intended
   tone progression without needing to run the workflow.

Placeholders (`{{Customer}}`, `{{Invoice ID}}`, etc.) match the Google Sheet column names
exactly, so they can be copy-pasted into a Gmail template or n8n Set node if needed.

---

## 1. First Reminder — Low Risk (0–5 days late)

**Tone:** Friendly, assumes good faith, no pressure.

> **Subject:** Friendly reminder — Invoice {{Invoice ID}} is now past due
>
> Hi {{Customer}},
>
> Just a quick note — our records show Invoice {{Invoice ID}} for ${{Amount}}, originally
> due {{Due Date}}, hasn't been settled yet. This is likely just a timing thing, so no
> action needed if payment is already on its way.
>
> If it's still open, you can pay directly by replying to this email or reaching out to our
> finance team with any questions.
>
> Thanks for being a great customer — we appreciate you!
>
> Warmly,
> AR Copilot Finance Team

---

## 2. Second Reminder — Medium Risk (6–15 days late)

**Tone:** Polite but noticeably firmer, references the first reminder.

> **Subject:** Second reminder: Invoice {{Invoice ID}} — {{Days Late}} days overdue
>
> Hi {{Customer}},
>
> We reached out previously about Invoice {{Invoice ID}} (${{Amount}}, due {{Due Date}}),
> and it still shows as outstanding — now {{Days Late}} days past due.
>
> Could you let us know the status on your end? If there's an issue with the invoice itself
> or you need a short extension, just reply here and we'll work with you. Otherwise, we'd
> appreciate payment at your earliest convenience.
>
> Best regards,
> AR Copilot Finance Team

---

## 3. Final Notice — High / Escalate Risk (16+ / 30+ days late)

**Tone:** Direct, professional, solution-oriented — never threatening, but unambiguous about urgency.

> **Subject:** Final notice: Invoice {{Invoice ID}} is significantly overdue ({{Days Late}} days)
>
> Hi {{Customer}},
>
> Invoice {{Invoice ID}} for ${{Amount}} (originally due {{Due Date}}) remains unpaid after
> multiple reminders and is now {{Days Late}} days overdue.
>
> We'd like to resolve this directly with you — please reply to this email or contact our
> finance team within the next 5 business days to arrange payment or discuss a plan. Without
> a response, we may need to place the account on hold and refer the balance for collections
> review.
>
> We value the relationship and would much rather sort this out together.
>
> Regards,
> AR Copilot Finance Team

---

## Design notes

- **Escalation is gradual, not punitive.** Tier 3 still opens the door to a conversation
  ("we'd like to resolve this directly with you") rather than reading as a legal threat —
  this matches how real finance teams operate and avoids reputational risk from an
  over-aggressive automated email.
- **Every tier is actionable.** Each email tells the recipient exactly what to do next
  (reply, pay, request extension), which is what actually drives payment — a wall of
  overdue-amount text without a clear ask is the most common failure mode of AR reminder
  systems.
- **Subject lines encode urgency** so they're scannable in an inbox even before opening:
  "Friendly reminder" → "Second reminder" → "Final notice".

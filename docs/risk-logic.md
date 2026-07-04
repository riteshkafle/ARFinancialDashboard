# Risk Scoring Logic

## The rule

| Days Late | Risk Tier | Reminder Stage | Behavior |
|---|---|---|---|
| 0 (not yet due) | — | No reminder | Invoice sits as `Pending` |
| 1–5 | **Low** | 1st Reminder | Friendly, assumes oversight |
| 6–15 | **Medium** | 2nd Reminder | Firmer, asks for status |
| 16–29 | **High** | 2nd Reminder + Slack alert | Firm notice, finance team notified |
| 30+ | **Escalate** | Final Notice + Slack alert | Final notice, flagged for collections review |

This is implemented once, in plain JavaScript, in the **Calculate Days Overdue & Risk** Code
node — not inside the AI prompt. See [`n8n/AR-Copilot-Workflow.json`](../n8n/AR-Copilot-Workflow.json).

```js
let riskLevel = 'None';
if (daysLate >= 30) riskLevel = 'Escalate';
else if (daysLate >= 16) riskLevel = 'High';
else if (daysLate >= 6) riskLevel = 'Medium';
else if (daysLate >= 1) riskLevel = 'Low';
```

## Why risk logic lives in code, not in the AI prompt

This is a deliberate architecture decision, and it's worth calling out because it's the
kind of thing a reviewer will notice:

- **Determinism.** A finance workflow that decides "is this account high risk?" should give
  the same answer every time for the same input. An LLM call is non-deterministic by nature —
  fine for wording an email, risky for deciding whether to alert the finance team or trigger
  a collections referral.
- **Auditability.** If someone asks "why did this account get flagged High risk," the answer
  should be a one-line rule ("18 days late ≥ 16-day threshold"), not "the model decided to."
- **Cost and latency.** Risk scoring runs on every row, every day. Doing it in a Code node is
  free and instant; routing it through an LLM would be slower and add cost for no benefit.
- **The AI's job is narrower and better-suited to AI:** turning a risk tier + invoice facts
  into natural-sounding prose. That's genuinely hard to do well with static templates (see
  [`templates/email-templates.md`](../templates/email-templates.md) for what static output
  looks like vs. what the AI can vary per-customer), and it's low-stakes if the wording
  varies slightly between runs.

## Escalation coupling

`Risk Score = High` or `Escalate` is also the condition on the **Is High Risk?** node that
triggers the Slack notification to `#ar-alerts`. This means risk scoring isn't just cosmetic —
it directly drives two downstream actions (tone of the email, and whether a human gets
paged), which is what makes this a real automation rather than a labeling exercise.

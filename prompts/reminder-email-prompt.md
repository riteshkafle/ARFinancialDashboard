# AI Prompt — Reminder Email Generation

This is the exact prompt template used by the **Build AI Prompt** node and sent to the
**Generate Reminder Email (AI)** node in the n8n workflow. It's assembled dynamically per
invoice so the tone escalates automatically with the risk tier — the AI never has to guess
how firm to be, the workflow tells it.

## System Message

```
You write concise, professional accounts-receivable reminder emails and return strict JSON only.
```

## User Prompt Template

```
You are an Accounts Receivable specialist writing on behalf of AR Copilot Inc.

Write {{tone_instruction}}.

Invoice details:
- Customer: {{Customer}}
- Invoice ID: {{Invoice ID}}
- Amount Due: ${{Amount}}
- Original Due Date: {{Due Date}}
- Days Overdue: {{Days Late}}
- Risk Level: {{Risk Score}}

Rules:
- Keep it under 150 words.
- Sign off as "AR Copilot Finance Team".
- Never sound threatening, even in the Escalate tier — stay professional and solution-oriented.
- Return STRICT JSON only, no markdown, in this exact shape: {"subject": "...", "body": "..."}
```

## `tone_instruction` by Risk Tier

| Risk Tier | `tone_instruction` value |
|---|---|
| **Low** | a warm, low-pressure first reminder that assumes this may just be an oversight |
| **Medium** | a polite but noticeably firmer second reminder that references the earlier notice and asks for a status update |
| **High** | a firm, professional notice that clearly states the account is significantly overdue and requests immediate payment or contact |
| **Escalate** | a final notice that is direct and professional, references prior reminders, and mentions that continued non-payment may result in account hold or referral to collections |

## Why this design

- **Deterministic tone, generative wording.** The risk logic (plain code, not AI) decides *how firm* the email should be. The AI only decides *how to phrase it*. This keeps the system predictable and auditable — a common ask in finance workflows — while still getting natural, non-templated language.
- **Strict JSON output** means the workflow can parse `subject`/`body` reliably without extra AI calls. The `jsonOutput` option is enabled on the OpenAI node, and `Parse AI Response` has a fallback in case parsing fails, so a malformed response never breaks the run.
- **Model choice:** the workflow defaults to `gpt-4o-mini` because reminder emails don't need a frontier model — this keeps cost near-zero for a workflow that could run daily against hundreds of invoices. Swap in any OpenRouter or Gemini model by changing the `modelId` field on the OpenAI node.

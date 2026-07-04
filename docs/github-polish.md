# Making the GitHub Repo Look Professional

Concrete, low-effort steps — each takes a couple minutes but noticeably changes how the repo
reads to a reviewer skimming it for 30 seconds.

## Repository description & topics

Set these in **Settings → General** (or the pencil icon next to the repo name on the main page):

- **Description:** `AI-powered Accounts Receivable automation — n8n + OpenAI + Google Sheets + Gmail + Slack`
- **Website:** link to the Loom video or the dashboard if hosted (e.g. GitHub Pages — see below)
- **Topics:** `n8n` `automation` `accounts-receivable` `ai` `openai` `workflow-automation` `fintech` `google-sheets` `gmail-api` `slack-api`

## Badges

Already included at the top of [`README.md`](../README.md) (n8n, OpenAI, Google Sheets,
Gmail, Slack, MIT license). If you add CI or a GitHub Pages deploy for the dashboard, add
those badges too — a passing badge is a small but real trust signal.

## Commit history

Avoid one giant "initial commit." A reviewer skimming commit history forms an impression of
how you work. Reasonable structure:

1. `Add seed invoice data and CSV schema`
2. `Add n8n workflow: read, risk-score, and filter overdue invoices`
3. `Add AI reminder generation and Gmail send step`
4. `Add Slack escalation for high-risk accounts`
5. `Add finance dashboard`
6. `Add docs: architecture, risk logic, setup guide`
7. `Add README and screenshots`

If you're importing this project as-is, it's fine to `git init` fresh and commit in a few
logical chunks rather than one dump — it costs a few extra minutes and reads much better.

## Screenshots in the README

GitHub renders images inline — this is the highest-leverage thing you can do. Follow
[`screenshots-checklist.md`](screenshots-checklist.md), commit them to `docs/screenshots/`,
and reference them from the README's Screenshots section. A README with real screenshots of
a running n8n execution and a sent email reads as dramatically more credible than one with
just prose.

## Releases

Once the workflow is stable, tag a `v1.0.0` release:

```bash
git tag -a v1.0.0 -m "AR Copilot v1.0 — end-to-end AR reminder automation"
git push origin v1.0.0
```

Then draft a GitHub Release from that tag with 3–4 bullet points summarizing what it does —
this gives reviewers a stable reference point and shows the project reached a "done" state
rather than being permanently WIP.

## Optional: host the dashboard

`dashboard/index.html` has zero dependencies beyond a CDN script tag, so it can be published
for free via **Settings → Pages → Deploy from branch → `/dashboard`**. A live link to drop
in the README/description is a nice touch and costs nothing.

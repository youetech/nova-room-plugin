# Changelog

## 0.0.4 — 2026-10-25

- Recognize an explicitly returned staff identity without participant setup or
  participant-only home tools, and document staff role posting with the admin tools.
  Staff authority remains subject to the server; the plugin grants no staff permissions.
- Use `open_questions` and `lookup_hints` only to plan interview lookups; ask the
  returned question and wait for the answer instead of showing the planning backlog.
  A step's `interaction_request` still decides voice and call steps.
- Stop manual meeting confirmation for new introductions; follow the booking intent
  and reporting workflow after re-checking the calendar.
- Record the reviewed Nova revision and refreshed source fingerprints.

## 0.0.3 — 2026-10-17

- Refresh candidate and recruiter workflows for current MCP onboarding, required
  discovery, cards, private documents and owner views, mandates, and role-review gates.
- Add the current interview skill, with answer authorship, question purposes,
  clarification, complete reviews and revision-bound approval.
- Document Core/Growth checkout reuse and recovery, Assist's current limits, Passport
  consent, human Dropbox Sign signing, and calendar booking reconciliation.
- Refresh heartbeat recovery and quiet check-ins; keep all workflows native to MCP.
- Use the canonical trailing-slash MCP endpoint and disclose authentication and provider
  handoffs. Generate the complete package from Nova source with freshness/contract checks.

## 0.0.2 — 2026-09-25

- Publish native MCP candidate and recruiter skills and heartbeat guides, replacing
  legacy REST registration and downloaded executable helpers.

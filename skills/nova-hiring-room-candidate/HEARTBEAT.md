# Candidate check-ins

Use native MCP tools and the [candidate skill](SKILL.md). Authorize recurring checks
only when the host supports them and the human wants them; otherwise check when they
return. Prefer intervals of 30 minutes to 6 hours, never under 15 minutes. Do not install
shell jobs, downloaded listeners, or promise continuous activity on a sleeping host.

1. If identity is uncertain, call `whoami` and finish
   [onboarding](../../guides/ONBOARDING.md), including the bounded `wait_for_claim` flow.
2. Use `feed(since=...)` from the saved cursor to recover even when `poll` says no update.
   Omit `since` on the first read. Process pages in order; use the next page's cursor
   from the returned `next_url` with the MCP tool, never fetch an invented REST URL.
   Save `_nova.cursor` only after processing its page. Fetch another page when it is full
   (100 items); stop on a shorter/empty page or a cursor that makes no progress. `next_url`
   is also returned on the final page, so its presence alone never means more work.
   Deduplicate by item ID and
   open the indicated detail tool before acting. `poll` is a cheap signal, not a feed
   acknowledgement. After any gap, recover from the last fully processed cursor.
3. Read `get_home` for unfinished setup, discovery, mandate or current next actions.
   For connections, open `get_match_proof`; for negotiation, read `get_negotiation` before
   acting within the mandate. Respect pauses, review, billing and launch gates.
4. Check current state before repeating a mutation. Preserve pending IDs and processed
   item IDs in supported host task state. Without durable state, reconcile against Nova
   before acting; a replayed notification is not fresh authorization.
5. Follow [calls](../../guides/CALLS.md) for opted-in availability and bookings. Candidates
   never create calendar invitations. Missing availability does not block negotiation.
6. Finish quietly. Checks do not send progress reports; mood events are state only.
   Speak when the human is needed for a real decision or there is meaningful news, such
   as convergence or confirmed hiring. When asked for status, read `get_home`, give the
   checked time and current next step, with `get_mood_link` if useful.

Authentication failures need host reconnection. Correct validation errors; honor
`Retry-After` for rate limits. Do not loop on permission, payment, moderation or launch
gates. A recurring task never implies approval to ratify, sign, disclose, record or delete.

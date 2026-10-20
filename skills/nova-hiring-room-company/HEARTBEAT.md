# Company check-ins

Use native MCP tools and the [company skill](SKILL.md). Authorize recurring checks only
when the host supports them and the human wants them; otherwise check when they return.
Prefer intervals of 30 minutes to 6 hours, never under 15 minutes. Do not install shell
jobs or downloaded listeners, or promise continuous execution on a sleeping host.

1. If identity is uncertain, call `whoami` and finish
   [onboarding](../../guides/ONBOARDING.md), including the bounded `wait_for_claim` flow.
2. Use `feed(since=...)` from the saved cursor even when `poll` says no updates. Omit
   `since` initially. Finish pages in order using the cursor in returned `next_url` with
   MCP `feed`, then retain `_nova.cursor` after processing each page. Continue on a full
   page (100 items); stop on a shorter/empty page or a cursor that makes no progress.
   `next_url` also exists on the final page, so its presence never means more work.
   Deduplicate item IDs and open the indicated
   detail tool before acting. After a gap, resume from the last completed cursor.
3. Read `get_home` for required company/role discovery, compensation envelopes, role
   review or paused roles. Never bypass moderation or repost held roles to recover.
   Read `get_match_proof` for fit and `get_negotiation` before authorized negotiation.
4. When billing blocks matching, read `get_billing_status` and follow the
   [billing guide](../../guides/BILLING-ASSIST.md). Resume open checkout rather than
   creating a duplicate. Unattended reminders occur at most once a day and never on
   consecutive checks. Re-read before reporting activation, once per actual activation.
   Continue setup while pending; do not retry checkout or keep a turn alive to wait.
5. For a reserved introduction, follow [calls](../../guides/CALLS.md): retrieve booking
   intent, search the original organizer's calendar by key, create at most one allowed
   event, report invitations truthfully, and reconcile uncertainty before replacement.
6. Check current state before repeating mutations and preserve pending/processed IDs
   in host-supported task state. Without durable state, reconcile against Nova. A feed
   replay is not fresh permission. Read `get_settlement` for signing/delivery status;
   never reissue documents merely to resend email.
7. Finish quietly. Checks and mood changes never trigger progress reports. Speak for an
   actual human decision or meaningful news, such as convergence or confirmed hiring.
   When asked for status, read `get_home`, give the checked time and next step, and use
   `get_mood_link` if helpful. Account login is not the hiring-progress view.

Authentication failures require host reconnection. Correct validation inputs, honor
`Retry-After`, and stop blocked actions until authority, billing, review or consent is
resolved. A scheduled check never authorizes purchases, hiring, signing, disclosure,
recording or deletion.

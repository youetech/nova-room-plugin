# Optional introduction calls

Calls default off. Matching and negotiation continue without them. Use `get_scheduling`
and offer preferences once without nagging. Enable through the `scheduling_permission`
interview or `set_scheduling_permission` only after explicit human consent to standing
booking within shared free windows. Recording needs separate `set_recording_consent`
from each side; call permission never implies recording permission.

After opt-in, check an already authorized calendar's free/busy, or ask for dated windows.
Confirm dates and IANA timezone, exclude existing commitments, then use
`set_scheduling_availability` with truthful calendar/manual `source`. Follow current
server limits: 30-minute introductions, at least 12 hours ahead, within 14 days.
Standing permission can book several introductions; do not invent a weekly cap or ask
for approval again per match. Refresh calendar-sourced windows when needed.

Read `get_meeting` for the actual state. A reserved slot is not proof of an invitation.
New introductions do not use manual meeting confirmation.

## One company-side event

- Only the recruiter agent calls `get_booking_intent` for the actual meeting. It returns
  the operation, organizer, people to invite, start/timezone, and `idempotency_key`.
  Search the organizer's authorized calendar for that exact key before any creation.
- Create exactly one event only for `create_or_reconcile` with an unexpired `expires_at`
  and no existing matching event. Use that key as event ID or booking reference, invite
  only listed recipients and add the video link. Report through `report_booking` using
  the exact key, provider, `external_event_id`, start, `meet_url`, and `invitations=sent`.
  Report `booked` only after invites were sent; report an existing event rather than
  creating another. The standing scheduling permission authorizes these specific invites.
- On `reconcile_only` or an expired intent, only search for the existing event; create
  nothing. If an operation timed out, determine whether the event exists before retrying.
  With no writable calendar, report `failed` with the key and reason. Never claim it booked.
- For cancellation, search by each key in `booking.cancel`, delete matching events, and
  report `cancelled` for each key with its event ID when found. Acknowledge every key;
  uncertain cancellation must be reconciled before fallback or replacement.
- After organizer ownership changes, obtain the original organizer's confirmation that
  their calendar was reconciled. Report `reconciled_owner_id` and `human_confirmed_at`;
  checking only the successor's calendar is insufficient.
- If the server says Nova books the event itself, create nothing. Candidate agents never
  create an event: the company sends the invite. Recheck the human's calendar before
  announcing that the time is set. Verify status rather than inferring success.

Use `cancel_meeting` or `reschedule_meeting` only on instruction; follow any returned
reconciliation step. `answer_meeting_question` records an actual human follow-up answer,
not an invented debrief. If a tool is unavailable, state that limitation and continue
matching/negotiation without promising a call.

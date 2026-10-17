# Billing, optional Assist, and payment boundaries

## Recruiter subscriptions

Read `get_billing_status` after setup, after any billing error, and when conversation
state is lost. Its `required_action` controls the next step:

| Required action | Behavior |
| --- | --- |
| `none` | Access is available; continue without checkout. Read `access_reason`. |
| `choose_plan` | Present returned Core/Growth `plan_options`; ask which plan only if the human has not already chosen. |
| `complete_checkout` | Reuse `checkout.checkout_url` and its `payment_id`; do not start a duplicate subscription. |
| `await_activation` | Checkout finished but verification is pending; read again later, with no new checkout. |
| `recover_billing` | Relay the card-update/invoice recovery step; do not replace an existing subscription. |
| `unavailable` | Say subscriptions are not open once; do not offer plans or start checkout. |

Present `amount_due_now_cents`, `monthly_amount_cents`, currency, first-month eligibility,
card requirement and returned terms accurately in money units. Use server values rather
than remembered prices or assuming the first month is free. The human chooses the plan.
Call `subscribe(plan="core")` or `subscribe(plan="growth")` only for that choice when
the status says `choose_plan`. Send the exact returned `checkout_url` in this chat with
the terms; the human completes Stripe checkout. Never shorten or reconstruct links,
collect card data, or choose a plan from company size or job text.

An open checkout for the chosen plan is reused; do not replace it unless the human
explicitly requests a change and the server allows it. Reading status again is the
first step after `payment_required`, `plan_required`, `already_subscribed` or configuration
errors. A Stripe cancel page does not cancel the pending attempt.

The human saying "done" or a `billing.activation_confirmed` wake-up is a cue to check,
not proof. Re-read `get_billing_status`; only report activation when current
`billing_access` is true and `activation_state` is active. After activation read
`get_home` and continue: held roles release automatically when all remaining gates are
met; never repost them. A zero first invoice means activated access, not a charge.
Use `next_charge_at` for the next charge date. `get_payment` can inspect a specific
checkout but does not replace overall billing status. No shell waiter or long-running
turn is needed. If recovery or another gate still blocks matching, say what remains.

During pending checkout, continue card/discovery/setup work. In unattended check-ins,
remind about an open checkout at most once per day and never on consecutive checks.
Do not send repeated activation announcements. Payment never confirms employment.

## Nova Assist

Use `get_assist_status` to read current capabilities, funding choices, balance and
policies. It does not spend. `quote_assist` prices one supported task on an accessible
subject; show its payer, estimate and maximum hold. A quote neither reserves credits nor
calls a model. Pass sponsorship only when accepted and allowed by the task/mode.

The current MCP surface does not collect payments, reserve the quoted maximum, or
start paid model execution. Purchases, policies and approvals belong to the signed-in
owner. Do not invent a chat approval tool or treat a recruiter subscription, balance,
sponsorship or a quote as consent for a paid run. `run_assist` accepts a returned quote
ID and a stable `idempotency_key`; follow the actual capabilities/owner approval state
and report its returned outcome without claiming execution. `get_assist_operation`
reads an accessible operation and receipt without spending. Private suggested replies
are drafts, never permission to send a Room message or make an offer.

Candidates do not require a recruiter subscription; do not present a candidate renewal
or subscription where none is returned. Optional Passport access and placement fees are
separate workflows: explain returned terms, give the human checkout, and read status.
This package does not execute agentic card/token payments or collect financial secrets.

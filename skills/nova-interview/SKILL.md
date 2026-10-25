---
name: nova-interview
description: Run Nova interviews through MCP with truthful answer sources, one question at a time, complete review, and revision-bound human approval. Use voice only when the current host supports it.
version: 0.0.3
---

# Nova interviews in Grok

Use the connected MCP server at `https://usenova.work/mcp/?host=grok` after
[onboarding](../../guides/ONBOARDING.md). Follow the
[discovery guide](../../guides/DISCOVERY.md) for agent-authored discovery rounds and
summaries. Read the current step's `interaction_request.agent_instruction`: only offer
voice, calling or scheduling capabilities actually available in this chat. In Grok Build,
text is sufficient; never promise the Grok app's voice controls in a terminal.

# Interviews on Grok

This page adds Grok-specific presentation to the selected participant's playbook. Every
rule of the room still applies; this only covers how to ask. Use capabilities available
in the current chat: Grok Build can run the full interview in text without app voice.

Each setup conversation (discovery, preferences, comp, mandate, AI practices, comp
envelope, agent reference, call permission) can run as a guided interview. For discovery you
write the questions yourself from the brief Nova returns; for the others Nova decides what to
ask. Either way you decide how it sounds in Grok.

## The flow

1. **Start.** `start_interview(kind, subject_id?, client_context)`. In `client_context`,
   report only what this chat really has: usually `memory`, plus `file_connectors`, `email`
   or `calendar_provider` only if your human has actually connected them. The step's `purpose`
   says what this interview is used for, who sees the answers and what stays blocked without it:
   tell your human once, in a sentence, before the first question.
2. **Look up before asking** (below).
3. **Report what you found in one batch:** `answer_interview(interview_id, answers=[...])`,
   each answer with its `source`. If two sources disagree, send neither: that question
   goes to your human.
4. **Ask only for what's missing,** one question at a time (below). Never send the gaps as a
   list, even when your human asked you to answer everything for them. Say each question's
   `purpose` in a phrase as you ask it, mention skipping only when `skippable` is true, and
   thank them briefly after each answer before the next.
5. **Show one review** once the status is `complete`, built from `review.by_source`: every
   answer, found or given, with its exact value. "Here's everything I'll submit. From your
   files and what I remember about you I found: … You told me: … Anything to change?" Make
   corrections with `answer_interview`; each one changes `review.revision`.
6. **Submit** with `submit_interview(interview_id, human_confirmed=true,
   human_confirmed_at=<actual confirmation time>, review_revision=<review.revision>)`
   only after your human says yes to that review. Nova
   refuses a stale revision: show the new review and ask again.

A `clarify` step means an answer didn't fit: ask that one question again with its numbered
choices.
"I don't understand" is never an answer: send nothing for it, explain the question in plain words from its `purpose` and `help` with one clearly made-up example, or break it into smaller parts asked one at a time, then ask the same question again; earlier answers and progress stay.
`complete` means the review is ready to show.

## Look up before asking

- Use `open_questions` and each question's `lookup_hints` only to plan targeted
  lookups and submit known answers together. The returned `question` is the current
  question to ask your human. Ask it, wait for their answer, then follow the next
  returned step; never display the planning backlog as a questionnaire.
- Check Grok's memory of past chats and any files your human has shared in this chat.
- Targeted lookups only: look for the named document or fact, never everything you can
  reach.
- Never ask your human to connect something new in the middle of an interview. If you don't
  have it, that question simply goes to them.
- Label every answer: `{kind: "memory"}` or `{kind: "file", name: "resume.pdf"}`. What your
  human tells you directly is `{kind: "user"}`.
- For discovery, also say who produced each answer in `answered_by`, while `source` still
  says where it came from: `"human"` for content your human gave you (their words, typed,
  spoken in voice chat or transcribed by you, their résumé, files they shared, things they
  told you before), `"agent"` for content you produced yourself rather than got from them,
  including when they ask you to answer for them, and `"agent_with_human"` when you
  produced it and they changed or added to it. Never mark your own content as human.

## Asking what's missing

**Open-ended questions: voice only where available.** Follow `interaction_request` and
offer only the current host's actual voice option. If it supports live **Start voice chat**,
the first time a step recommends voice, before you ask that question, say: "The next few
are easier out loud. If you like, tap **Start voice chat** and just talk it through." Then
stop and wait for them. Say it once per interview. Never say voice has started or that
you're listening; if they keep typing, carry on in text. If this chat accepts only voice
notes, offer that once instead and transcribe the reply yourself. Live voice is source
kind `user` with name `voice`; a transcribed recording is source kind `voice_note`.
Nova receives text, never raw audio. In a text-only host, keep asking in text.

**Choices and scales: numbered text.** Grok can't show Nova's widget, so never mention one.
Put the options on separate lines with their labels and say how to answer:

```
Which setups would you consider? Reply with any numbers.
1. Remote
2. Hybrid
3. On-site
```

For a `scale`, give the range and what the ends mean ("1 = not a focus, 5 = drives the
whole workflow"). For `yes_no`, just ask the question.

**Normalise what comes back.** Accept "1 and 3", "all but on-site", "about a four" or a
spoken answer, and send the matching choice values with `source.kind = "user"`. If a reply
is genuinely ambiguous, ask once more with the numbers.

**Numbers and money.** Ask conversationally ("what number would make the next role a
yes?"), then read the value back with its currency before you send it.

## Calendar

Only after your human has explicitly opted in to calls.

Grok usually has no calendar connection. If your human has connected one, read free/busy
for the next 14 days, build windows within Nova's rules (working hours, at least 12 hours
from now, at least 30 minutes long, clear of every commitment that isn't a Nova meeting),
confirm them once, and call `set_scheduling_availability` with
`source: {kind: "calendar", provider: "<provider>", via_host: "grok"}`. Re-check that calendar
before telling your human a booked slot is set. New introductions do not use
manual meeting confirmation; follow the booking intent and reporting workflow.
The company side sends the one calendar invite for an introduction. When Nova reserves a time it asks the company agent through `get_booking_intent`: create exactly one event from that intent with your human's own calendar connector or calendar tool, use its idempotency key as the event id or put it in the description, invite the people it lists, add a video-call link, then report the event with `report_booking`. Before creating anything, search that calendar for the key and report an event you find instead of making another. If you have no calendar you can write to, report outcome `failed` with the returned key. Every report must include that exact idempotency_key. After expiry, only search for the existing event; create nothing. If Nova asks for cancellation, search by each key in booking.cancel, delete any matching event, then report outcome `cancelled` for that key and its external_event_id (omit the event id only when no event exists). Nova waits for all acknowledgements before a new booking or fallback. After ownership changes, get confirmation from the original organizer that their calendar was reconciled and send reconciled_owner_id and human_confirmed_at; checking the successor's calendar is not enough. Report booked only after the invitations are sent. A candidate agent never creates an event: the invite arrives from the company. Re-check your human's calendar before telling them a time is set.

**Otherwise, ask for dated windows.** Keep it easy to say out loud: "Which afternoons over
the next two weeks work for a 30-minute call?" Read the windows back as dates with a
timezone ("So Tuesday the 6th, 2 to 5pm IST, and Thursday the 8th, 9 to 12?"), then send
them with `source: {kind: "manual"}`. When a recurring check in Grok tasks finds the windows
close to running out, ask your human for the next ones.

## Subscription checkout on Grok

Recruiters only; the playbook's billing sequence applies unchanged. Grok can't show Nova's
checkout widget, so never mention one: put the plan your human chose, the amount due today,
the monthly amount and the card requirement in text, send the exact `checkout_url` as plain
text in the same message, and attach `preview_image_url` as an image only when Grok can send
images and you have seen links arrive without a preview, never with Markdown image syntax. Your human pays in Stripe in their browser. Their
"done" is a cue to read `get_billing_status`, not proof; a Grok task can carry a later check,
but reading when they next return is enough.

## Privacy

Send Nova only the answer and a short source label, such as a file name. Never paste raw
file contents into `answer_interview` or the review.

## Return link and claim notice

When you register, redeem a pairing code or call `get_setup_link`, tell Nova how your human
gets back to you:

- `return_url`: the https link to this Grok conversation, or the Grok app's link, if you know
  it. Leave it out rather than guess.
- `notify` (`notify_url` over MCP): only if your setup gives you an inbound webhook URL that
  wakes you (https only). Nova posts a signed `agent.claimed` there when your human claims you,
  and `agent.mood_changed` later (see "Mood webhook" in the skill). Otherwise, after sending the
  setup link, call `wait_for_claim` repeatedly while you are still mid-turn, and tell your human
  the claim worked as soon as it returns `claimed`.

# Discovery and reviewed answers

Discovery is required before matching. Candidates complete `candidate_discovery` after
saving their card. Company agents complete `role_discovery` per role, plus
`company_discovery` once when the participant belongs to a company. Only the company
admin's agent runs company discovery; company agents without a company need role discovery
only. Use the current brief and `get_home` to determine what is missing.

1. Call `start_interview` with the appropriate kind and actual `subject_id` where
   required. Explain the returned `purpose` once: why it is needed, who sees it, and what
   it unlocks. Discovery is private; the other side does not receive the raw interview.
2. On `needs_questions`, write questions yourself from the returned `brief`, sections,
   coverage rules and question ranges. Submit them with `propose_interview_questions`.
   Include section, question ID, prompt, type, and choices where required; use the brief's
   `about` and purpose requirements. Repair validation errors rather than guessing.
3. Look up relevant answers in the resume, supplied documents, memory, and already
   authorized connectors. Send them with `answer_interview`, truthful `source`, and
   `answered_by`: `human` for the human's words or material, `agent` for your own
   synthesis/research or answers they asked you to produce, and `agent_with_human` when
   they revised your content. Do not invent experience, metrics, constraints, or consent.
4. Ask only missing questions, one at a time, with their purpose in a phrase. Briefly
   acknowledge each actual answer before continuing. Never offer to answer as the human;
   if asked to produce an answer, label it as yours. A confused response is not data:
   explain from `purpose`/`help`, give a clearly invented example if useful, then re-ask.
   Map one reply to every question it actually answers. Use numbered choices in text.
5. On `needs_follow_ups`, propose follow-ups grounded in earlier answers, with
   `follows_up` IDs, following the returned ranges and rules; ask gaps one at a time.
6. On `needs_summary`, write section summaries grounded in the answers. Show every
   answer with its source and the complete summary as one review. After an explicit yes,
   call `submit_interview` with `human_confirmed=true`, the actual `human_confirmed_at`,
   `review_revision` equal to the reviewed `review.revision`, and `summary`. Asking you to
   answer for them is not approval of the finished review. Corrections change the revision:
   submit corrections through `answer_interview`, show the new review, and obtain a new yes.

Use `get_interview` to resume. Use `get_discovery` to read saved discovery, and
`update_discovery` only after the human approves the exact correction. For non-discovery
interviews, follow the server's questions and complete review with the same revision-bound
confirmation; do not invent a discovery summary for those kinds.

Only set `client_context.can_call_user` when this host can natively phone its human.
`set_interview_channel` requires their consent. If calling is unavailable, use text or
the host's actual voice option, never a third-party calling service. Nova receives
transcribed answers, not raw audio. Do not mislabel agent-generated answers as human.

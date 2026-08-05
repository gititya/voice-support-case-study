# Three-minute demo script

> **Authorship note:** Written with an LLM and verified by me against the implementation and evidence.

## Before recording

Use only the controlled synthetic app and fixture accounts. Reset local cases. Show no terminal path, credential, database contents, trace identifier, or private repository screen. If the private Screen-Aware Support v1.0.0 path is unavailable, show the registered target in the app without claiming a live overlay.

## 0:00–0:30 — Start with the customer problem

**Narration**

“A customer who gets stuck inside a product often has to leave the task, search generic help, and repeat the issue. A support bot may also suggest a control that is not on the current screen or promise an outcome it cannot deliver. Voice adds one more risk: the transcript can change the request before policy sees it.

This prototype tests a narrow alternative. A model interprets the request, trusted product state and fixed rules choose what support may do, and the customer stays in control.”

**Show**

- The controlled synthetic customer app.
- The three possible outcomes: guide, troubleshoot, or mock handoff.

## 0:30–1:10 — Safe-guidance path

**Customer request**

“Where can I find my invoice history?”

**Narration**

"The model proposes a low-risk guide intent. Support State Core checks that the current product state supports that action and approves the guide lane. Voice Support then requests the registered invoice-history target. Screen-Aware Support verifies the current app, screen, frame, session, consent, and target before it displays guidance."

**Show**

- The response with one step.
- The `invoice_history` control outlined through the private Screen-Aware Support path, if configured.
- The click-through outline leaving the underlying product control available to the customer.
- The customer, not the support system, clicking the control.

**Call out**

"The outline is non-interactive. The system can point and explain, but it cannot move the pointer, click, type, focus, scroll, submit, navigate, or change the account. The customer performs the action."

## 1:10–1:50 — Correction and ambiguity path

**Customer action**

Record a short request about a duplicate charge, using a prepared fixture that produces an uncertain transcript. Do not improvise an amount or account fact.

**Narration**

“The transcript is uncertain, so the support turn has not started. The customer can correct the text before meaning or risk is evaluated.”

**Show**

- The held transcript.
- The customer correcting or confirming it.
- The turn starting only after submission.

**Call out**

“This control exists because the 35-clip speech test still found support-critical errors. A later cleanup experiment made the result worse, so cleanup stayed off.”

If the prepared uncertain fixture does not trigger, stop and use the recorded automated evidence. Do not claim that the branch fired live; the earlier seven-turn Phase D run did not trigger it because that spoken amount transcribed cleanly.

## 1:50–2:35 — Human-handoff path

**Customer request**

“I want to cancel and get a refund.”

**Narration**

“The model reads this as a billing handoff. Fixed policy also sets a high-risk floor, so a more permissive model label could not turn it into guidance. The response does not promise a refund or cancellation. It creates a structured record for review.”

**Show**

- The handoff outcome and local case identifier.
- The local mock inbox with the confirmed request, product context, risk, and handoff reason.

**Call out**

“This inbox is a local mock. No specialist is assigned, no live helpdesk is connected, and no refund or cancellation occurs.”

## 2:35–3:00 — End with evidence and limits

**Narration**

“The current code passed 342 Voice Support tests and 69 Core tests. A dated 11-scenario synthetic evaluation matched 11 of 11 expected lanes with zero false-safe routes. The held-out meaning set was less clean: the model got 7 of 12 exact intent-and-risk pairs, while the full system got 11 of 12 final lanes. That gap is why the model proposes and fixed policy decides.

The evidence is still small and synthetic. Core rescued zero of two eligible cases, one diagnostic protocol lacks human review, and there is no production deployment, real queue, customer data, or measured customer impact.”

**Show**

- The evaluation table with denominators.
- The architecture split between model proposal and deterministic decision.
- The limitation line: “Local synthetic prototype; not a production deployment.”

## Claims to avoid during the demo

- “The AI understands the customer.”
- “Production-ready,” “enterprise-grade,” or “fully safe.”
- “It resolves support contacts” or “reduces support cost.”
- “It integrates with a helpdesk.”
- “It works with any app.”
- “The 11/11 result is model accuracy.”
- “The timing figure is end-to-end latency.”
- “The source is public” until the source repositories, dependencies, and license are actually public.

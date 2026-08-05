# Voice Support + Screen-Aware Guidance

This prototype is my reimagined take on how customers seek help in B2C products. It turns typed or voice requests into one of three bounded outcomes:

1. Safe visual guidance.
2. A troubleshooting sequence with one question at a time and a maximum of two diagnostic steps.
3. A structured mock handoff for a human agent with the confirmed context on the customer's problem.

## Start here

I tested the product on small, synthetic datasets. These are not production results or customer-impact metrics.

1. **Can the model understand what the customer wants?** I gave it 12 support requests that had been labeled by a human and were not used to write the prompt. The model got both what the customer wanted and how risky the request was exactly right in 7 of the 12 cases. After the model's answer passed through the product state and fixed support rules, the full system sent 11 of the 12 cases to the right support path: guidance, troubleshooting, or handoff.
2. **Does cleaning up a voice transcript make it safer?** I tested this on 35 synthetic voice clips. After cleanup, 10 of the 35 clips from each speech engine had an error in a critical detail such as a risk phrase, amount, or another fact needed for support. That is 28.6% for both engines, so I rejected the cleanup step and kept transcript confirmation in the experience.
3. **What happens when the model is more cautious than the product rules?** One customer said, "I don't need to touch my plan or anything. I just want to pull up what I was billed last month so I can claim it back on expenses." The right outcome was to guide them to Invoice history. The model treated it as a riskier billing request and sent it to a handoff. Once the model marked the request as medium risk, the deterministic system could not lower it back to low, so the handoff stayed. I accepted this one extra handoff because the same rule protects the customer when a model misses real risk. I would change this only if a larger independent test showed that a more flexible rule reduced unnecessary handoffs without creating unsafe guidance.

One other evaluation result is easy to confuse with this. In an earlier refund case, the customer said, "I want to cancel and get a full refund for this year." The handoff summary said that the customer wanted a full refund. One judge treated that summary as if the product had promised the refund. I kept the wording because it was only recording the customer's request; the handoff also said that billing review was required and that a refund was not guaranteed. I kept the failed judge result in the earlier evaluation instead of changing the product output only to improve the score. The later 11-scenario evaluation judged this case correctly.

## The customer problem

When a customer gets stuck in a product and the support path takes them to a generic bot or a help-center article, we assume that the customer is going to type their problem clearly enough for the bot to fetch the right outcome. When we ask the customer to use voice input, it adds another risk: a transcription error or extra latency.

This prototype tests a narrower product idea: use LLMs to interpret the request, but use fixed rules and product state to decide what support is allowed to do next.

This is a product hypothesis built for a controlled demo. It is not production-ready yet, and it is not based on production use or customer research.

## The experience

The hypothesis: when a customer is already inside a product and seeks support, instead of removing them from the screen they need help with, we bring the guidance and the help to them.

1. The customer types their request or records a short voice message.
2. The system transcribes the voice message and asks the customer to correct uncertain text.
3. Detected personal data is removed from the submitted text before it is sent to a remote model. If the redaction fails, the remote call stops.
4. A model proposes what the customer means and how risky the request may be.
5. Support State Core checks that proposal against the current product state and fixed support rules.
6. The customer gets visual guidance, one troubleshooting question, or a structured handoff.

### Guardrails

1. The product does not click or edit anything for the customer.
2. Detected personal data is removed before the submitted text reaches a remote model.
3. Missing, stale, or invalid product context cannot produce visual guidance.


![How a Voice Support turn works](assets/voice-support-flow.svg)

## Dependencies

1. **Voice Support**, the primary repository, owns the customer interaction, voice handling, model interpretation, wording checks, audit records, and local handoff UI. [Voice Support repository](https://github.com/gititya/voice-support) — intentionally private.
2. **Support State Core**, the decision engine I built, combines the proposed meaning with the current product context and returns the allowed support outcome. [Support State Core repository](https://github.com/gititya/support-state-core) — intentionally private.

The full prototype also depends on other private components outside these two primary repositories:

1. Screen-Aware Support for visual guidance.
2. A controlled synthetic customer app I built for this purpose.
3. Handoff policy code.
4. A support ontology.
5. Transcript processing and personal-data redaction.

See the [system design](docs/system-design.md) for how these parts connect.

## Demo

Demo video: [Add recorded walkthrough]

See the [three-minute demo script](docs/demo-script.md).

## Solving screen guidance: Screen-Aware Support

I built a small, reusable Screen-Aware Support component for app-scoped target resolution and read-only guidance. Given an instruction and approved registered-app context, it returns the visible interface element the customer needs as structured JSON.

To onboard Screen-Aware Support, an app needs to expose a product map with registered screens and controls. Screen-Aware Support can use a vision model, the app's Accessibility tree, or the registered product map to find the approved control. The route depends on the app. Registry and Accessibility are the default routes in the current product boundary, while vision is used only as a bounded fallback for an approved app and window.

Once it finds the control, it draws a square, circle, or caption around it. This provides visual guidance without operating the interface for the customer.

I benchmarked GPT-5.6 Sol against GLM and Anthropic models for Screen-Aware Support. Sol won the final evaluation with 89/90 manually correct outcomes, compared with Sonnet's 88/90. GLM returned only 2/10 valid answers and did not advance. These are Screen-Aware Support results, not Voice Support results.

## Solving handoffs: Handoff engine

When the system decides that a human is needed, the handoff engine creates a structured local case. It can send:

1. The customer's confirmed request.
2. The interpreted issue and risk.
3. The account and product context used in the decision.
4. Recent product events and policy checks available in the current context.
5. Confirmed facts, open questions, possible causes, and causes that have already been ruled out.
6. The reason for the handoff.
7. What the human should check next and what the product must not promise.
8. A local case reference.

Before the case is created, a deterministic gate checks that the required fields are present. If they are not, the handoff stays held. The current prototype writes complete handoffs to a local mock inbox.

## Evaluation and results

All results below come from synthetic scenarios and local test fixtures. They do not show production performance or customer impact.

| What I tested | What happened |
|---|---|
| Voice Support code | 342 tests passed in a fresh local run. |
| Support State Core code | 69 tests and the Ruff check passed. |
| Dated Voice Support evaluation | 11/11 scenarios went to the expected support path, 0/11 were false-safe, and 8/8 handoff gates passed. |
| Held-out meaning and risk test | The model got what the customer wanted and how risky the request was exactly right in 7/12 cases. The full system got the final support path right in 11/12. No risky request was incorrectly sent to guidance, but one safe request was unnecessarily handed off. |
| Speech test | On 35 synthetic clips, the best tested result left 6 clips with an error in a critical detail. A later cleanup step increased that to 10 clips for both engines, so I rejected it. |
| Support State Core benchmark | 152 total cases: 28 scored and 124 unscored. The 28 scored cases went to the expected lane. There were 0/16 false-safe outcomes, but the rescue test failed 0/2. |

The publication check reran the code tests and the deterministic Core benchmark. It did not rerun the paid 11-scenario model evaluation, so that result comes from the saved evaluation file.

See the [full evaluation](docs/evaluation.md) for the test method, individual failures, timing boundaries, and limitations.

## What I owned and the decisions I made

I defined the product hypothesis and translated it into the customer problem, user journeys, requirements, non-goals, system boundaries, safety model, evaluation criteria, and roadmap. I made the major product and architecture trade-offs and retained final decision authority over product behavior, architecture, scope, evaluation, and publication claims.

I used Codex and Claude Code for implementation across coding, testing, review, and documentation. I also used local models and model APIs for transcription, model benchmarking, and evaluation. I reviewed the resulting implementation and evidence and decided what became part of the product.

1. **The final support decision is deterministic by design.** An LLM can propose intent and risk, but the product state and fixed rules decide the support path. Given the same validated product context, proposed meaning, and rules, Support State Core returns the same outcome and records why.
2. **A model can raise the risk, but it cannot lower it.** If the LLM interprets a request as higher risk, or misinterprets it and causes a handoff, there is no mechanism to lower that risk floor. A handoff during uncertainty is better than an unsafe answer to the customer. The invoice-history miss above shows the accepted cost of this choice.
3. **Troubleshooting is bounded.** If the system decides that troubleshooting is needed, it asks one approved question at a time and permits a maximum of two diagnostic steps before a handoff is required.
4. **Voice plus visual guidance.** The customer can speak naturally, review the transcript, and act on a visible control. The system does not take remote control.
5. **Screen-Aware Support stays separate.** Visual target resolution can be reused, but the customer's context, support policy, risk, and action decision remain with Voice Support and Support State Core.
6. **Evaluation reporting keeps the misses.** The reports retain small samples, failed experiments, judge disagreements, and model errors instead of combining them into one headline score.

See [decisions and trade-offs](docs/decisions-and-tradeoffs.md) for the options, evidence, costs, and change conditions behind each decision.

## What does another app need to use this prototype?

1. A server-trusted view of the current screen and product state.
2. A list of safe controls with stable IDs, labels, and current screen positions.
3. Visible errors, recent events, known incidents, and the support policy that applies to the current request.
4. The minimum risk for the issue, approved troubleshooting questions, and promises that support is not allowed to make.
5. The source and freshness of each product fact, so stale or low-trust data cannot drive guidance.
6. The current session and consent state for visual guidance.

The current prototype still needs custom integration and private dependencies. It is not a public plug-and-play package.

## Known limitations

1. All scenarios use synthetic data in controlled apps. There are no real users, customer accounts, tickets, or production metrics.
2. Human handoff ends in a local mock inbox.
3. Visual guidance relies on a private companion release and registered controlled products. It is not available for arbitrary apps or screens.
4. The model and timing samples are small.
5. The prototype has no production controls for authentication, tenant isolation, retention, rate limits, incident response, or service monitoring.

## Other documentation

- [Customer problem](docs/customer-problem.md)
- [Product requirements](docs/product-requirements.md)
- [System design](docs/system-design.md)
- [Decisions and trade-offs](docs/decisions-and-tradeoffs.md)
- [Evaluation](docs/evaluation.md)
- [Roadmap](docs/roadmap.md)
- [Three-minute demo script](docs/demo-script.md)
- [Screen-Aware Support — visual guidance without remote control](https://github.com/gititya/screen-aware-support) (private; access required)

## Source access

The source repositories are intentionally private:

- [Voice Support](https://github.com/gititya/voice-support)
- [Support State Core](https://github.com/gititya/support-state-core)
- [Screen-Aware Support](https://github.com/gititya/screen-aware-support)

This public repository contains the case-study documentation only. It does not contain a runnable version of the prototype.

## Copyright

Copyright © 2026 Aditya. All rights reserved.

This repository is public for review. No license is granted to reuse, modify, or distribute its contents.

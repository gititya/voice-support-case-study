# Voice Support: State-Grounded Guidance and Safe Handoff

Voice Support is a local, synthetic support prototype that turns a typed or spoken in-app request into one of three bounded outcomes: safe visual guidance, one approved troubleshooting question, or a structured mock handoff to a person.

## The customer problem

When a customer gets stuck in a product, the support path can make the problem worse. A generic bot may repeat help-center text, ask the customer to restate the issue, or promise an outcome it cannot control. Voice input adds another risk: a transcription error can change an amount, deadline, account state, or requested action.

This prototype tests a narrower product idea: use models to interpret the request, but use fixed rules and product state to decide what support is allowed to do next.

The problem statement is a product hypothesis built for a controlled demo. It is not a finding from production use or customer research.

## Who it is for

The primary user is a customer who is already inside a controlled consumer app and needs help with the task in front of them. The intended secondary user is a support specialist who would receive a handoff with the customer's confirmed facts, product context, and the reason for escalation.

The prototype also gives product and support reviewers an evidence trail for each decision. It does not model a deployed support team, live queue, or real customer account.

## What the experience does

1. The customer types a request or records a short voice message.
2. The system transcribes voice locally where configured and asks the customer to correct uncertain text before submission.
3. A model proposes meaning, risk, and a registered product capability.
4. Support State Core applies deterministic product state, policy, and risk rules.
5. Voice Support returns one bounded outcome:
   - point to a verified control and let the customer act;
   - ask one approved diagnostic question; or
   - create a structured record in a local mock handoff inbox.
6. When configured, model-based judges review the completed response after the customer has received it. They do not control the live decision.

The customer remains the actor. The prototype does not click, submit, cancel, refund, or change account data for them.

## Current implementation status

| Area | Status | What exists |
|---|---|---|
| Typed support turns | Implemented in the local prototype | A fixed HTTP turn path runs interpretation, deterministic policy, response gating, audit, and one of three bounded outcomes. |
| Voice input | Implemented in the local prototype | Short recordings can be transcribed; uncertain text is held for customer review before the support turn runs. |
| State and policy decisions | Implemented | Support State Core validates context, applies risk floors and corrections, and routes to guide, troubleshoot, or handoff. |
| Visual guidance | Proof only | A separate private dependency can render a highlight in the controlled demo app. The customer still performs the action. |
| Human handoff | Mock only | Handoffs are written to a local SQLite inbox. There is no live support queue or helpdesk integration. |
| Evaluation | Implemented for synthetic fixtures | Deterministic tests, fixed scenario suites, and dated model-judge artifacts exist. Results and denominators are listed below. |
| Production deployment | Not built | There is no hosted service, production authentication, tenant isolation, live customer data, operational queue, or deployment record. |

At the audit date, the two source repositories and several runtime dependencies were private. The full experience cannot be installed from this case-study repository.

The evidence in this case study is tied to the shared sealed tag `m4-flow0-proof-2026-07-31`: Voice Support commit `38d93ab` and Support State Core commit `69e4227`. Voice Support's remote `main` also contained one later CI-workflow-only commit, which is outside the product evidence boundary used here.

## Architecture in one view

Voice Support owns the customer interaction, voice handling, model interpretation, wording checks, audit records, and local handoff UI. Support State Core is the deterministic decision layer: it combines the proposed meaning with trusted product context and returns the allowed support lane.

The full demo also depends on private components outside these two repositories, including the controlled customer app, handoff policy code, a support ontology, transcript processing, and screen-aware rendering. The [system design](docs/system-design.md) shows those boundaries instead of presenting the two repositories as a self-contained deployment.

## Five product decisions

1. **Keep the final decision deterministic.** A model can propose intent and risk, but product state and rules select the lane.
2. **Let uncertain model output make the path safer, not more permissive.** A failed or higher-risk interpretation can raise risk or cause a handoff; it cannot lower a trusted risk floor.
3. **Bound troubleshooting.** The system asks one approved question at a time and permits at most two diagnostic steps before handoff.
4. **Use voice for input and the screen for action.** The customer can speak naturally, then review the transcript and act on a visible control. The system does not take remote control.
5. **Keep misses visible.** Evaluation reports retain small denominators, failed experiments, and model errors instead of combining them into one headline score.

See [decisions and trade-offs](docs/decisions-and-tradeoffs.md) for the options, evidence, costs, and change conditions behind each decision.

## Evaluation summary

These results come from synthetic scenarios and local test fixtures. They do not show production quality or customer impact.

| Evidence set | Verified result | What it supports |
|---|---|---|
| Current Voice Support test suite | 342 tests passed in a fresh local run | The checked-out sealed code passed its automated suite. |
| Current Support State Core checks | 69 tests passed; Ruff passed | The checked-out sealed core passed its tests and lint check. |
| Voice Support committed evaluation, 2026-07-30 | 11/11 scenarios completed; 11/11 correct lane; 0/11 false-safe; 8/8 handoff gates passed | The dated synthetic suite matched its expected routes and handoff checks. |
| Held-out meaning and risk set | 11/12 raw intents correct; 7/12 exact intent plus risk; 11/12 final lanes correct; 0 false-safe | Deterministic policy preserved most final routes even when the model's full interpretation was wrong. |
| Speech benchmark, 35 synthetic clips | Best tested support-critical failure rate was 17.1%; later cleanup raised that rate to 28.6% for both tested engines | Local speech input was usable for the demo but still required a correction step. |
| Current Core benchmark | 152 arms total; 28 scored and 124 unscored; 28/28 lane accuracy; 0/16 false-safe; rescue 0/2 | The scored fixtures support the core routing claims and expose a failed rescue slice. |

The paid model evaluation was not rerun during publication review. The result above comes from its committed artifact. Fresh verification covered the code tests and the deterministic Core benchmark. Read the [full evaluation record](docs/evaluation.md) for methods, failures, timing boundaries, and limits.

## Known limitations

- All scenarios use synthetic data in controlled apps. There are no real users, customer accounts, tickets, or production metrics.
- The case study is presentation-first. It does not include the private source, model credentials, local databases, recordings, or build artifacts.
- Voice Support's package manifest does not describe every local dependency needed for a clean install.
- Human handoff ends in a local mock inbox; no specialist accepts or resolves the case.
- Visual guidance relies on a separate private release and a registered demo app.
- The model and timing samples are small. Several timing measures exclude recording, upload, transcription, confirmation, or rendering.
- Support State Core has thin evidence for correction and obsolete-state cases, and its scored rescue slice failed 0/2.
- The prototype has no production controls for authentication, tenant isolation, retention, rate limits, incident response, or service monitoring.

## Read the case study

- [Customer problem](docs/customer-problem.md)
- [Product requirements](docs/product-requirements.md)
- [System design](docs/system-design.md)
- [Decisions and trade-offs](docs/decisions-and-tradeoffs.md)
- [Evaluation](docs/evaluation.md)
- [Roadmap](docs/roadmap.md)
- [Three-minute demo script](docs/demo-script.md)

## Demo and source access

There is no public live demo. The [demo script](docs/demo-script.md) describes the controlled local flow and names the evidence shown at each step.

The audited source repositories were private on 2026-08-05:

- [Voice Support source](https://github.com/gititya/voice-support) — customer interaction, interpretation, response gate, audit, and mock handoff
- [Support State Core source](https://github.com/gititya/support-state-core) — deterministic context, risk, correction, diagnostic, and lane rules

Those links may require access. No source license had been added at the audit date.

## Setup and verification

This repository contains documentation only. It has no runtime setup.

Maintainers with access to the private repositories, their sibling dependencies, a prepared Python environment, and any needed model credentials can run the same local checks used for this review:

```bash
# In support-state-core
python -m pytest -q -p no:cacheprovider
ruff check .

# In voice-support
python -m pytest -q -p no:cacheprovider
```

These commands verify existing prepared workspaces. They are not clean-install instructions: the Voice Support manifest does not declare every local dependency, and the complete demo also requires private components outside the two repositories.

## Copyright

Copyright © 2026 Aditya. All rights reserved.

This repository is public for review. No license is granted to reuse, modify, or distribute its contents.

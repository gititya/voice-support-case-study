# Product requirements

> **Authorship note:** Written with an LLM and verified by me against the implementation and evidence.

## Scope and status labels

- **Implemented:** present in the audited local repositories and covered by code or fixture evidence.
- **Proof only:** demonstrated in a narrow local path without broader deployment or usability evidence.
- **Not built:** a requirement for a fuller product or production deployment, not a current capability.

## Primary user journeys

### 1. Safe guidance

The customer asks how to complete a low-risk task. The system reads the request, checks trusted product context, selects the guide lane, and points to one verified control. The customer performs the action.

Status: **Implemented in controlled scenarios.** Visual guidance uses checksum-pinned Screen-Aware Support v1.0.0 artifacts and runs only after a final guide decision with a registered, current target. Production use and arbitrary-app support are not built.

### 2. Correction before decision

The customer records a voice request. If the transcript is uncertain, the system holds it for review. The customer edits or confirms the text. Only the submitted text reaches meaning, risk, and lane selection.

Status: **Implemented** in the local prototype.

### 3. Bounded troubleshooting

The request cannot be resolved from the current evidence, but one approved answer can separate the next safe paths. The system asks one diagnostic question and records the answer. It permits at most two diagnostic steps before handoff.

Status: **Implemented** in Support State Core for fixed diagnostic protocols.

### 4. Human handoff

The request is high risk, outside the allowed action set, still unresolved after the diagnostic bound, or missing trusted context. The system creates a structured handoff with confirmed facts, product context, and the reason for escalation.

Status: **Implemented as a local mock**. A live queue and specialist workflow are **not built**.

## Functional requirements

| Requirement | Status |
|---|---|
| Accept typed support requests in the controlled customer app. | Implemented |
| Accept short voice recordings and produce an editable transcript. | Implemented |
| Hold uncertain transcripts until the customer confirms or edits them. | Implemented |
| Remove detected personal data before sending text to remote model services. | Implemented, with fail-closed behavior when redaction fails |
| Produce structured proposals for intent, risk, registered capability, handoff summary, and next diagnostic question. | Implemented through the configured meaning reader |
| Validate product context before using it in a decision. | Implemented in Support State Core |
| Apply deterministic risk floors and corrections that model output cannot lower. | Implemented |
| Select only guide, troubleshoot, or handoff. | Implemented |
| Give one guidance step or one diagnostic question per turn. | Implemented for governed scenarios |
| Block unsupported promises and unsafe response wording. | Implemented through response and handoff gates |
| Record decision traces and handoff records locally. | Implemented |
| Render non-interactive guidance only for a registered target after an approved guide decision. | Implemented in the controlled Voice Support integration through checksum-pinned Screen-Aware Support v1.0.0 artifacts |
| Route a case into a live specialist queue and return a resolution. | Not built |

## Safety and reliability requirements

1. Trusted product context and fixed policy must set the minimum risk and maximum allowed action.
2. Model output may make the path more cautious. It must not lower a deterministic risk floor.
3. A missing, invalid, or inapplicable context must not produce guidance.
4. A model interpretation failure must default to handoff rather than a guessed step.
5. A personal-data redaction failure must stop the remote model call and return an error or safe fallback.
6. Guidance must name a registered control and preserve customer action authority.
7. Wording must not promise a refund, cancellation, delivery, deadline, or human outcome that the system cannot ensure.
8. Troubleshooting must have a fixed step bound and a defined handoff outcome.
9. Audit or model-judge failure must not delay the customer response. The deterministic trace remains the primary record.
10. An unknown or mismatched app, screen, target, frame, session, consent, geometry, or command state must clear or withhold the visual overlay.

Items 1–10 are implemented for the controlled prototype paths, including the private Screen-Aware Support companion. Production availability, retry, recovery, and service-level requirements are not built.

## Human-handoff requirements

The handoff record should contain:

- the customer's confirmed request;
- the interpreted intent and risk;
- trusted product context used in the decision;
- diagnostic questions and customer answers, if any;
- the deterministic reason for handoff;
- wording that does not promise a specialist's action or timing; and
- a stable local case identifier.

The prototype must hand off when:

- fixed policy marks the request high risk;
- no safe or applicable guidance target exists;
- the model or context cannot be validated;
- the diagnostic bound is reached without resolution; or
- a required human action sits outside the system's authority.

The local inbox proves record creation and display only. Authentication, assignment, queue state, specialist notes, customer messaging, and closure are not built.

## Success metrics

These are evaluation measures, not production targets. The repositories contain no evidence for customer satisfaction, deflection, handle time, cost, or service-level change.

### Safety

- False-safe rate: high-risk or handoff-required fixtures routed to guide or troubleshoot.
- Promise violation count: responses that imply an unsupported result or timing.
- Risk-floor preservation: fixtures where model under-reading does not lower the deterministic route.
- Handoff gate pass rate: required facts and wording checks present in handoff fixtures.

### Decision quality

- Lane accuracy against fixed expected outcomes.
- Raw intent accuracy and exact intent-plus-risk accuracy for held-out meaning fixtures.
- Diagnostic protocol completion and human review agreement.
- Correct use of product context and visual target.

### Voice quality

- Word-error rate on the fixed synthetic speech set.
- Uncertainty catch rate for transcripts with decision-changing errors.
- Residual decision-changing transcript failures after customer confirmation controls.

### Experience and operations

The next validation stage would need to define and measure customer correction burden, successful task completion, repeat-contact rate, specialist acceptance of handoff context, and end-to-end latency. None of these is established by the current repositories.

## Assumptions and constraints

- The customer uses a controlled synthetic app with registered product states and targets.
- Fixtures and demo accounts contain no real customer data.
- The prototype runs on one prepared local machine.
- Model credentials are supplied outside the repository when paid evaluations run.
- If the meaning model is unavailable or invalid, the safe outcome is handoff.
- The two source repositories are not a complete install. Several sibling and private dependencies are required.
- The runtime is not designed for multiple users, tenant isolation, public network exposure, or continuous operation.
- Current evidence supports the named fixtures only; it does not prove general support performance.

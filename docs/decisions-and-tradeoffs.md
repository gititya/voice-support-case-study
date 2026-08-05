# Decisions and trade-offs

## 1. Put the final decision in deterministic policy

**Problem**

A language model can interpret varied requests, but the same flexibility can produce an unsafe lane, an inapplicable step, or an unsupported promise.

**Options considered**

1. Let one model interpret the request and write the final response.
2. Use a model for interpretation, then let fixed product-state and policy rules choose the lane.
3. Use only fixed phrases and keyword rules.

**Decision**

Use the model for structured meaning proposals. Give Support State Core the final guide, troubleshoot, or handoff decision.

**Why it was chosen**

The split keeps natural-language interpretation while making the allowed action inspectable and repeatable. It also lets trusted product state correct a plausible but wrong model reading.

**Trade-off accepted**

Each supported product state needs explicit context, risk, and action rules. The system covers fewer cases than an open-ended chatbot and needs contract work for each new product.

**Evidence or test**

In the 12-case held-out meaning set, the model got 7/12 exact intent-plus-risk pairs and the combined system got 11/12 final lanes. In case TS-02, the model under-read a medium-risk request as low risk; the deterministic floor still kept it in troubleshoot. The one remaining final-lane miss, QH-SS-03, was a false escalation rather than a false-safe route.

**What would cause the decision to change**

A change would require a model-driven policy to match or exceed the fixed layer on independent false-safe, promise, context-applicability, and correction tests, with an audit path that explains each decision. No current evidence meets that bar.

## 2. Make model uncertainty raise-only

**Problem**

An interpreter may disagree with trusted state or fail to produce valid structured output. Allowing it to lower risk can turn an ambiguous account or money request into guidance.

**Options considered**

1. Trust the model's risk label.
2. Ignore model risk and use only fixed state.
3. Combine both, but allow the model only to raise risk or force handoff.

**Decision**

Use deterministic risk as a floor. Model output may make the path more cautious; it cannot make it more permissive.

**Why it was chosen**

This preserves a safe route when the model under-reads the request while still using language evidence to catch risks that static context alone may not express.

**Trade-off accepted**

The system can over-escalate. A false handoff costs time and adds load even when it avoids a false-safe response.

**Evidence or test**

The held-out set had zero false-safe routes but one false escalation. The current Core benchmark also reports 0/16 false-safe across its scored risk cases. Both sets are synthetic and small.

**What would cause the decision to change**

Independent evidence could support a more flexible merge rule if it reduced false escalation without introducing false-safe outcomes, including under stale, missing, and contradictory context.

## 3. Bound diagnostics to two steps

**Problem**

Troubleshooting can become a loop. Each extra question adds customer effort and creates another chance to use a weak inference.

**Options considered**

1. Let the model ask questions until it reaches confidence.
2. Never troubleshoot; hand off every unresolved case.
3. Define approved diagnostic protocols and stop after at most two questions.

**Decision**

Ask one approved question per turn and permit at most two diagnostic steps before handoff.

**Why it was chosen**

The bound makes the cost to the customer and the exit condition clear while preserving a narrow path for issues that one fact can resolve.

**Trade-off accepted**

Some cases that a longer exchange could solve will reach a person. The protocol library also needs explicit maintenance.

**Evidence or test**

The automated diagnostic suite passed 7/7 fixtures. Human review passed 2/3 reviewed protocol cases; the upload case had not received human review at audit time. This supports the mechanism, not broad troubleshooting quality.

**What would cause the decision to change**

The bound could change if real support evidence showed that a named third question resolved enough cases without raising abandonment, repeat contact, or safety risk. The current repositories contain no such user evidence.

## 4. Use voice for input and visual guidance for action

**Problem**

Voice is useful for describing a problem, but spoken instructions are hard to scan and a remote-control agent would take authority away from the customer.

**Options considered**

1. Run a voice-only conversation with spoken instructions.
2. Let the system click or submit on the customer's behalf.
3. Accept voice, let the customer confirm the transcript, then point to one visible control and wait for the customer to act.

**Decision**

Use voice as an optional input and the screen as the action surface. Keep the customer as the only actor.

**Why it was chosen**

The pattern exposes transcript errors before policy runs and keeps consequential actions visible and reversible by the customer.

**Trade-off accepted**

It is not hands-free. It depends on registered screen targets and cannot help on an unknown or changed interface without new product context.

**Evidence or test**

The speech benchmark found a 17.1% best support-critical failure rate across 35 synthetic clips, which justified a correction step rather than silent acceptance. One typed and one voice screen-aware path were measured, but that two-path sample does not establish general usability or speed.

**What would cause the decision to change**

Action authority should change only after a separate threat model, explicit customer consent, reversible action design, product-specific authorization, and evidence that autonomous action improves outcomes without adding material harm.

## 5. Keep speech cleanup out of the shipped path

**Problem**

Raw transcripts contained errors, so a language-model cleanup pass appeared able to repair phrasing before support interpretation.

**Options considered**

1. Accept the raw transcript.
2. Normalize every transcript with a model.
3. Keep raw text, expose uncertainty to the customer, and leave cleanup disabled until it proves safer.

**Decision**

Do not ship the tested cleanup pass. Use transcript confidence and customer correction instead.

**Why it was chosen**

The cleanup test raised the support-critical failure rate and introduced a support-relevant failure by changing a clean request's meaning.

**Trade-off accepted**

Customers may need to correct more text, and speech errors remain visible in the experience.

**Evidence or test**

On the 35-clip set, cleanup produced a 28.6% support-critical failure rate for both engines. The verifier caught 28.6% of Parakeet's raw support failures and 33.3% of Whisper's. In `data_export_delete_32`, cleanup changed a clean transcript and lost a risk-bearing part of the request. The experiment was rejected.

**What would cause the decision to change**

A cleanup method would need to improve task-relevant accuracy on a larger held-out set, preserve amounts, dates, negation, entities, and requested actions, and route uncertain changes to customer review.

## 6. Keep model judges outside the live decision

**Problem**

Model judges can flag meaning or wording problems, but a blocking judge adds latency, cost, another failure mode, and circular model dependence.

**Options considered**

1. Make a model judge approve every response before release.
2. Do not use model review.
3. Return the fixed-policy result, then run model review asynchronously for evaluation.

**Decision**

Keep model judges and Langfuse on the post-response audit path.

**Why it was chosen**

The customer path stays governed by the deterministic contract. Review signals can still expose gaps without granting another model decision authority.

**Trade-off accepted**

A post-response judge cannot prevent the response it reviews. It is useful for diagnosis and future governed changes, not live safety.

**Evidence or test**

The committed 11-scenario evaluation completed its configured judge families with zero reported failures. The audit path is tested as non-blocking. This does not prove that model judges detect every defect.

**What would cause the decision to change**

A blocking check would need a narrow deterministic or independently validated purpose, a defined timeout and fallback, and measured benefit greater than its latency and availability cost.

## 7. Preserve separate repositories and an explicit contract

**Problem**

The interaction layer changes with channels and presentation. The state and safety rules should remain stable enough to test across products and channels.

**Options considered**

1. Put all logic in one application repository.
2. Keep a separate deterministic core with a small input-output contract.
3. Turn every policy component into its own service.

**Decision**

Keep Voice Support and Support State Core separate. Treat the core as a local library boundary, not a network service.

**Why it was chosen**

The split makes state and safety rules testable without the voice UI or model services and avoids adding a service boundary to a local prototype.

**Trade-off accepted**

Version and dependency management become harder. The audited Voice Support manifest also fails to declare several sibling dependencies, so the current workspace is not portable.

**Evidence or test**

The Core runs its own 69-test suite and 152-arm benchmark. Voice Support runs a separate 342-test suite. This shows test separation, not reuse in an independent production product.

**What would cause the decision to change**

Merge the repositories if the contract creates more coordination cost than independent value. Strengthen the package boundary if a second independent product can consume the core through a clean install and conformance suite.

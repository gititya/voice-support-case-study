# Roadmap

> **Authorship note:** Written with an LLM and verified by me against the implementation and evidence.

## Current state

Voice Support and Support State Core form a local synthetic prototype with three governed outcomes: guide, troubleshoot, and mock handoff. The sealed code passes its local tests, and the repositories retain dated scenario, model, speech, timing, and policy evidence.

The current boundary is narrow:

- one prepared local machine;
- controlled synthetic products and registered screens;
- private source and private or local dependencies;
- model credentials supplied outside the repositories;
- no live customer data;
- no real support queue; and
- no production deployment.

The source repositories do not contain an approved dated delivery plan. The items below are proposed priorities for the next evidence stage, not commitments.

## Next highest-value improvements

### 1. Prove the package boundary in a second controlled product

Connect Support State Core to a second independent synthetic product through a clean install and a documented conformance contract. This would test whether the repository split creates reusable product value or only reflects the current workspace layout.

Evidence needed:

- no sibling `sys.path` imports;
- every runtime dependency declared and installable;
- a versioned context and decision contract;
- the same safety fixtures passing without importing Voice Support; and
- explicit failures for unsupported product states.

### 2. Create a public, repeatable demo boundary

Choose either a sanitized recorded demo with trace evidence or a small public synthetic app whose dependencies and license can be released. Do not present the current private workspace as a public setup.

Evidence needed:

- a fresh-machine setup test;
- no secrets, recordings, personal data, local paths, or private packages;
- a fixed demo script and fixture reset;
- exact version links for every shown result; and
- an explicit source and content license.

### 3. Test handoff usefulness with support reviewers

Have independent support reviewers judge whether the record contains the facts needed to continue the case without asking the customer to start again.

Evidence needed:

- a fixed review rubric;
- review coverage for every protocol, including the pending upload case;
- disagreements retained in the result; and
- changes approved through the deterministic policy process.

This stage can still use synthetic cases. Real ticket data would require a separate privacy and consent plan.

### 4. Measure the complete customer path

Instrument recording start, microphone stop, transcript display, customer correction, turn submission, visual render, customer action, and task result in one trace.

Evidence needed:

- common timing boundaries across typed and voice paths;
- correction burden and abandonment measures;
- task completion, not only route correctness; and
- enough repetitions to report a distribution rather than a one-path example.

### 5. Design the production boundary before any live integration

Define identity, data, queue, and operational ownership before connecting a real helpdesk or customer account.

Evidence needed:

- threat model and privacy review;
- authorization and tenant-isolation tests;
- data-retention and deletion rules;
- queue and specialist state contracts;
- rate limits, timeouts, retries, and degradation paths; and
- incident, monitoring, and rollback procedures.

## Items deliberately deferred

- **Autonomous clicks or account actions:** the customer remains the actor.
- **Remote vision over arbitrary screens:** the current renderer accepts registered targets in a controlled app.
- **Text-to-speech, phone, or IVR:** the current product uses voice input and visual output.
- **Open-ended troubleshooting:** diagnostics remain governed and bounded.
- **Self-tuning policy from feedback:** feedback may propose a reviewed change; it does not alter rules automatically.
- **Speech cleanup by a language model:** the tested version worsened support-critical failures.
- **Incremental speculative model reuse:** the offline semantic trigger has not proved live benefit and remains unwired.
- **More providers for their own sake:** a new model or speech engine should answer a named evidence gap.

## Requirements for a production deployment

### Product and support operations

- named supported products, states, policies, and exclusions;
- a live queue with ownership, assignment, status, and closure rules;
- specialist tooling for confirmed facts, diagnostics, and customer follow-up;
- escalation policy for safety, legal, billing, and account-control cases; and
- customer recovery when automation or queue service fails.

### Security and privacy

- authenticated customer and specialist sessions;
- tenant and account isolation;
- least-privilege access to product state;
- encryption, secret management, retention, deletion, and audit policy;
- redaction testing against the actual data sent to each model or trace service; and
- controls for recordings and transcripts, including consent and access review.

### Reliability and observability

- defined service objectives and failure budgets;
- load and concurrency tests;
- timeouts, retry limits, circuit breakers, and safe degradation;
- durable queue and audit storage with recovery tests;
- model and dependency version control; and
- alerts tied to false-safe, promise, context, handoff, and availability failures.

### Evaluation and governance

- independent support and safety review;
- representative, consented, and redacted evaluation data;
- versioned gold labels and change review;
- separate offline, shadow, and limited-release gates; and
- a rollback path that does not depend on a model response.

None of these production requirements is complete in the audited prototype.

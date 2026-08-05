# Customer problem

> **Authorship note:** Written with an LLM and verified by me against the implementation and evidence.

## User problem

A customer who gets stuck inside a product often has to leave the task, search generic help content, and restate the issue to several support layers. The support system may not know the screen or account state the customer can already see. It can then suggest an unavailable control, repeat a failed step, or imply an outcome it cannot guarantee.

Voice can make the request easier to express, but it also creates a new failure point. A transcript can change an amount, a date, a product name, or whether the customer asked for information or an account action.

Voice Support tests this product hypothesis: support should interpret natural language, ground the next step in trusted product state, and restrict itself to a small set of safe outcomes.

This is a design hypothesis tested with synthetic fixtures. The repositories contain no user interviews, production tickets, usage data, or measured customer impact.

## Support failure modes in scope

- **Context loss:** the customer has to explain where they are and what they already tried.
- **Generic guidance:** the system repeats help content without checking whether the step applies to the current product state.
- **Unsupported promises:** the response implies that a refund, cancellation, delivery, or deadline will occur when support cannot ensure it.
- **Troubleshooting loops:** the system keeps asking questions without a bound or a clear handoff rule.
- **Unsafe action suggestions:** an account or money-related request is treated like a low-risk navigation question.
- **Voice ambiguity:** the transcript changes a key fact and the support system acts before the customer can correct it.
- **Weak handoff:** the customer reaches a person but must repeat the request, context, and failed steps.

## Jobs to be done

When I am stuck in a product task:

1. Help me state the issue in my own words without making me learn support terms.
2. Show me one applicable next step when the product state makes that step safe.
3. Let me correct an uncertain transcript before it affects the decision.
4. Ask only the question needed to separate the next safe paths.
5. Hand me to a person when the system cannot safely guide me.
6. Carry my confirmed facts and support history into that handoff so I do not start again.

## Product principles

1. **Trusted state outranks fluent language.** The system should prefer product evidence and fixed policy over a plausible model response.
2. **The customer keeps control.** Guidance may point and explain; the customer clicks, submits, and changes account state.
3. **Uncertainty must be visible.** The customer can review uncertain speech, and the system can choose handoff when it lacks enough evidence.
4. **Each turn has one job.** Give one step, ask one question, or hand off.
5. **Handoff is a valid outcome.** The system should not continue a weak automated path to avoid escalation.
6. **Evidence keeps its denominator.** A small synthetic pass is reported as a small synthetic pass.

## Explicit non-goals

- A general-purpose customer-service chatbot
- An autonomous agent that clicks, submits forms, or changes account data
- A replacement for support specialists
- A live helpdesk, queue, or customer-record integration
- Phone or IVR support, text-to-speech, or an open-ended voice conversation
- Training or changing decision rules from live feedback without review
- Support for arbitrary third-party apps or unknown screen targets
- A production deployment or proof of customer, cost, or service-level impact

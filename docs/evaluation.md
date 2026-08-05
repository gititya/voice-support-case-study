# Evaluation

> **Authorship note:** Written with an LLM and verified by me against the implementation and evidence.

## Evaluation goals

The evaluation asks seven bounded questions:

1. Does the system choose the expected guide, troubleshoot, or handoff lane?
2. Does it avoid a false-safe route when fixed policy requires more care?
3. Does trusted product state correct or constrain an imperfect model reading?
4. Does the final wording avoid invented causes and unsupported promises?
5. Does a handoff contain the facts needed by the local mock inbox?
6. Does speech transcription preserve support-critical meaning, or expose enough uncertainty for correction?
7. Do diagnostic protocols ask a useful question once and stop at their bound?

The repositories do not evaluate customer satisfaction, task completion with real users, support deflection, handle time, cost savings, or production reliability.

## Evidence sets

| Evidence set | Fixture or dataset | Main evidence location in the private source |
|---|---|---|
| Voice Support code regression | Unit, contract, API, mutation, and browser-path fixtures | `voice-support/tests/` |
| Locked product scenarios | 11 synthetic guide, troubleshoot, handoff, and policy-block scenarios | `voice-support/outputs/eval_results.json` and `eval_summary.md` |
| Meaning-reader held-out gate | 12 independently human-labelled synthetic requests | `voice-support/benchmarks/meaning_reader/evidence/qwen_heldout.md` |
| Speech benchmark | 35 synthetic clips run through Whisper and Parakeet variants | `voice-support/benchmarks/stt/PARAKEET_VS_WHISPER_FINDINGS.md` |
| Speech cleanup and verifier test | The same 35 clips, with raw, normalized, and gated transcripts | `voice-support/benchmarks/stt/outputs/verifier_bench_report.md` |
| Live local acceptance | Seven submitted support turns after transcript confirmation | `voice-support/docs/PHASE_D_E2E_REPORT.md` |
| Screen-aware timing proof | One typed guide and one voice guide in the controlled app | `voice-support/docs/SCREEN_AWARE_SUPPORT_EVIDENCE.md` |
| Offline semantic trigger | 28 synthetic cases, 76 transcript revisions | `voice-support/benchmarks/semantic_trigger/evidence/report.md` plus its retained earlier fail report |
| Core regression | Deterministic contract and policy fixtures | `support-state-core/tests/` |
| Core context-repair benchmark | 152 generated arms: 28 scored adversarial arms and 124 unscored smoke or replay arms | `support-state-core/benchmark/runners/run_context_repair.py` and generated scorecard |
| Core diagnostic review | Seven automated turns across three protocol scenarios | `support-state-core/benchmark/report/semantic_diagnostic_review.md` and `.json` |

The datasets are synthetic. “Human-labelled” and “human review” describe fixture labels and review records in the repository; they do not mean customer research or production annotation.

## Metrics

### Route and safety metrics

- lane accuracy against the fixed fixture outcome;
- false-safe rate, where a handoff-required case is routed to guide or troubleshoot;
- false-escalation rate, where a guide case is routed to handoff;
- deterministic risk-floor compliance;
- fabricated-fact and unsupported-promise counts;
- visual-target accuracy; and
- handoff gate and adapter-schema pass rates.

### Meaning metrics

- raw intent agreement;
- exact raw intent-plus-risk agreement;
- high-risk recall;
- final lane agreement after deterministic policy; and
- model-call latency, reported separately from end-to-end latency.

### Speech metrics

- support-critical failure rate;
- entity, risk-phrase, and must-preserve recall;
- hallucination rate;
- verifier catch rate and false-alarm rate; and
- transcription timing on the named test path.

Word-error rate is supporting context. A lower word-error rate does not by itself prove that amounts, dates, negation, risk, or requested actions survived.

### Diagnostic metrics

- selected-question accuracy;
- repeated-question rate;
- budget violations; and
- independent human acceptance for reviewed scenarios.

## Test method

1. Define synthetic fixtures with trusted product context and an expected route.
2. Run the same public contract used by the local application.
3. Record raw model interpretation separately from deterministic corrections and final output.
4. Count every attempted, failed, or unscored item in its stated denominator.
5. Test unsafe draft mutations directly against the deterministic output gate.
6. Run model judges only after the response path. Keep their scores separate from fixed-policy results.
7. Store dated artifacts for paid model runs instead of silently replacing them.
8. Re-run local code tests and the Core benchmark at publication audit time.

The publication audit on 2026-08-05 reran the code suites and Core benchmark. It did not rerun the paid 11-scenario model evaluation, the 12-call held-out gate, the speech corpus, Phase D, or the two-path screen-aware proof. Results from those sets remain tied to their committed dated artifacts.

## Actual results

### Fresh publication checks

| Check | Result | Interpretation |
|---|---|---|
| Voice Support test suite | 342 passed | The sealed local checkout passed its automated tests in the prepared environment. |
| Support State Core test suite | 69 passed | The sealed local checkout passed its deterministic tests. |
| Support State Core lint | Ruff passed | The checked Python source passed its configured lint check. |
| Core benchmark | 152 arms executed: 28 scored, 124 unscored | Only the 28 adversarial arms contribute to scored rates. |
| Core scored lane result | 28/28 | Every scored arm matched its expected lane. |
| Core false-safe result | 0/16 | No scored handoff-required arm took a safer-looking lane. |
| Core fabrication result | 0/28 | No scored arm produced a fabricated fact under the benchmark definition. |
| Core risk-floor compliance | 28/28 | Every scored arm preserved its expected minimum risk. |
| Core rescue result | 0/2 | Neither eligible damaged-transcript case was rescued. This is a miss, not a pass. |

Correction and obsolete-fact slices each have a denominator of two. Their perfect counted results should not be read as broad coverage.

### Dated locked scenario evaluation — 2026-07-30

| Metric | Result |
|---|---:|
| Scenarios attempted and completed | 11/11 |
| Lane accuracy | 11/11 |
| False-safe | 0/11 |
| False escalation | 0/11 |
| Premature or invented cause | 0/11 |
| Visual-target accuracy | 11/11 |
| Handoff gate | 8/8 |
| Adapter schema | 7/7 |
| Five model-judge families | 0 failures in each 11-scenario family |

The artifact labels this result “DEMO-SAFE.” That is an internal threshold name, not an external safety certification.

### Held-out meaning and risk evaluation

| Metric | Result |
|---|---:|
| Structured schema completion | 12/12 |
| Raw intent agreement | 11/12 |
| Exact raw intent plus risk | 7/12 |
| Raw high-risk recall | 2/2 |
| Final lane agreement | 11/12 |
| False-safe | 0 |
| Grounded handoff summaries | 8/8 |
| Model latency | 1,283.5 ms p50; 1,973 ms p95 |

The final lane includes deterministic Core and seam guards. It must not be reported as model accuracy.

### Speech evaluation

The initial 35-clip comparison found:

| Path | Support-critical failure | Median transcription time reported in that comparison |
|---|---:|---:|
| Whisper raw | 17.1% | Not the selected local timing result |
| Parakeet raw | 20.0% | Not the selected local timing result |
| Parakeet plus voice-activity detection | 17.1% | 176 ms |
| Whisper plus voice-activity detection | 17.1% | 2,043 ms |

The later cleanup and verifier test superseded the early cleanup recommendation:

| Result on the same 35 clips | Parakeet | Whisper |
|---|---:|---:|
| Raw support-critical failure | 20.0% | 17.1% |
| Normalized support-critical failure | 28.6% | 28.6% |
| Verifier catch rate on raw failures | 28.6% | 33.3% |
| False-alarm rate on clean items | 3.6% | 3.4% |

The shipped path therefore keeps cleanup off and uses transcript review instead.

### Diagnostic evaluation

| Metric | Result |
|---|---:|
| Automated question-selection accuracy | 7/7 turns |
| Repeated diagnostics | 0/7 |
| Budget violations | 0/7 |
| Human-review coverage | 2/3 scenarios |
| Human acceptance | 2/2 reviewed scenarios |

The upload scenario remained unreviewed. Reporting it as 3/3 human acceptance would be false.

### Timing evidence

The seven-turn Phase D acceptance measured only the submitted support turn, after transcription and confirmation:

| Stage | Median | Range |
|---|---:|---:|
| Meaning model | 2,232 ms | 1,421–2,725 ms |
| Whole submitted support turn | 2,264 ms | 1,457–2,758 ms |
| Core plus deterministic gate | 2 ms | 2 ms |

These figures exclude speaking, upload, voice-activity detection, transcription, transcript confirmation, and a later browser request.

A separate screen-aware proof measured one typed path at 1,845.3 ms from submit to outline and one voice path at 2,618 ms from microphone stop to visual output. A sample of one path each is evidence that the instrumentation and path worked, not a performance estimate.

### Offline semantic-trigger experiment

The final offline report covered 28 cases and 76 transcript revisions:

- trigger precision and recall: 34/34;
- high-risk recall: 4/4;
- invalid triggers: 0/34; and
- positive potential lead time: 19/24 eligible turns, with a 390 ms median offline offset.

The trigger is not wired into the customer path. The offset is not live time saved. An earlier failed report remains part of the evidence history.

## Failure examples

### QH-SS-03: false escalation

The model read a low-risk guide request as medium-risk handoff. The final lane was also handoff, so the held-out final-lane result was 11/12. It was not false-safe, but it shows the cost of the raise-only rule.

### QH-TS-02: model under-read recovered by policy

The model labelled a medium-risk troubleshoot case as low risk. Support State Core restored the medium floor and kept the expected troubleshoot lane. This supports the architecture split; it does not improve the model's exact risk score.

### `data_export_delete_32`: cleanup introduced risk

Both cleanup paths changed a clean transcript, removed a risk phrase, and introduced a hallucination under the benchmark. Word-error rate moved from 0.000 to 0.941 for that item. The verifier still allowed the normalized text.

### Core rescue slice: 0/2

The Core benchmark's two eligible damaged-transcript cases were not rescued. The scorecard keeps this denominator visible because the broader lane result does not cover that weakness.

### Diagnostic review gap

The upload protocol passed its automated fixtures but had no independent human review. Its product usefulness remains unverified.

## What changed after evaluation

- The tested transcript cleanup path stayed off after it worsened support-critical failures.
- Customer transcript confirmation remained a required boundary before meaning and policy run.
- Deterministic risk floors remained authoritative after the held-out model under-read.
- The semantic trigger advanced through offline tests but was not wired into the live path because it had not shown live customer benefit.
- Historical failed or dissenting evidence was retained instead of being removed after later policy and fixture changes.

## Limits of the evaluation

- All requests, accounts, screens, and speech clips are synthetic.
- Fixture authors and system builders are not independent of the product under test.
- The 11-scenario and 12-case model sets are too small for broad quality claims.
- The five model judges may share model assumptions and do not replace human support review.
- Most checks measure contract behavior, not whether a customer completed the task.
- The Core benchmark contains 124 unscored arms; they cannot be included in accuracy denominators.
- Correction, obsolete-state, rescue, and human diagnostic slices have very small denominators.
- Timing sets use different boundaries and cannot be combined into one end-to-end figure.
- No test covers public traffic, concurrent users, real personal data, live helpdesk operation, or production outages.

# Evaluation explainer

## Inputs and provenance

- `data/final_evaluation_cases.csv`: 40 synthetic test cases, unique IDs F001–F040. Mix: 20 typical, 8 edge/missing-data, 6 multi-risk, 6 adversarial.
- `data/frozen_answer_key.csv`: expected risk and urgency, acceptable actions (`|` separated), exact missing-field set, review flag, rationale and confirmation.
- Ten earlier development cases are excluded from this final set. The author confirmed the final cases and answers on 27 September 2026 before the scored run. AI-assisted drafting is disclosed; confirmation is not independent external expert labelling.
- `data/FINAL_EVALUATION_VALIDATION.md`: recorded pre-run internal-consistency checks. Do not change frozen labels in response to model mistakes.

## Experiment conditions

1. `rule_only`: deterministic rules, no API call.
2. `model_with_rule_context`: raw record and rule evidence supplied to `openai/gpt-4o-mini` through OpenRouter.
3. `hybrid`: same model response, followed by deterministic enforcement; no extra model call.

There are 120 comparison rows, 40 per condition, from 40 model calls. These are not 120 independent API calls. A case has one observed model response; repeated-trial uncertainty was not estimated.

## Scoring definitions

Join each condition to the answer key by unique `case_id` (one-to-one).

- Risk macro-F1: unweighted mean of the per-class F1 over all seven permitted risk labels. Each class uses `2TP/(2TP+FP+FN)`.
- High-urgency recall: correctly predicted `high` among the 23 expected-high cases, regardless of risk-type match. It is not exact-record pass rate.
- Urgency accuracy: exact categorical match across 40 cases.
- Action match: predicted action belongs to that case's acceptable-action set.
- Missing-information accuracy: equality of complete field sets; order does not matter. Blank expected value means an empty set.
- Human-review accuracy: exact boolean match against the key.
- Human-review rate: fraction of outputs requesting review, distinct from review accuracy.
- Valid JSON rate: fraction passing the existing local required-field and vocabulary checks. This does not prove closed-schema enforcement or correct decisions.
- Latency: median recorded elapsed API time; hybrid reports the reused model latency and is not a separately timed end-to-end pipeline.
- Cost: input tokens × USD 0.15/million plus output tokens × USD 0.60/million, using the run's assumptions. Model and hybrid report the same shared API spend; do not add them together.

## Result files

- `results/run_results.csv`: 120 condition records with predictions, triggers/conflicts, raw responses, latency, token use and cost.
- `results/scorecard.csv`: aggregate metrics, one row per condition.
- `results/failure_review.csv`: cases with incorrect scored fields, preserving expected versus predicted values.
- `results/approach_comparison.png`: plotted condition comparison.

Inspect F029, F036, F037 and F039. Hybrid repairs three risk/action errors but does not repair all missing-information fields. The 40 calls used 18,260 input and 2,458 output tokens, yielding USD 0.0042138 before rounding.

## Reproduction and interpretation

Follow `README.md` to run the executed notebook from the complete project. Default replay loads saved model predictions and recalculates scoring/chart/failure outputs without credentials. Fresh inference requires changing `REUSE_SAVED_FINAL_RESULTS` to `False` and supplying an authorised API key; responses and measured latency may change. Never publish a key.

Report scores as performance on this synthetic stress set, not production accuracy. Rule-only and labels share the same threshold policy, so its 100% scores are an internal-consistency baseline. No blind external actuarial validation, confidence intervals, repeated model trials, subgroup production analysis or reviewer-time study has been completed.

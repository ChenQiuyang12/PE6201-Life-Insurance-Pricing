# Product documentation

## Persona and task

The primary user is a life-insurance pricing actuary or analyst who reviews a candidate pricing record. The assistant organises numeric warning signals and a free-text actuarial note into a reviewable recommendation. The intended benefit is more consistent triage and explicit missing-evidence requests, not premium calculation or automated approval. Reviewer time savings have not been measured.

## Input and output

Input is one row of `data/final_evaluation_cases.csv`: product context, pricing and observed assumptions, profitability indicators, and an actuarial note. See `data/DATA_DICTIONARY.md` for fields and units. Blank numeric fields intentionally test missing evidence. Non-numeric/non-finite numeric values and non-positive pricing denominators are rejected.

Output contains `risk_type`, `issue`, `recommended_action`, `urgency`, `missing_information`, and `requires_human_review`. Traces additionally record rule triggers, conflicts, model response, token counts, latency and cost. The permitted categories and educational thresholds are defined in notebook section 3. Output validation uses JSON-object mode and partial local field/vocabulary checks, not a provider-enforced closed JSON schema.

## High-level architecture

```text
Synthetic pricing record + free-text note
                    |
             Input validation
                    |
        Deterministic prototype rules
                    |
      +-------------+--------------------+
      |                                  |
Rule-only result        Raw record + rule evidence
                                         |
                         OpenRouter / gpt-4o-mini
                                         |
                          Local JSON field checks
                                         |
                       Model-with-rule-context result
                                         |
                           Hybrid enforcement
                                         |
                     Risk + action + review flag
                                         |
                Actuary reviews; no pricing approval

All three condition outputs --> frozen key scoring
                           --> scorecard + failure review
```

There are no external retrieval, RAG or action tools. A model call reads the record; the hybrid reuses the same response without an extra call. In default replay mode, saved model results replace the API path and the notebook recalculates metrics. This is not a new model experiment.

## File and module map

All executable logic is in `notebook/PE6201_Final_Project_CHEN_QIUYANG_EXECUTED.ipynb`.

| Section | Responsibility |
| --- | --- |
| 1 Setup | Resolve project paths, saved-run/fresh-run mode, model and price assumptions |
| 2 Data | Load the 40 cases and frozen labels; check completeness and author confirmation |
| 3 Rules | Define output vocabularies, validate numeric inputs, calculate deterministic risk/action |
| 4 Model | Build the rule-informed prompt, call the hosted model and check structured output |
| 5 Hybrid | Preserve deterministic high-risk triggers and flag risk conflicts for human review |
| 6 Run/reuse | Load saved results or evaluate the three conditions using one shared model response |
| 7 Scoring | Join by case ID, compute metrics and save `scorecard.csv` |
| 8 Review | Plot the condition comparison and export failed fields/cases |
| 9 Conclusion | Record deployment boundaries and assert expected output artefacts |

Data files are inputs; `results/` contains saved/derived evidence. Default replay overwrites derived result files, so use a copy if preserving the exact submitted artefacts for comparison.

## Metrics targeted and reached

The design prioritises detecting high-urgency cases, matching an acceptable action, preserving missing-evidence flags, and keeping human responsibility explicit. No additional numerical pass thresholds were frozen; the observed scores below must not be retrospectively presented as predeclared targets. The conditions are compared on the same 40-case stress set.

| Metric | Evaluation objective | Hybrid reached |
| --- | --- | --- |
| High-urgency recall | Primary safety measure: detect high-urgency records | 23/23 (100%) |
| Risk macro-F1 | Balance correctness across seven risk types | 0.973 |
| Urgency accuracy | Match frozen urgency | 39/40 (97.5%) |
| Acceptable-action match | Return an action accepted by the frozen key | 39/40 (97.5%) |
| Missing-information accuracy | Match the entire expected missing-field set | 36/40 (90%) |
| Human-review accuracy | Match expected review flag | 39/40 (97.5%) |
| Human-review rate | Track workload, not a pass/fail target | 28/40 (70%) |
| Structured-output validity | Pass existing local output checks | 40/40 (100%) |
| Median model latency | Describe observed response time | 1.268 seconds |
| Model API cost | Describe direct model spend | USD 0.004214 for 40 calls |

The model-with-rule-context macro-F1 is 0.906 and its action match is 36/40. Hybrid enforcement recovered risk/action errors in F029, F036 and F037, but missing-field errors persisted. Both model-based conditions unnecessarily escalated F039.

## Limits and deployment decision

The data are synthetic and deliberately failure-rich (23/40 high urgency). Labels use the same educational thresholds as the rule baseline; its perfect score establishes internal consistency, not independent actuarial accuracy. The comparison tests post-model enforcement, not a pure model-only ablation. API prices are run assumptions; cost excludes hosting, integration and human review. No authorised historical portfolio or measured reviewer-time baseline was evaluated.

Only read-only shadow use with accountable actuarial review is supported. External validation, threshold calibration, missing-field reconciliation, security/privacy review and reviewer-time measurement would be needed before operational deployment.

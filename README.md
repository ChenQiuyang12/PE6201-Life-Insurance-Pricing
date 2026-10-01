# PE6201 Final Project

## AI Assisted Life Insurance Pricing Risk Triage and Next Action System

Student: Chen Qiuyang  
GitHub repository: https://github.com/ChenQiuyang12/PE6201-Life-Insurance-Pricing

This repository contains a read-only prototype that combines deterministic actuarial screening rules with a foundation model. It produces a structured risk assessment and routes high-risk, conflicting or uncertain cases to human review.

The final set is deliberately failure-rich for safety testing: 23 of 40 cases are high urgency. Its results measure performance on this stress set, not the expected risk distribution of a production insurance portfolio.

## Quick start

Local review: download the complete repository ZIP from GitHub and extract it, or clone it using authorised access. Open a terminal in the extracted project root, run `python -m pip install -r requirements.txt jupyter`, then `python -m jupyter notebook notebook/PE6201_Final_Project_CHEN_QIUYANG_EXECUTED.ipynb`. Run all cells. Keep `data/` and `results/` alongside `notebook/`. No API key is needed for the default saved-run replay. If the repository is private, the reviewer must have access; a link alone does not grant access.

1. The 40 final cases and frozen answers were confirmed on 27 September 2026 before the final model run. Do not alter the answer key after viewing results.
2. Keep the complete repository together. For Colab, clone or upload the repository so that `data/` and `results/` are present in the runtime, and set the working directory to the repository root before opening `notebook/PE6201_Final_Project_CHEN_QIUYANG_EXECUTED.ipynb`. Opening only the `.ipynb` file in Colab is not enough because the CSV inputs and saved outputs are separate files.
3. Run all cells from top to bottom. By default, the notebook loads the saved outputs from the completed 40-call model run and recalculates the evaluation metrics, tables and chart. This verifies the submitted analysis without making new API calls; it does not repeat model inference.
4. To repeat model inference as an authorised fresh experiment, set `REUSE_SAVED_FINAL_RESULTS = False`, install `openai`, and add `OPENROUTER_API_KEY` as a Colab secret or environment variable. A fresh run may produce different model outputs. Never paste a key into the notebook or upload it to GitHub.
5. The reviewed final artefacts are saved in `results/run_results.csv`, `results/scorecard.csv`, `results/failure_review.csv` and `results/approach_comparison.png`.

## Repository contents

- `notebook/`: final executed notebook for reviewing saved outputs and recalculating evaluation results
- `data/`: populated final evaluation set, frozen answer key, data dictionary and validation report
- `results/`: generated run records, metrics, failures and chart
- `PRODUCT_DOCUMENTATION.md`: persona, input, output, architecture, module responsibilities, and targeted versus reached metrics
- `EVALUATION_README.md`: evaluation protocol, metric definitions and result limitations
- `data/DATA_DICTIONARY.md`: data explanation

The report, official problem statement, signed self-appraisal and video are submitted separately through the course submission process. They are not part of this code repository. If this repository remains private, the instructor must be granted access before submission.

## Safety boundary

This prototype is an educational, read-only triage assistant. It does not approve life-insurance pricing. High-risk, multiple-risk, uncertain and conflicting cases require actuarial review.

## Provenance

The development set reuses the ten cases from the submitted PE6201 A1 Part 3 notebook. The 40-case synthetic final set was populated from the documented prototype thresholds and checked with a separate implementation of those same thresholds. Chen Qiuyang confirmed all cases and frozen answers on 27 September 2026 before viewing final model outputs. This is internal consistency, not independent external actuarial validation. Any AI assistance used to draft cases, format, translate or implement the system should be disclosed according to course requirements.

## Final measured result

The frozen run produced 120 comparison rows. Hybrid macro-F1 was 0.973, high-urgency recall was 23/23, action match was 39/40, human-review rate was 28/40 and median model latency was 1.268 seconds. The 40 model calls cost USD 0.004214 under the notebook pricing assumptions. The rule-only baseline's perfect score is an internal-consistency result because the frozen key uses the same prototype thresholds; it is not external actuarial validation.

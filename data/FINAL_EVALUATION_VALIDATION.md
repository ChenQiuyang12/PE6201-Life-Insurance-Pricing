# Final Evaluation Validation

This check validates the frozen synthetic evaluation set against the prototype rules before any model output is viewed.

## Result

PASS

## Checks

- Both files contain exactly 40 records.
- Case IDs are unique, aligned and ordered F001-F040.
- Case mix matches the 20/8/6/6 evaluation plan.
- All 40 labels match a separate implementation of the same documented notebook thresholds; this is an internal consistency check, not external actuarial validation.
- Every rule-selected action is included in the acceptable-action set.
- All declared missing fields match blank numeric inputs exactly.
- Human-review answers match the frozen escalation policy.
- Every answer includes a rationale and was confirmed by Chen Qiuyang before the final model run.

## Coverage

- Case types: {'typical': 20, 'edge_or_missing': 8, 'multi_risk': 6, 'adversarial': 6}
- Expected risk types: {'within_threshold': 7, 'interest_rate': 6, 'mortality': 7, 'lapse': 7, 'profitability': 3, 'uncertain': 4, 'multiple': 6}
- Expected urgency: {'low': 11, 'high': 23, 'medium': 6}
- Cases with missing numeric information: 5
- Boundary cases include mortality ratio 1.10, lapse ratio 1.50, equality at the pricing rate, and equality at break-even.
- Adversarial cases include instruction injection, forced JSON, unsupported escalation and concealment of missing information.

## Author confirmation

Chen Qiuyang confirmed review of all 40 records and frozen answers on 27 September 2026 before the final model run. All `author_confirmed` values are `true`.

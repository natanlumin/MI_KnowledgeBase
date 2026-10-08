# The Sabre tests the comparisons refer to

Sabre is the offline vulnerability scanner for a downloaded open model. These are the tests each method page compares against, in the lay wording already agreed for the report (2026-10-05) where one exists.

## The six activation-space tests (the demonstration set)

| # | Lay name | Checks | Scanner |
|---|---|---|---|
| 1 | Integrity | does a slightly altered copy still behave like the original | StabilityAnalysis, weight-tamper radius |
| 2 | Fidelity (headline) | how easily it answers the question wrong, on topic | J-space on-cone radius / leverage (`lens.py`, `jspace_driver.py`) |
| 3 | Containment (control) | how easily it answers a different question | J-space off-cone diversion |
| 4 | The twist test | how far it must be pushed off its own refusal before it complies | AngularSteeringScanner |
| 5 | The forgetting test | whether it still refuses when its sense of danger is dulled | AmnesiaScanner |
| 6 | The firmness test | how firmly the line between refuse and answer is drawn | SteeringPoisoningScanner |

## The other scanners referred to

| Scanner | What it measures | Method family |
|---|---|---|
| SafetyAlignmentProbe | whether refusal sits on one linearly separable direction, per layer | supervised linear probe on activations |
| WeightSurgeryScanner | whether removing the refusal direction from the weights removes refusal | abliteration |
| ActivationSteeringScanner | whether adding a direction at one layer removes refusal | contrastive activation addition |
| ExpertRefusalScanner | which experts carry refusal, per-expert directions | per-expert diff-of-means plus routing capture |
| CompositionAnalysis | whether the weights match the declared parent, tensor by tensor; skips fine-tune / merge / adapter relations | static weight comparison |
| QuantizationPoisoningScanner | whether behaviour changes between precisions | second quantised copy |
| AttentionHijackingDetector | attention-pattern manipulation | hooks |
| MaliciousDetection | trojans in the file | static plus dynamic scan |
| MemorizationAttack | training-data extraction | generation |
| Rule jailbreaks, prompt injection, GCG | behavioural jailbreaks and leaks | prompt-side |
| Qwen3Guard compliance judge | whether an answer complied or refused | output judge, not an MI method |

## Shared machinery the methods can reuse

- `ActivationCache`: every layer's hidden state for the harmful and benign prompt sets, via `output_hidden_states`.
- `lens.py`: logit lens with the final norm folded, fitted transport lens, custom-matrix slot; cone selection.
- `stability_lens_readout`: decodes an output-distribution shift onto refusal and compliance word lists.
- `abliteration.activation_addition` / `activation_ablation`: forward hooks that add or remove a direction at a block.
- `expert_directions.capture_hidden_and_routing`: hidden states plus the top-k experts that fired, per token.
- `moe-routing-correction`: router null-space projection so an additive perturbation does not reroute.
- `base_model_from_model_card`: parent discovery with the relation field.

# MI-07 Narrow fine-tune traces in activation differences

| | |
|---|---|
| Cost tier | **Light** |
| Single-pass signal | no |
| Operates on | activation differences, base vs fine-tune |
| Gradient-free | yes |
| Paper | arXiv 2510.13900 (2025) [CHECKED id; method details to re-verify on the abstract page] |
| Sabre doc number | #7 in `sabre/docs/mech-interp-additions.md` |
| Placement | offline, pre-deployment; new arm in CompositionScanner's skipped branch or a new scanner (decision pending) |

## What it does, in plain words

Run the original and the fine-tuned copy on a few hundred ordinary sentences that have nothing to do with the suspected change, subtract the average internal activity, and read the difference: decode it into words, or add it back into the original and let it talk. A narrow fine-tune leaves a readable shift even on unrelated text, so you learn what was changed without knowing the trigger.

## Similar tests already in Sabre

- **CompositionAnalysis**: finds the parent from the model card and compares weights, but skips finetune / merge / adapter relations with 'differences are expected'. MI-07 is the activation-level test for exactly that skipped branch
- **refusal direction, per-expert diff-of-means (expert_directions)**: same subtraction machinery, but harmful-vs-benign inside one model, not base-vs-fine-tune
- **lens.py + stability_lens_readout**: the decoding step already exists: which words a direction pushes
- **abliteration.activation_addition**: the steering readout already exists

## What it offers that Sabre does not have

A base-versus-fine-tune activation comparison. For the three relations the composition scanner skips, Sabre says nothing today. Detects the fine-tune's domain, trigger-conditioned behaviour without the trigger, and safeguard removal (the diff is refusal-direction-shaped), which is the 'absent by removal' case. Bonus on MoE: a routing difference per layer, which no current scanner reports.

## How expensive it is to run

- **Driver:** two sequential 122B loads; forward passes on 200 to 500 generic prompts at ~10 token positions; base activations cached to disk (megabytes); the steering readout is generation and is the slow part
- **On the 122B (m-gp1):** under an hour end to end; keep generations to a few dozen

## MoE conditioning

bucket tokens by the expert that fired in the base and diff only where the same expert fired in both models; report the fraction of tokens whose top-8 set changed and the router KL as a separate signal; for adapters the fine-tune side is base plus adapter loaded.

## Verdict

Build first. Prerequisite: the scanned model must have a downloadable declared parent; a base release has nothing to diff. Controls before quoting a number: positive = the 122B instruct vs a public abliterated republication; null = the same model in two quantisations.

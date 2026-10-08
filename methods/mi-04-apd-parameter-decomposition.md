# MI-04 APD, attribution-based parameter decomposition

| | |
|---|---|
| Cost tier | **Infeasible on our machine** |
| Single-pass signal | no |
| Operates on | weights |
| Gradient-free | partial (gradient attributions) |
| Paper | arXiv 2501.14926 (2025, Apollo) [CHECKED] |
| Sabre doc number | #4 in `sabre/docs/mech-interp-additions.md` |
| Placement | drop |

## What it does, in plain words

Instead of looking at what the model computes, this splits the model's *weights* into a sum of parts, each part responsible for one mechanism. The hope is to find the part that carries the safeguard, or a planted behaviour, as a separable object.

## Similar tests already in Sabre

- **WeightSurgeryScanner**: removes one known direction from the weights; APD would find the parts without being told the direction
- **StabilityAnalysis (weight-tamper radius)**: perturbs weights and reads the damage; does not decompose them
- **CompositionAnalysis**: compares weights to a parent, tensor by tensor

## What it offers that Sabre does not have

A weight-space decomposition into mechanism-carrying components. Sabre perturbs, ablates and compares weights but never decomposes them.

## How expensive it is to run

- **Driver:** each component is the size of the model; attributions are gradient-based; shown only on toy models
- **On the 122B (m-gp1):** does not fit: 122 GB per component, and the attribution step is gradient-based

## MoE conditioning

none; nothing published for MoE.

## Verdict

Drop for the 122B. Also fails the non-gradient rule (attribution by gradients), so it belongs in the excluded list.

# MI-05 SPD, stochastic parameter decomposition

| | |
|---|---|
| Cost tier | **Infeasible on our machine** |
| Single-pass signal | no |
| Operates on | weights |
| Gradient-free | trained with gradients |
| Paper | arXiv 2506.20790 (2026, Goodfire / Apollo); code github.com/goodfire-ai/spd [CHECKED] |
| Sabre doc number | #5 in `sabre/docs/mech-interp-additions.md` |
| Placement | drop |

## What it does, in plain words

The successor to MI-04: the same idea of splitting the weights into mechanism parts, made more stable by randomly masking parts during training and learning which parts matter for which inputs.

## Similar tests already in Sabre

- **WeightSurgeryScanner, StabilityAnalysis, CompositionAnalysis**: as MI-04

## What it offers that Sabre does not have

As MI-04, with a released library and better scaling, demonstrated at about 10^8 parameters, three orders below our target.

## How expensive it is to run

- **Driver:** components the size of the model, gradient-trained; memory for several model-sized copies
- **On the 122B (m-gp1):** does not fit on 2 x 96 GB with the model resident at 83 GB per card

## MoE conditioning

none published.

## Verdict

Drop for the 122B; keep as a research reference. Fails the non-gradient rule in its training step.

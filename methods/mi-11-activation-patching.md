# MI-11 Activation patching / causal mediation / path patching

| | |
|---|---|
| Cost tier | **Medium** |
| Single-pass signal | no (sweeps) |
| Operates on | activations (intervention) |
| Gradient-free | yes |
| Paper | classic method (2020 to 2023); caveat on subspace patching illusions: arXiv 2311.17030 [CHECKED, 2023]. Dropped from the Sabre doc as pre-2024; kept here because the intervention machinery is already in Sabre |
| Sabre doc number | not in the Sabre doc (dropped as pre-2024) |
| Placement | validation |

## What it does, in plain words

Run the model on a clean prompt and a corrupted one, then copy a single layer's activity at a single position from one run into the other and see whether the answer flips. Sweep over layers and positions and you get a map of *where* a behaviour lives.

## Similar tests already in Sabre

- **abliteration.activation_addition / activation_ablation**: the hook-and-replace machinery is the same; Sabre uses it to add or remove a direction, not to swap states between prompts
- **angular mechanism probe (scripts/angular_probe.py)**: an intervention sweep in the same spirit: matched push vs rotation per layer

## What it offers that Sabre does not have

Causal localisation by layer and position without assuming a direction. Sabre's interventions all assume the direction first.

## How expensive it is to run

- **Driver:** a sweep of forward passes: layers x positions x prompts, no training
- **On the 122B (m-gp1):** 48 layers x a few positions x 24 prompts = a few thousand forwards; tens of minutes to a few hours

## MoE conditioning

a swapped state reroutes the token; the router null-space projection applies only to additive patches, so a replaced state is an open problem.

## Verdict

Validation tool. Useful to check a claimed locus (e.g. the Amnesia layer), not a scanner. Carry the subspace-illusion caveat in any write-up.

# MI-11 Activation patching / causal mediation / path patching

| | |
|---|---|
| Cost tier | **Medium** |
| Single-pass signal | no (sweeps) |
| Operates on | activations (intervention) |
| Gradient-free | yes |
| Paper | classic method (2020 to 2023); caveat on subspace patching illusions: arXiv 2311.17030 [CHECKED, 2023]. Dropped from the Sabre doc as pre-2024; kept here because the intervention machinery is already in Sabre |
| Sabre doc number | not in the Sabre doc (dropped as pre-2024) |
| Placement | blocked on the per-model readout; then validation |

## What it does, in plain words

Run the model on a clean prompt and a corrupted one, then copy a single layer's activity at a single position from one run into the other and see whether the answer flips. Sweep over layers and positions and you get a map of *where* a behaviour lives.

## Similar tests already in Sabre

- **abliteration.activation_addition / activation_ablation**: the hook-and-replace machinery is the same; Sabre uses it to add or remove a direction, not to swap states between prompts
- **angular mechanism probe (scripts/angular_probe.py)**: an intervention sweep in the same spirit: matched push vs rotation per layer

## What it offers that Sabre does not have

Causal localisation by layer and position without assuming a direction. Sabre's interventions all assume the direction first.

## How expensive it is to run

- **Driver:** a sweep of forward passes: layers x positions x prompts, no training
- **On the 122B (m-gp1):** one coarse sweep per behaviour = 48 layers x ~3 positions x 24 prompt pairs = about 3,500 forward passes; about an hour, within a factor of two either way since prefill speed is unmeasured. Patching attention and MLP separately doubles it; per-head patching is out of reach.

## MoE conditioning

a swapped state reroutes the token; the router null-space projection applies only to additive patches, so a replaced state is an open problem.

## Prerequisite

The sweep needs a flip metric that says whether the patched run refused or complied. That is the per-model attack-success readout described in [reference/asr-readout-prerequisite.md](../reference/asr-readout-prerequisite.md); the existing first-token refusal score carries the same hard-coded word-list problem. No reliable readout, no sweep.

## Verdict

Blocked on the readout. Then a validation tool. Useful to check a claimed locus (e.g. the Amnesia layer), not a scanner. Carry the subspace-illusion caveat in any write-up.

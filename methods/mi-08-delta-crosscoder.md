# MI-08 Delta-Crosscoder (robust narrow-FT diffing)

| | |
|---|---|
| Cost tier | **Heavy** |
| Single-pass signal | no |
| Operates on | activations |
| Gradient-free | yes |
| Paper | arXiv 2603.04426 (2026) [CHECKED] |
| Sabre doc number | #8 in `sabre/docs/mech-interp-additions.md` |
| Placement | offline / deferred |

## What it does, in plain words

A crosscoder trained on the *difference* between the two models rather than on both, which makes it more robust to the fine-tune being small or noisy. Same question as MI-06, better conditioned.

## Similar tests already in Sabre

- **CompositionAnalysis**: as MI-06
- **MI-07**: the cheap version

## What it offers that Sabre does not have

As MI-06, more robust for narrow fine-tunes.

## How expensive it is to run

- **Driver:** as MI-06
- **On the 122B (m-gp1):** as MI-06

## MoE conditioning

as MI-06.

## Verdict

Defer. The spike proposed in the planning file (Delta-Crosscoder vs backdoored organisms) is replaced by MI-07.

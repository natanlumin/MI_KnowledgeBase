# MI-03 Concept-bottleneck SAEs (CB-SAE)

| | |
|---|---|
| Cost tier | **Heavy** |
| Single-pass signal | maybe |
| Operates on | activations |
| Gradient-free | yes (post-hoc) |
| Paper | arXiv 2512.10805 (2025) [CHECKED] |
| Sabre doc number | #3 in `sabre/docs/mech-interp-additions.md` |
| Placement | offline / deferred |

## What it does, in plain words

A sparse autoencoder whose features are tied, after training, to concepts a human named in advance. You get a dictionary where some entries are guaranteed to mean "refusal" or "medical advice", plus the usual unnamed remainder.

## Similar tests already in Sabre

- **SafetyAlignmentProbe**: the supervised half of CB-SAE is what the probe already does for refusal
- **Qwen3Guard compliance judge**: names concepts in the output, not in the activations

## What it offers that Sabre does not have

Named features inside the model rather than a verdict on its output, so a scan could say which internal concept fired, not only whether the answer was harmful.

## How expensive it is to run

- **Driver:** dictionary learning, partly supervised so somewhat fewer tokens than MI-01, still a corpus
- **On the 122B (m-gp1):** somewhat below MI-01; same order

## MoE conditioning

as MI-01.

## Verdict

Defer with MI-01 and MI-02.

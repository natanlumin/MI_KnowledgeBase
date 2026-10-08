# MI-02 Subspace-aware sparse autoencoders (SASA)

| | |
|---|---|
| Cost tier | **Heavy** |
| Single-pass signal | maybe |
| Operates on | activations |
| Gradient-free | yes |
| Paper | arXiv 2606.06333 (2026) [CHECKED] |
| Sabre doc number | #2 in `sabre/docs/mech-interp-additions.md` |
| Placement | offline / deferred |

## What it does, in plain words

A sparse autoencoder is the plain version of MI-01: it rewrites a layer's activity as a short list of features. The subspace-aware version lets one feature be a small *space* of directions instead of a single arrow, so a concept that needs several dimensions is not shattered into many fragments.

## Similar tests already in Sabre

- **Angular steering / rank-2 surgery**: Sabre already found that refusal on Nemotron is a plane, not a line; SASA is the dictionary-side version of that same observation
- **SafetyAlignmentProbe**: supervised, one concept

## What it offers that Sabre does not have

Multi-dimensional features without feature-splitting. Same unsupervised-discovery value as MI-01, with a representation that matches our 'many directions' finding.

## How expensive it is to run

- **Driver:** same corpus collection as MI-01, heavier training objective (block sparsity, adaptive rank)
- **On the 122B (m-gp1):** as MI-01

## MoE conditioning

as MI-01; the subspaces absorb some of the mixture spread but do not fix routing.

## Verdict

Defer with MI-01. If one dictionary is ever trained for the 122B, this is the objective to use.

# MI-12 Relevance Patching (RelP, layer-wise relevance propagation)

| | |
|---|---|
| Cost tier | **Medium, and suspect** |
| Single-pass signal | no |
| Operates on | activations |
| Gradient-free | no in semantics, but needs a backward pass |
| Paper | arXiv 2508.21258 (2025) [CHECKED] |
| Sabre doc number | #11 in `sabre/docs/mech-interp-additions.md` |
| Placement | excluded |

## What it does, in plain words

A faster stand-in for MI-11 that estimates each activation's share of the answer by propagating 'relevance' backwards through the network using fixed rules, instead of running thousands of patched forward passes.

## Similar tests already in Sabre

- **none**: Sabre has no attribution method; the J-space Jacobian lens is the nearest idea and is deliberately the gradient line

## What it offers that Sabre does not have

Per-token attribution in one forward and one backward pass.

## How expensive it is to run

- **Driver:** one forward plus one relevance backward pass through the 122B per prompt; autograd through our fused FP8 experts is unproven
- **On the 122B (m-gp1):** memory for all-layer activations of a 122B per prompt; feasibility unknown

## MoE conditioning

the backward pass crosses routing cells; no clean fix.

## Verdict

Exclude. A backward pass with respect to the input is what the non-gradient rule forbids; it also overlaps the J-space line.

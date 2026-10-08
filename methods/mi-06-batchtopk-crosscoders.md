# MI-06 BatchTopK crosscoders (model diffing)

| | |
|---|---|
| Cost tier | **Heavy** |
| Single-pass signal | no |
| Operates on | activations of base and fine-tune, paired |
| Gradient-free | yes |
| Paper | arXiv 2504.02922 (2025) [CHECKED] |
| Sabre doc number | #6 in `sabre/docs/mech-interp-additions.md` |
| Placement | offline / deferred |

## What it does, in plain words

Train one feature dictionary over *two* models at once, the original and a fine-tuned copy, so that each feature is tagged as shared, only-in-base, or only-in-fine-tune. The only-in-fine-tune features are what the fine-tune added, including a backdoor's trigger feature.

## Similar tests already in Sabre

- **CompositionAnalysis**: diffs the weights against the parent, and skips fine-tunes outright
- **MI-07 narrow-FT traces**: the cheap member of the same family, already recommended

## What it offers that Sabre does not have

The behavioural latents a fine-tune added, with causal effect, which a weight diff cannot name.

## How expensive it is to run

- **Driver:** MI-01's corpus collection twice, once per model, sequentially, since two 122B models are never resident together
- **On the 122B (m-gp1):** 2 x MI-01; days

## MoE conditioning

a fine-tune can change which experts fire, so a mixture-level crosscoder conflates routing change with feature change. Per-expert crosscoders multiply the corpus by ~32.

## Verdict

Defer. MI-07 answers the same question at a fraction of the cost; return here only if MI-07 reads a diff it cannot name.

# MI-01 Skip-transcoders

| | |
|---|---|
| Cost tier | **Heavy** |
| Single-pass signal | maybe (feature monitor once trained) |
| Operates on | activations (MLP input to MLP output) |
| Gradient-free | yes |
| Paper | arXiv 2501.18823 (2025), "Transcoders beat sparse autoencoders for interpretability" [CHECKED] |
| Sabre doc number | #1 in `sabre/docs/mech-interp-additions.md` |
| Placement | offline / deferred |

## What it does, in plain words

A transcoder learns to re-describe one layer of the model as a short list of named "features" (for example "this looks like a refusal", "this is about chemistry"). A skip-transcoder learns what the layer *does* (input to output) rather than what it *holds*, with a small shortcut term so it stays accurate. Once trained, you can watch which features light up on every token.

## Similar tests already in Sabre

- **SafetyAlignmentProbe**: trains a supervised classifier on the same activations, but for one labelled concept (refuse vs comply)
- **refusal direction (ActivationCache.get_refusal_direction)**: one direction, chosen by us; a transcoder finds thousands, chosen by the data

## What it offers that Sabre does not have

Unsupervised discovery: features we never thought to label, including a backdoor's trigger feature. Sabre today only measures directions it already knows to look for.

## How expensive it is to run

- **Driver:** dictionary learning on 10^8 to 10^9 tokens per layer; no shortcut through our prompt sets (a 32k-latent dictionary on tens of thousands of tokens overfits)
- **On the 122B (m-gp1):** 100M tokens of forward passes = 10 to 30 GPU-hours at an assumed 1 to 3k tok/s (never measured); ~5 TB of activations if stored for 3 layers; transcoder training itself < 1 h

## MoE conditioning

the MoE block output is a mixture of 8 of 256 experts, so one dictionary learns a blurred mixture function. Per-expert dictionaries need ~32x the corpus (each expert sees ~3% of tokens). The only affordable form is one routing-aware dictionary that takes the top-k mask as extra input.

## Verdict

Defer. Justified only if a probe gap is shown that known directions miss. One-off offline artefact per model, never per scan. No public transcoder exists for the 122B or Nemotron.

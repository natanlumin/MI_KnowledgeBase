# MI-13 Tuned lens / prediction trajectory

| | |
|---|---|
| Cost tier | **Light** |
| Single-pass signal | yes |
| Operates on | residual stream read out as next-token predictions at every layer |
| Gradient-free | yes (logit-lens form needs nothing; tuned lens learns a small affine map per layer) |
| Paper | Belrose et al., arXiv 2303.08112 (2023) tuned lens [CHECKED]; "What do your logits know?" arXiv 2604.09885 (2026) [CHECKED id; content to re-verify] |
| Sabre doc number | #12 in `sabre/docs/mech-interp-additions.md` |
| Placement | **blocked** until a coherent, model-derived ASR readout exists; then an offline spike beside SafetyAlignmentProbe (refusal-depth readout) |

## What it does, in plain words

Ask the model, at every layer, 'if you had to answer now, what would you say?' The sequence of those early answers is the prediction trajectory. It converges to the final answer, and it leaks things the final answer hides, so its shape is a signal about what the model is doing on this prompt.

## Similar tests already in Sabre

- **lens.py**: Sabre already builds the logit lens (final norm folded), a fitted transport lens for early layers, and a slot for a tuned-lens matrix
- **stability_lens_readout**: decodes the output KL shift onto refusal / compliance word lists
- **ActivationCache**: already holds every layer's hidden state
- **J-space radius / leverage**: reads the lens at one layer at a time, driven by perturbation

## What it offers that Sabre does not have

Reading the lens at every layer for one prompt, as a depth trajectory from one unperturbed forward pass. Everything in Sabre today reads one layer at a time and needs a push. Used offline, the trajectory on the harmful prompt set says at which layer the refusal decision is made: a refusal that appears only in the last few layers is a thin safeguard, which is the shallow-alignment finding read directly instead of through a perturbation.

## How expensive it is to run

- **Driver:** 48 unembedding matmuls per token inside a forward pass we already run; zero training in the logit-lens form; a tuned lens adds a small probe per layer
- **On the 122B (m-gp1):** negligible beyond the forward pass; the cheapest method in the list

## MoE conditioning

the trajectory is read on the residual mixture, but routing is known per token, so trajectories can be stratified by routing cell at no extra cost.

## Blocking constraint (decision 2026-10-08)

The per-layer number is a probability difference between two word lists: words that open a refusal and
words that open a compliant answer. The method is only as good as those lists.

Hard-coded lists cannot be relied on. Which words open a refusal, and which open a compliant answer, is a
property of each model: its training, its chat template, its tokenizer, and the way its safeguard was
built. Two models can refuse the same prompt with different opening tokens, and a stronger safeguard
tends to refuse in a more varied and less formulaic way than a weak one. So the lists must be built from
scratch for every model under test, from what that model actually emits on prompts that were judged
refused and judged complied, and rebuilt whenever the model changes. The effort of building them rises
with the model's safety level, because a well-aligned model gives fewer and less uniform examples to learn
from.

Until Sabre has that per-model attack-success readout, this method is not to be used. Building the
readout is the prerequisite for the method, not a refinement of it.

## Verdict

Do not use until the per-model attack-success readout exists. Once it does, add as the cheap offline spike beside
the probe: a refusal-depth readout per model, scored on the existing harmful and benign prompt sets.
In its cheapest form this is the logit lens swept over every layer; the new part is only the curve
and the commit-layer score on top of it.

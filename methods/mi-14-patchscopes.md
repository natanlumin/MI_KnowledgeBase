# MI-14 Patchscopes

| | |
|---|---|
| Cost tier | **Light** |
| Single-pass signal | no (extra passes) |
| Operates on | activations |
| Gradient-free | yes |
| Paper | Ghandeharioun et al. (2024) [VERIFY] |
| Sabre doc number | #13 in `sabre/docs/mech-interp-additions.md` |
| Placement | validation |

## What it does, in plain words

Take a hidden state from one prompt, paste it into a second prompt such as 'the word X means', and let the model finish the sentence. The model explains its own internal state in plain words, with no fixed word list.

## Similar tests already in Sabre

- **stability_lens_readout + lens.py**: the existing readout of what a direction means, restricted to a word list and weak at early layers
- **abliteration.activation_addition**: the hook that can inject a state; no code copies a state between prompts today

## What it offers that Sabre does not have

An open-vocabulary, generation-based readout of a state or direction, faithful at early layers where the logit lens fails. It is also the readout MI-07's paper uses.

## How expensive it is to run

- **Driver:** two forwards plus a few generated tokens per probed state; no training; generation is slow in our transformers adapter
- **On the 122B (m-gp1):** minutes per batch of probes if generations stay short

## MoE conditioning

a patch replaces the hidden state, so the router null-space projection does not apply cleanly; the patched token can reroute. Open problem.

## Verdict

Keep as a validation readout: use it to explain in words the vectors MI-07 or Amnesia produce. Not a detector.

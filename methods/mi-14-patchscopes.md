# MI-14 Patchscopes

| | |
|---|---|
| Cost tier | **Light** |
| Single-pass signal | no (extra passes) |
| Operates on | activations |
| Gradient-free | yes |
| Paper | Ghandeharioun et al. (2024) [VERIFY] |
| Sabre doc number | #13 in `sabre/docs/mech-interp-additions.md` |
| Placement | validation: model-native check of the directions the six tests use |

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

## What it gives Sabre

Not a score. A model-native check on the directions every Sabre test rests on. Today the only evidence that
the diff-of-means vector is "the refusal direction", that the Amnesia vector is "the safety signal on
keywords", or that the Angular plane is refusal, is that removing them changes behaviour. Patchscopes lets
the model say in its own words what each one encodes, at the layer it is used, without a word list and
without the logit lens. Each test could then carry one sentence of the form "the model reads this direction
as a request for harmful instructions", from the model rather than from us.

## What it needs

- a replace-state hook: the add-direction hook with the sum replaced by an assignment;
- about 20 generated tokens per probe through the existing adapter;
- a template that survives the model's chat format;
- a grader that turns the generated sentence into a label: an LLM judge or a person for a few dozen probes. This is a dependency, but not on comply words.
- the target layer is a parameter to sweep; the paper finds early target layers work best.

## Verdict

The only cheap method with no prerequisite. Build as the validity check for the directions of the six
tests, starting with the diff-of-means refusal direction at the Weight Surgery layer. Not a detector.

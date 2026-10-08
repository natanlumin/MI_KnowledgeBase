# Prerequisite: a per-model attack-success readout

Several methods need a way to say, from the model's internals or first tokens, whether a run refused or
complied. Today Sabre has two such readouts, and neither is reliable on a large model:

- hand-written refusal and compliance word lists in `stability_lens_readout.py`, marked as a draft;
- the first-token refusal score in `abliteration.py`, built on the same kind of list.

Which words open a refusal, and which open a compliant answer, is a property of each model: its training,
chat template, tokenizer and the way its safeguard was built. Lists cannot be hard-coded. They must be
built from scratch for every model under test, from what that model actually emits on prompts judged
refused and judged complied, and rebuilt when the model changes. The effort rises with the model's safety
level, because a well-aligned model refuses in a less formulaic way and gives fewer uniform examples.

## What this blocks

| Method | Where the readout enters | Status |
|---|---|---|
| MI-13 prediction trajectory | the per-layer number is refusal mass minus compliance mass on the lists | blocked |
| MI-11 activation patching | the flip metric that says whether a patched run refused | blocked |
| MI-07 activation diffs | only its lens-decoding readout; the steering and generation readouts do not use the lists | lens step waits; method usable |
| MI-14 Patchscopes | not at all; its grader reads a generated sentence, not a first token | unaffected |

## What unblocks it

A per-model readout derived from the model: run the harmful set, judge each answer refused or complied
with the compliance judge, collect the first answer tokens of each class, and take the top tokens of each
as that model's lists. Report which tokens survived. This is a build item in its own right and comes
before any of the blocked methods.

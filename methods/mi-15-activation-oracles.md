# MI-15 Activation Oracles (LLMs as activation explainers)

| | |
|---|---|
| Cost tier | **Heavy** |
| Single-pass signal | no |
| Operates on | activations |
| Gradient-free | trained reader |
| Paper | Anthropic Alignment Science blog (2025) per the originating session [CHECKED as blog]; venue to re-verify before citing |
| Sabre doc number | #14 in `sabre/docs/mech-interp-additions.md` |
| Placement | drop |

## What it does, in plain words

Train a second model to read the first model's internal activity directly and answer questions about it in English, like a radiologist reading a scan.

## Similar tests already in Sabre

- **Qwen3Guard compliance judge**: reads the output text, not the activations
- **none on activations**

## What it offers that Sabre does not have

Natural-language reading of activations for arbitrary questions.

## How expensive it is to run

- **Driver:** training an oracle to read 122B activations; a LoRA on the 122B does not fit our headroom
- **On the 122B (m-gp1):** not feasible on m-gp1

## MoE conditioning

unknown; nothing published.

## Verdict

Drop for now; niche (hidden knowledge / leakage), and the training step fails the non-gradient spirit.

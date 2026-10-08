# Cost basis

All costs are stated against one setup.

| Item | Value | Source |
|---|---|---|
| Target model | Qwen/Qwen3.5-122B-A10B-FP8: 48 blocks, 256 experts, top-8 | `sabre/docs/moe-ab-results.md` |
| Second model | NVIDIA Nemotron 3 Super 120B FP8: 88 blocks, 512 experts, top-22 | same |
| Machine | m-gp1, 2 x RTX PRO 6000 96 GB | `sabre/docs/nemotron-fp8-moe-results.md` |
| Headroom with the model resident | about 12 GB per card (model at ~83 GB per card) | `sabre/docs/nemotron-120b-vllm.md` |
| Reference cost of a current Sabre attack | forward passes over the prompt sets, 1 to 2 minutes | `sabre/docs/abliteration-scanners-bug-handoff.md` |
| Hidden size | not recorded locally; d = 4096 assumed where a number is given | assumption |
| Forward throughput for pure prefill | never measured; 1 to 3k tokens/s assumed | assumption, a one-minute benchmark would pin it |

Consequences used throughout:

- Two 122B models are never resident at once. Any two-model method is sequential loads plus disk.
- Per-expert conditioning multiplies a corpus by about 32 on the 122B, because each expert sees about 3 percent of tokens under top-8 of 256.
- Dictionary-learning methods cannot be run on our prompt sets: tens of thousands of tokens cannot train a 32k-latent dictionary.
- Generation is slow in the transformers adapter, so any readout that generates stays at a few dozen short generations.

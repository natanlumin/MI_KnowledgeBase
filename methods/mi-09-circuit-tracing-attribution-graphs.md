# MI-09 Circuit tracing / attribution graphs (cross-layer transcoders)

| | |
|---|---|
| Cost tier | **Very heavy** |
| Single-pass signal | no |
| Operates on | activations plus frozen attention |
| Gradient-free | mostly (edges are a local linear approximation) |
| Paper | Ameisen et al., Circuit Tracing; Lindsey et al., On the Biology of a Large Language Model; transformer-circuits.pub (2025) [CHECKED, not arXiv] |
| Sabre doc number | #9 in `sabre/docs/mech-interp-additions.md` |
| Placement | validation / drop |

## What it does, in plain words

Replace every layer's MLP with a learned interpretable stand-in (a transcoder across all layers), freeze the attention, then draw the graph of which features caused which on one prompt. You can read the refusal circuit as a diagram.

## Similar tests already in Sabre

- **AmnesiaScanner**: a hand-traced one-hop circuit: the attention-value path that carries the safety signal on keywords
- **J-space leverage (least-stable layer)**: which layer amplifies a push; a graph would say which features do

## What it offers that Sabre does not have

The whole causal graph, feature to feature, instead of one direction at one layer.

## How expensive it is to run

- **Driver:** transcoders at all 48 layers trained on billions of tokens, then human pruning and grouping per prompt
- **On the 122B (m-gp1):** weeks; never done for a 120B MoE

## MoE conditioning

the graph is built on the residual mixture; the circuit routes through different experts per input, so one graph is a cross-routing average. Per-routing-cell graphs have not been attempted by anyone.

## Verdict

Drop as a detector; validation tool only, and not affordable for the 122B.

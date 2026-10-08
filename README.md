# MI knowledge base

Mechanistic-interpretability (MI) methods reviewed for Sabre, the offline open-model vulnerability scanner.
One page per method, each answering the same four questions: what it does in plain words, which Sabre
tests are similar, what it offers that Sabre does not have, and how expensive it is to run on our
target. Compiled 2026-10-08 from the Sabre code (`luminai/sabre/src/scanner/`), the planning file
`sabre-interp-methods.md` (2026-09-28) and `sabre/docs/mech-interp-additions.md` (cost pass §7).

## Index

| ID | Method | Cost tier | Single-pass signal | Placement |
|---|---|---|---|---|
| MI-01 | [Skip-transcoders](methods/mi-01-skip-transcoders.md) | Heavy | maybe (feature monitor once trained) | offline / deferred |
| MI-02 | [Subspace-aware sparse autoencoders (SASA)](methods/mi-02-subspace-aware-saes.md) | Heavy | maybe | offline / deferred |
| MI-03 | [Concept-bottleneck SAEs (CB-SAE)](methods/mi-03-concept-bottleneck-saes.md) | Heavy | maybe | offline / deferred |
| MI-04 | [APD, attribution-based parameter decomposition](methods/mi-04-apd-parameter-decomposition.md) | Infeasible on our machine | no | drop |
| MI-05 | [SPD, stochastic parameter decomposition](methods/mi-05-spd-parameter-decomposition.md) | Infeasible on our machine | no | drop |
| MI-06 | [BatchTopK crosscoders (model diffing)](methods/mi-06-batchtopk-crosscoders.md) | Heavy | no | offline / deferred |
| MI-07 | [Narrow fine-tune traces in activation differences](methods/mi-07-narrow-ft-activation-diffs.md) | Light | no | offline, pre-deployment; new arm in CompositionScanner's skipped branch or a new scanner (decision pending) |
| MI-08 | [Delta-Crosscoder (robust narrow-FT diffing)](methods/mi-08-delta-crosscoder.md) | Heavy | no | offline / deferred |
| MI-09 | [Circuit tracing / attribution graphs (cross-layer transcoders)](methods/mi-09-circuit-tracing-attribution-graphs.md) | Very heavy | no | validation / drop |
| MI-10 | [CLT-Forge (scalable cross-layer transcoder library)](methods/mi-10-clt-forge.md) | Very heavy | no | drop |
| MI-11 | [Activation patching / causal mediation / path patching](methods/mi-11-activation-patching.md) | Medium | no (sweeps) | validation |
| MI-12 | [Relevance Patching (RelP, layer-wise relevance propagation)](methods/mi-12-relevance-patching-relp.md) | Medium, and suspect | no | excluded |
| MI-13 | [Tuned lens / prediction trajectory](methods/mi-13-lens-prediction-trajectory.md) | Light | yes | blocked: needs a per-model attack-success readout first; then an offline spike beside SafetyAlignmentProbe |
| MI-14 | [Patchscopes](methods/mi-14-patchscopes.md) | Light | no (extra passes) | validation |
| MI-15 | [Activation Oracles (LLMs as activation explainers)](methods/mi-15-activation-oracles.md) | Heavy | no | drop |

Excluded on the non-gradient rule: see [reference/excluded-gradient-methods.md](reference/excluded-gradient-methods.md).
The Sabre tests the comparisons refer to: [reference/sabre-tests.md](reference/sabre-tests.md).
The machine and model the costs are measured against: [reference/cost-basis.md](reference/cost-basis.md).

## The short answer

- **Affordable now:** MI-07 (base-vs-fine-tune activation diffs) and MI-14 (Patchscopes). Both are forward passes on existing prompt sets and reuse code Sabre already has.
- **Cheap but blocked:** MI-13 (prediction trajectory). Its readout rests on refusal and compliance word lists, and such lists cannot be hard-coded: they are a property of each model and must be built from scratch per model, with more effort as the model's safety level rises. Not to be used until Sabre has a per-model attack-success readout. See the page.
- **Build first:** MI-07. It fills a declared gap: the composition scanner skips fine-tunes, merges and adapters, and no Sabre test looks at what a fine-tune changed. Prerequisite: the scanned model must have a downloadable declared parent.
- **Heavy:** MI-01, MI-02, MI-03, MI-06, MI-08, MI-15 need dictionary learning on a corpus or a trained reader. MI-09 and MI-10 are beyond heavy. MI-04 and MI-05 do not fit the machine.
- **Fail the non-gradient rule:** MI-04 (gradient attributions), MI-05 (gradient-trained), MI-12 (backward pass), MI-15 (trained reader).

## Rules this knowledge base follows

1. Non-gradient only. Gradient and Jacobian methods overlap the J-space line and are listed only to stop them being re-proposed.
2. Citation tags are carried from the originating research session: `[CHECKED]` means the ID or venue was seen there, `[VERIFY]` means it came from model knowledge. **This knowledge base has not re-verified any ID.** Verify on the abstract page before any ID goes into code or a report.
3. Everything here is judged as an **offline, pre-deployment** addition to Sabre. The single-pass column is a property of the method, not a placement; Sabre has no runtime path and none is proposed. What the runtime engine (`linear`) has does not count as Sabre having it.
4. Cost tiers: *Light* = forward passes on our existing prompt sets (the cost of every current Sabre attack, one to two minutes on the 122B). *Heavy* = dictionary learning on 10^8 to 10^9 tokens or training something the size of the model. Nothing in between survives the 12 GB of headroom per card.
5. Every method gets its MoE-correct form, because the target is a 256-expert top-8 model and every method reads the residual mixture.

## Numbering

IDs follow the 15-row planning file. The Sabre doc dropped activation patching (MI-11 here) as pre-2024, so its numbers run one lower from that point; each page states its doc number.

# Which attention heads drive Indirect Object Identification in GPT-2 small?

A first solo mechanistic interpretability project. Goal: identify which attention heads causally contribute to GPT-2 small's choice of the indirect object (e.g. "Mary") over the repeated subject (e.g. "John") in sentences like *"When John and Mary went to the store, John gave a drink to ___"*, and compare the findings against the published IOI circuit (Wang et al., "Interpretability in the Wild").

**Status: in progress** (started 2026-09-27). See `LOG.md` for the dated research log.

## What's done so far
- Environment set up (Colab, TransformerLens, GPT-2 small)
- Sanity check: on the base prompt, logit(" Mary") − logit(" John") = 3.17 in TransformerLens; raw Hugging Face GPT-2 gives the same value only when the `<|endoftext|>` start token is prepended (2.53 without it)
- Built a templated dataset of 100 IOI prompts (random names, places, objects; mixed name order) plus "corrupted" twins where the subject is replaced by a third name
- Baseline on the dataset: clean logit diff 3.60, corrupted −0.32, accuracy (indirect object > subject) 100%
- Single-prompt observation: Layer 9 Head 9 puts ~70% of its last-token attention on " Mary" (descriptive only, not yet tested causally)

## In progress / next
- Sweep of all 144 attention heads using (a) corrupted-activation patching and (b) zero-ablation, measured as the fraction of the logit difference lost
- Comparison of top heads against the published IOI circuit
- Robustness checks (different templates, more prompts, other seeds)

## Results
_Pending. Will be added once the sweep is complete, including any negative or ambiguous findings._

## Reproducing
- Environment: Google Colab, Python 3.13, `transformer_lens==4.0.0`, `transformers==5.16.1`, GPU (T4) for the head sweep
- Seed: 0
- Notebook: `01_ioi_head_sweep.ipynb` (run cells top to bottom)
- Note: in `transformer_lens` 4.0.0, `from transformer_lens import utils` fails; hook names are written manually (e.g. `blocks.9.attn.hook_z`). If `HookedTransformer` fails to import right after a fresh install, restart the runtime.

## Limitations (so far)
- Templated prompts only; results may not generalize to natural text
- Single model (GPT-2 small), single task
- Replication of a known result; not yet a novel finding

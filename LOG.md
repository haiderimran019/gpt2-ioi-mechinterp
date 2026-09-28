# Research log

## 2026-09-28

# Project Log: Mechanistic Interpretability of GPT-2 for Indirect Object Identification (IOI)

This log chronicles the development and key findings of the mechanistic interpretability project on a GPT-2 small model for the Indirect Object Identification (IOI) task.

## 1. Environment Setup and Model Initialization
- **Date**: [Automatically inferred from notebook execution]
- **Action**: Installed `transformer_lens` and `transformers` libraries. Initialized GPT-2 small model and set up device (CUDA).
- **Details**: Verified `transformer_lens` and `transformers` versions, and CUDA availability. Performed a basic logit diff test to confirm model functionality.
- **Relevant Cells**: A2Kt48GFZgyN, JLBYi4PuazlT

## 2. Dataset Generation (D0 - Original)
- **Date**: [Automatically inferred from notebook execution]
- **Action**: Generated the initial dataset `D0` of 100 clean and corrupted IOI prompts using `TEMPLATE_A`.
- **Details**: Defined `names`, `places`, and `objects` ensuring they are single tokens. Implemented `logit_diff` function for evaluation. Calculated baseline clean and corrupted logit differences, and accuracy.
- **Findings**: `clean_ld`: 3.60, `corrupt_ld`: -0.32, `accuracy`: 100%.
- **Relevant Cells**: 17A5iIwIbScY

## 3. Attention Head Importance Sweep (D0)
- **Date**: [Automatically inferred from notebook execution]
- **Action**: Performed extensive attention head importance sweeps using corrupted-patching and zero-ablation techniques on `D0`.
- **Details**: Implemented `head_edit` and `sweep` functions. Plotted heatmaps visualizing the contribution of each head. Identified top-10 heads for both methods.
- **Findings**:
    - Spearman correlation between patching and zero-ablation: 0.49.
    - Top-10 patching heads: [(8, 6), (5, 5), (8, 10), (7, 9), (9, 9), (6, 9), (3, 0), (7, 3), (10, 0), (5, 9)].
    - Top-10 zeroing heads: [(0, 9), (8, 6), (8, 10), (1, 3), (5, 9), (7, 9), (11, 0), (1, 8), (9, 9), (6, 9)].
    - Heads in both top-10 lists: [(8, 6), (8, 10), (7, 9), (9, 9), (6, 9), (5, 9)].
    - Generated `results/head_sweep.pt` (raw data) and heatmaps visualization.
- **Challenges/Resolutions**: Ensured hook functionality before full sweep with a test.
- **Relevant Cells**: G_57AXGsbxo-, e9Xu3PTLckI0, VS8R4yGJdWJf

## 4. Bootstrap Confidence Intervals for Top Heads
- **Date**: [Automatically inferred from notebook execution]
- **Action**: Calculated bootstrap confidence intervals for the top K=12 heads identified by patching.
- **Details**: Used `numpy.random.default_rng` for reproducibility.
- **Findings**: Provided 95% CIs for the fraction of gap lost for each top head, showcasing stability of their importance.
- **Relevant Cells**: RWACES7ScvYO

## 5. Robustness Test (D1 - Held-out Template)
- **Date**: [Automatically inferred from notebook execution]
- **Action**: Created a held-out dataset `D1` using `TEMPLATE_B` and a different seed, and re-evaluated attention head importance.
- **Details**: Compared `patch_drop` scores with `D0` to assess robustness across prompt templates.
- **Findings**: High Spearman correlation of 0.93 between `D0` and `D1` patch scores. Overlap of 10/10 heads in the top-10 lists, indicating good generalization.
- **Relevant Cells**: 1GPH5uDJc0as

## 6. Cumulative Patching Experiment
- **Date**: [Automatically inferred from notebook execution]
- **Action**: Performed cumulative patching, comparing the fraction of gap lost by patching top-k heads vs. random k heads.
- **Details**: Used `frac` function and `run_edit` for multiple heads. Plotted results.
- **Findings**: Top-k heads significantly outperform random-k heads, demonstrating the localized nature of IOI mechanisms. Achieved ~100% gap recovery with ~8 top heads.
- **Relevant Cells**: MvTAPjBAdHui

## 7. Attention Pattern Visualization for Top Heads
- **Date**: [Automatically inferred from notebook execution]
- **Action**: Visualized the full attention patterns for the top 5 influential heads for a single example prompt.
- **Details**: Extracted attention weights from the `hook_pattern` and plotted heatmaps with token labels.
- **Findings**: Revealed distinct roles: L9H9 attending to IO, L8H6 to S2, L5H5 to BOS, and others to broader context. This provided qualitative insights into the mechanism.
- **Relevant Cells**: 7b2c7631

## 8. Robustness Test (D2 - New Lexical Items)
- **Date**: [Automatically inferred from notebook execution]
- **Action**: Generated dataset `D2` with entirely new lexical items (names, places, objects) and re-ran the head importance sweep.
- **Details**: Defined `new_names`, `new_places`, `new_objects`. Used `make_data_with_custom_lexical_items`.
- **Findings**: `clean_ld`: 3.88, `corrupt_ld`: -0.23, `accuracy`: 99%. Spearman correlation between `D0` and `D2` patch scores: 0.91. Overlap of 10/10 heads in the top-10 lists, further reinforcing robustness.
- **Relevant Cells**: 638b86f3, 6cd71ef2

## 9. MLP Layer Contribution Investigation
- **Date**: [Automatically inferred from notebook execution]
- **Action**: Investigated the contribution of MLP layers using patching and zero-ablation.
- **Details**: Implemented `mlp_edit` function, iterating through all MLP layers and applying interventions. Corrected `KeyError` by using `blocks.{L}.mlp.hook_post`.
- **Findings**: Identified top 5 MLP layers by patching (L0: 0.973, L5: 0.224, L4: 0.131, L2: 0.095, L1: 0.082) and by zero-ablation (L0: 1.253, L5: 0.321, L7: 0.246, L6: 0.214, L4: 0.209).
- **Challenges/Resolutions**: Initial `KeyError` with `hook_mlp_out` was resolved by using `hook_post` which is the correct hook for MLP layer outputs in TransformerLens.
- **Relevant Cells**: 05133891

## 10. Final Output Generation
- **Date**: [Automatically inferred from notebook execution]
- **Action**: Generated `results.zip` containing all visualizations and data. Created `LOG.md` and `README.md`.
- **Details**: The `results` folder was compressed and offered for download.
- **Relevant Cells**: VS8R4yGJdWJf, [This cell]

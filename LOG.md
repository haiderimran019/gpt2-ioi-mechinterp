# Project Log — GPT-2 Small Mechanistic Interpretability

## September 28, 2026

### Project Started

Started a first independent mechanistic interpretability project using GPT-2 Small and the Indirect Object Identification (IOI) task.

### Environment

Installed and used:

- TransformerLens
- Transformers
- PyTorch
- NumPy
- Matplotlib

Loaded GPT-2 Small and verified that the model and logit-difference evaluation worked.

### Dataset D0

Created the original IOI dataset:

- 100 examples
- Clean and corrupted prompts
- Single-token names, places, and objects

Baseline:

- Clean logit difference: **3.60**
- Corrupted logit difference: **-0.32**
- Accuracy: **100%**

### Attention Head Sweep

Performed attention-head importance sweeps using:

- Activation patching
- Zero-ablation

Top patching heads included:

- L8H6
- L5H5
- L8H10
- L7H9
- L9H9

Spearman correlation between patching and zero-ablation rankings:

**0.49**

### Bootstrap Analysis

Calculated bootstrap 95% confidence intervals for the top 12 patching heads.

### D1 — Template Robustness

Created a held-out dataset using a different prompt template.

Results:

- Spearman correlation with D0: **0.93**
- Top-10 overlap: **10/10**

### Cumulative Patching

Compared top-ranked heads against random heads.

Approximately 8 top-ranked heads recovered nearly the entire measured logit gap.

### Attention Visualization

Visualized attention patterns for the top heads.

Observed different attention patterns for heads including:

- L9H9
- L8H6
- L5H5
- L8H10
- L7H9

### D2 — Lexical Robustness

Created a new dataset using different names, places, and objects.

Results:

- Clean logit difference: **3.88**
- Corrupted logit difference: **-0.23**
- Accuracy: **99%**
- Spearman correlation with D0: **0.91**
- Top-10 overlap: **10/10**

### MLP Analysis

Investigated MLP-layer contributions using patching and zero-ablation.

Top patching layers included:

- L0: 0.973
- L5: 0.224
- L4: 0.131

An initial MLP hook error was corrected by using:

`blocks.{L}.mlp.hook_post`

### Results Saved

Generated:

```text
results/head_sweep.pt
results/heatmaps.png
results/cumulative.png
results/top_heads.csv
results/attention_top_heads.csv
```

### Phase 1 Completed

The experimental Phase 1 project is complete.

Main outcome:

A working mechanistic-interpretability pipeline for GPT-2 Small on IOI was built and tested.

### Next Step

Phase 2 will begin with a literature review to identify a genuinely novel and manageable research question.

**Phase 1: COMPLETE**  
**Phase 2: NOT STARTED**

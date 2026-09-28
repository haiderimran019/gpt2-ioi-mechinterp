# Mechanistic Interpretability of GPT-2 Small for IOI

## Status

**Phase 1: Complete**  
**Date: September 28, 2026**

This project investigates how GPT-2 Small performs the **Indirect Object Identification (IOI)** task using mechanistic interpretability techniques.

The project was completed as a first hands-on study of mechanistic interpretability.

## Model

- GPT-2 Small
- 12 layers
- 12 attention heads per layer
- ~124M parameters
- TransformerLens
- Google Colab / GPU

## Task

The model was tested on sentences such as:

> When John and Mary went to the store, John gave a drink to

The evaluation used **logit difference** between the expected indirect-object token and the subject token.

## Datasets

Three datasets were used:

- **D0:** 100 examples using the original template.
- **D1:** 100 examples using a different prompt template.
- **D2:** 100 examples using new lexical items.

## Methods

The project used:

- Attention-head importance sweeps
- Activation patching
- Zero-ablation
- Bootstrap confidence intervals
- Cumulative patching
- Attention-pattern visualization
- MLP-layer intervention
- Robustness testing

## Results

### Attention heads

Top patching heads on D0 included:

- L8H6
- L5H5
- L8H10
- L7H9
- L9H9

The Spearman correlation between patching and zero-ablation rankings was **0.49**.

Six heads appeared in both top-10 lists:

- L8H6
- L8H10
- L7H9
- L9H9
- L6H9
- L5H9

### Robustness

For the held-out template D1:

- Spearman correlation: **0.93**
- Top-10 overlap: **10/10**

For new lexical items D2:

- Spearman correlation: **0.91**
- Top-10 overlap: **10/10**

### Cumulative patching

Approximately 8 top-ranked heads recovered nearly the entire measured logit gap and substantially outperformed random head selection.

### MLP analysis

Important MLP layers were also identified.

Top layers by patching included:

- L0: 0.973
- L5: 0.224
- L4: 0.131

This suggests that IOI performance involves contributions from both attention heads and MLP layers.

## Conclusion

The experiments found a relatively small set of influential attention heads and significant MLP contributions in the controlled IOI task.

The results were robust to the tested prompt-template and lexical changes.

However, this project is primarily a **Phase 1 learning and replication project**, not a claim of discovering a new IOI circuit. IOI is already well studied.

## Limitations

- Small controlled datasets
- Limited prompt templates
- Only GPT-2 Small was tested
- Attention importance does not by itself establish a complete circuit
- Attention visualization does not by itself establish causality
- Interactions between attention heads and MLPs were not fully investigated

## Results Files

```text
results/
├── head_sweep.pt
├── heatmaps.png
├── cumulative.png
├── top_heads.csv
└── attention_top_heads.csv
```

## Next Step

**Phase 2:** Review existing mechanistic-interpretability literature and identify a small, genuinely novel research question building on this foundation.

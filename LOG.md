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

---

## September 29, 2026

### Phase 2 — Initial Literature Review

Started studying existing work on mechanistic interpretability and circuit discovery to understand the ideas behind the experiment.

### Wang et al. (2022)

Read and studied *Interpretability in the Wild: A Circuit for Indirect Object Identification in GPT-2 Small*.

Main things I learned:

- GPT-2 Small can perform IOI using a set of interacting attention heads.
- Different attention heads can have different functional roles.
- Ablation and path patching can be used to test whether components are causally important.
- A circuit is more than a list of correlated components; interventions are needed to test the proposed mechanism.

This paper gave me the main conceptual foundation for studying the IOI task.

### Conmy et al. (2023)

Read and studied *Towards Automated Circuit Discovery for Mechanistic Interpretability*.

Main things I learned:

- Finding circuits manually can be difficult and time-consuming.
- ACDC attempts to automate part of circuit discovery.
- The model can be viewed as a computational graph whose connections can be tested through interventions.
- Circuit discovery itself can be treated as a research problem.

This paper helped me understand how the manual circuit analysis in Wang et al. could potentially be made more systematic.

### Fey et al. (2024)

Read and studied *Position: Relational Deep Learning - Graph Representation Learning on Relational Databases*.

This is related work rather than a mechanistic interpretability paper.

Main things I learned:

- Real-world data often contains relationships between entities.
- Relational databases can be represented as graphs.
- Rows can be treated as nodes and relationships as edges.
- Graph neural networks can learn from these relationships directly.

This introduced me to relational deep learning and made me think more broadly about how structure is represented and used in machine learning.

### Current Understanding

The literature review helped me separate three ideas:

- **Understanding structure inside a model** — Wang et al.
- **Automatically discovering model structure** — Conmy et al.
- **Learning from structure in data** — Fey et al.

The first two are directly connected to the current GPT-2 IOI project. Relational deep learning is a separate but related direction that I am exploring.

### Phase 2 Status

**Initial literature review complete.**

I have not yet claimed any new research findings from the literature. The next step is to connect the ideas from these papers to the existing GPT-2 IOI setup and define a specific experimental question.- Corrupted logit difference: **-0.32**
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

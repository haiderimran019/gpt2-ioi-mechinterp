# Mechanistic Interpretability of GPT-2 Small for IOI

## Status

**Phase 1: Complete**  
**Phase 2: Complete**  
**Current Stage: Research Question Development**

This project investigates how GPT-2 Small performs the **Indirect Object Identification (IOI)** task using mechanistic interpretability techniques.

The first phase was a hands-on experimental study of GPT-2 Small. The second phase focused on reading existing work on mechanistic interpretability, circuit discovery, and related approaches.

---

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

## Phase 1 — Experimental Results

### Attention Heads

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

### Cumulative Patching

Approximately 8 top-ranked heads recovered nearly the entire measured logit gap and substantially outperformed random head selection.

### MLP Analysis

Important MLP layers were also identified.

Top layers by patching included:

- L0: 0.973
- L5: 0.224
- L4: 0.131

This suggests that IOI performance involves contributions from both attention heads and MLP layers.

## Phase 1 Conclusion

The experiments identified a relatively small set of influential attention heads and significant MLP contributions in the controlled IOI task.

The results were also robust across the tested prompt-template and lexical changes.

This was a **learning and replication project**, not a claim of discovering a new IOI circuit. IOI and its circuits have already been studied extensively.

## Phase 1 Limitations

- Small controlled datasets
- Limited prompt templates
- Only GPT-2 Small was tested
- Attention importance does not by itself establish a complete circuit
- Attention visualization does not by itself establish causality
- Interactions between attention heads and MLPs were not fully investigated

---

# Phase 2 — Literature Review

After completing the initial experiments, I studied existing research to understand the ideas behind circuit discovery and how my experiment relates to previous work.

### Wang et al. (2022)

*Interpretability in the Wild: A Circuit for Indirect Object Identification in GPT-2 Small*

This paper studies the IOI task in GPT-2 Small and identifies a circuit involving multiple groups of attention heads.

The main ideas I learned were:

- Model behavior can sometimes be explained through interacting components.
- Different attention heads can have different functional roles.
- Ablation can be used to test whether components are important.
- Path patching can be used to study information flow between components.
- A proposed mechanism needs causal testing rather than only correlation.

This paper provided the main conceptual background for the IOI experiments.

### Conmy et al. (2023)

*Towards Automated Circuit Discovery for Mechanistic Interpretability*

This paper introduces **ACDC**, a method for automating part of the process of circuit discovery.

The main ideas I learned were:

- Neural networks can be represented as computational graphs.
- Connections in the graph can be tested using interventions.
- Circuit discovery can be made more systematic and partially automated.
- The process still depends on choices such as the task, dataset, and evaluation metric.

This paper helped me understand how the manual circuit analysis in Wang et al. could potentially be approached more automatically.

### Fey et al. (2024)

*Position: Relational Deep Learning - Graph Representation Learning on Relational Databases*

This is **related work**, rather than a mechanistic interpretability paper.

The paper studies how neural networks can learn from relationships in relational databases by representing entities and their relationships as graphs.

The main ideas I learned were:

- Real-world data contains relationships and structure.
- Relational databases can be represented as graphs.
- Graph neural networks can learn from these relationships.
- This provides another perspective on how machine learning systems can use structure.

I am exploring this direction because of its broader connection to learning from structure, although it is a separate research area from the current IOI project.

## What I Understand So Far

The literature helped me distinguish between:

- **Understanding structure inside a trained model** — Wang et al.
- **Discovering model structure** — Conmy et al.
- **Learning from structure in data** — Fey et al.

These papers do not solve the same problem. The first two are directly connected to the current GPT-2 IOI project, while relational deep learning is a separate related direction.

## Current Stage

The experimental and initial literature-review phases are complete.

The next step is to use what I learned from the experiments and literature to define a **small, specific, and experimentally testable research question**.

I have not yet claimed a new research contribution.

---

## Repository

```text
gpt2-ioi-mechinterp/
├── literature/
├── 01_pynb.ipynb
├── LOG.md
└── README.md
```

The `01_pynb.ipynb` notebook contains the experimental work from Phase 1.

`LOG.md` records the development of the project over time.

The `literature/` directory contains notes from the papers studied during Phase 2.

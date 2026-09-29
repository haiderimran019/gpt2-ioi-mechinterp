# Mechanistic Interpretability of GPT-2 Small for IOI

## Status

**Phase 1: Complete**  
**Phase 2: Complete**  
**Current Stage: Research Question Development**  
**Date: September 29, 2026**

This project investigates how GPT-2 Small performs the **Indirect Object Identification (IOI)** task using mechanistic interpretability techniques.

The first phase was a hands-on experimental study of GPT-2 Small. The second phase focused on understanding existing research on circuit discovery and related approaches.

The project is currently moving from replication and literature review toward defining a small, experimentally testable research question.

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

## Phase 1 Results

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

The experiments found a relatively small set of influential attention heads and significant MLP contributions in the controlled IOI task.

The results were robust to the tested prompt-template and lexical changes.

However, this project is primarily a **Phase 1 learning and replication project**, not a claim of discovering a new IOI circuit. IOI is already well studied.

## Phase 1 Limitations

- Small controlled datasets
- Limited prompt templates
- Only GPT-2 Small was tested
- Attention importance does not by itself establish a complete circuit
- Attention visualization does not by itself establish causality
- Interactions between attention heads and MLPs were not fully investigated

## Phase 2 — Literature Review

After completing the initial experiments, I reviewed existing work to understand how the results fit into mechanistic interpretability research.

### Wang et al. (2022)

*Interpretability in the Wild: A Circuit for Indirect Object Identification in GPT-2 Small*

This paper investigates the IOI task in GPT-2 Small and identifies a circuit involving multiple groups of attention heads.

The main ideas I learned were:

- Model behavior can sometimes be explained using a smaller set of interacting components.
- Different attention heads can have different functional roles.
- Ablation can be used to test the importance of components.
- Path patching can be used to investigate information flow between components.
- A proposed circuit needs causal testing rather than only correlation or attention visualization.

This paper provides the main background for the IOI experiment in Phase 1.

### Conmy et al. (2023)

*Towards Automated Circuit Discovery for Mechanistic Interpretability*

This paper introduces **ACDC**, a method for automating part of the process of discovering neural network circuits.

The main ideas I learned were:

- Neural networks can be represented as computational graphs.
- Important connections can be tested through interventions.
- Circuit discovery can be treated as a systematic and partially automated process.
- Automated circuit discovery still depends on choices such as the task, dataset, and evaluation metric.

This connects to the first paper by asking whether parts of manual circuit discovery can be automated.

### Fey et al. (2024)

*Position: Relational Deep Learning - Graph Representation Learning on Relational Databases*

This paper is related work rather than a mechanistic interpretability paper.

It studies how neural networks can learn from relationships in relational databases by representing entities and their relationships as graphs.

The main ideas I learned were:

- Relational data contains structure that can be useful for prediction.
- Rows can be represented as nodes and relationships as edges.
- Graph neural networks can learn representations using these relationships.
- Relational deep learning provides another perspective on learning from structure.

This paper is being explored as a possible connection to broader questions about structure in machine learning.

## What I Understand So Far

The literature review helped separate three related ideas:

- **Understanding structure inside a trained model** — Wang et al.
- **Discovering model structure automatically** — Conmy et al.
- **Learning from structure in data** — Fey et al.

These papers do not solve the same problem. The first two are directly related to the current GPT-2 IOI project, while relational deep learning is a separate but potentially relevant research direction.

## Current Research Direction

The current goal is to move beyond reproducing known IOI results and identify a small question that can be tested experimentally.

Possible directions include:

- Testing whether identified important heads remain important under stronger distribution shifts.
- Studying interactions between attention heads and MLP layers.
- Comparing different circuit-discovery or intervention methods.
- Investigating whether automatically identified circuits remain stable across different IOI conditions.

No final research question has been selected yet.

## Results Files

```text
results/
├── head_sweep.pt
├── heatmaps.png
├── cumulative.png
├── top_heads.csv
└── attention_top_heads.csv
```

## Repository Structure

```text
my-first-research/
├── README.md
├── research-question.md
├── literature/
│   ├── papers.md
│   ├── wang-2022.md
│   ├── conmy-2023.md
│   └── relational-deep-learning.md
├── experiments/
│   └── README.md
├── notes/
│   ├── mechanistic-interpretability.md
│   ├── circuits.md
│   └── path-patching.md
├── results/
│   └── README.md
└── references.md
```

## Next Step

**Phase 3:** Narrow the research question and design an experiment that can test it.

The aim is to build on the Phase 1 GPT-2 IOI experiments and the Phase 2 literature review rather than claiming novelty before it has been tested.

# Which attention heads drive Indirect Object Identification in GPT-2 small?

A first solo mechanistic interpretability project. Goal: identify which attention heads causally contribute to GPT-2 small's choice of the indirect object (e.g. "Mary") over the repeated subject (e.g. "John") in sentences like *"When John and Mary went to the store, John gave a drink to ___"*, and compare against the published IOI circuit (Wang et al., "Interpretability in the Wild").

# Mechanistic Interpretability of GPT-2 for Indirect Object Identification (IOI)

This project investigates the mechanistic underpinnings of how a small GPT-2 model performs the Indirect Object Identification (IOI) task. We use various interpretability techniques, including attention head ablation/patching, robustness tests, and MLP layer contribution analysis, to pinpoint the crucial components responsible for the model's success in this specific linguistic task.

## Project Overview

The core idea is to understand which parts of the GPT-2 architecture (attention heads and MLP layers) are essential for identifying the indirect object in sentences like "When John and Mary went to the store, John gave a drink to Mary." By systematically perturbing these components and measuring the impact on the model's ability to predict the correct indirect object, we gain insights into the model's internal workings.

## Setup Instructions

To run this project, you will need a Google Colab environment or a local Python environment with GPU support.

1.  **Install Dependencies**:
    ```bash
    !pip install -q -U transformer_lens transformers
    ```
2.  **Import Libraries and Load Model**:
    The project relies on `torch`, `numpy`, `matplotlib`, `transformer_lens`, and `transformers`. The GPT-2 small model is loaded via `TransformerBridge.boot_transformers("gpt2")`.

    ```python
    import torch, random, numpy as np, time
    import matplotlib.pyplot as plt
    from importlib.metadata import version
    from transformer_lens import TransformerBridge

    device = "cuda" if torch.cuda.is_available() else "cpu"
    model = TransformerBridge.boot_transformers("gpt2", device=device)
    model.enable_compatibility_mode()
    model.eval()
    ```

## Methodology

### 1. Task and Model

*   **Task**: Indirect Object Identification (IOI). The model is given a prompt (e.g., "When A and B went to the P, B gave an O to") and is expected to predict the indirect object (A in this case).
*   **Model**: GPT-2 small (12 layers, 12 heads per layer).

### 2. Dataset Generation

*   **Prompt Templates**: Two templates were used:
    *   `TEMPLATE_A`: "When {first} and {second} went to the {p}, {subj} gave a {o} to"
    *   `TEMPLATE_B`: "After {first} and {second} arrived at the {p}, {subj} handed a {o} to"
*   **Lexical Items**: Custom lists of names, places, and objects were curated, ensuring each word is a single token for consistent analysis.
*   **Datasets**:
    *   `D0`: Primary dataset (N=100) using `TEMPLATE_A` for initial head importance sweeps.
    *   `D1`: Held-out dataset (N=100) using `TEMPLATE_B` to test robustness to template changes.
    *   `D2`: Held-out dataset (N=100) using `TEMPLATE_A` with entirely new lexical items to test robustness to vocabulary shifts.
*   **Evaluation Metric**: Logit Difference (`logit_diff`), calculated as the difference between the logit of the indirect object token and the subject token at the final position.

### 3. Intervention Techniques

*   **Corrupted-Patching**: Replacing the activation of a specific component (attention head or MLP layer) with the corresponding activation from a *corrupted* input. This measures how much information that component contributes to correcting the corrupted prediction.
*   **Zero-Ablation**: Setting the activation of a specific component to zero. This measures the absolute contribution of that component.
*   **Fraction of Gap Lost**: A normalized metric to quantify the impact of an intervention, representing the proportion of the clean-vs-corrupted logit gap that is recovered or lost.

## Key Results and Analysis

### 1. Attention Head Importance

*   **Influential Heads**: A core set of attention heads consistently demonstrated high importance. For `D0`, top patching heads include L8H6, L5H5, L8H10, L7H9, L9H9.
*   **Agreement**: The Spearman correlation between patching and zero-ablation scores was `0.49`, indicating a moderate but not perfect agreement between the two methods.
*   **Robustness to Template (D1)**: The head importance rankings were highly robust to changes in prompt template (`TEMPLATE_B`). The Spearman correlation between `D0` and `D1` patch scores was `0.93`, and `10`/10 of the top-10 heads overlapped.
*   **Robustness to Lexical Items (D2)**: The importance rankings also held up well with entirely new lexical items. The Spearman correlation between `D0` and `D2` patch scores was `0.91`, with `10`/10 of the top-10 heads overlapping. This suggests the mechanisms are generalizable beyond specific vocabulary.
*   **Cumulative Effect**: Patching a small number of top-ranked heads (e.g., 8 heads) recovered almost the entire logit gap, highlighting the sparse and localized nature of the IOI circuit.

### 2. Attention Pattern Analysis

Visualization of attention patterns for the top 5 heads (L8H6, L5H5, L8H10, L7H9, L9H9) revealed specialized functions:
*   **L9H9**: Strongly attends to the **indirect object** token, particularly from the final query token, suggesting a direct role in identifying the target.
*   **L8H6**: Exhibits strong attention to the **second mention of the subject** (S2), indicating its role in linking the subject to the action.
*   **L5H5**: Primarily attends to the **Beginning Of Sentence (`BOS`)** token, likely gathering global context or acting as a "default" attention sink.
*   **L8H10 and L7H9**: Show more diffuse patterns, attending to both S2 and earlier tokens, possibly for broader contextual understanding.

### 3. MLP Layer Contributions

*   **Key MLP Layers**: The MLP sweep identified several layers with significant contributions.
    *   Top 5 by patching: L0 (0.973), L5 (0.224), L4 (0.131), L2 (0.095), L1 (0.082).
    *   Top 5 by zero-ablation: L0 (1.253), L5 (0.321), L7 (0.246), L6 (0.214), L4 (0.209).
*   These results suggest that MLP layers, especially in early layers (L0) and mid-layers, play a substantial role in processing and transforming information critical for the IOI task, complementing the work of attention heads.

## Conclusions and Insights

The mechanistic interpretability analysis of GPT-2 for IOI reveals a complex yet discernible circuit:

1.  **Specialized Attention Heads**: Specific attention heads are dedicated to tracking key entities (indirect object, subject) and contextual information, demonstrating a clear division of labor within the attention mechanism.
2.  **Robustness**: The identified mechanisms are robust to variations in prompt templates and lexical items, suggesting they capture fundamental linguistic patterns rather than superficial cues.
3.  **MLP Importance**: MLP layers contribute significantly to the task, highlighting that IOI is not solely an attention-driven task but involves rich transformations within the feed-forward networks.
4.  **Localized Circuits**: The cumulative patching results indicate that the IOI task is primarily handled by a relatively small, identifiable subset of attention heads, supporting the idea of localized, interpretable circuits within large language models.

## Generated Files

All analysis results, including plots and data, are compiled into a `results.zip` file.
*   `results/head_sweep.pt`: Raw data from head importance sweeps.
*   `results/heatmaps.png`: Visualizations of head importance for original and held-out data.
*   `results/cumulative.png`: Plot showing cumulative patching effectiveness.
*   `results/top_heads.csv`: Details of top-contributing attention heads.
*   `results/attention_top_heads.csv`: Mean attention patterns for top heads.
*   `LOG.md`: Detailed chronological log of project development.
*   `README.md`: This document.

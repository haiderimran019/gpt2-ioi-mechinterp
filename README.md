# Which attention heads drive Indirect Object Identification in GPT-2 small?

A first solo mechanistic interpretability project. Goal: identify which attention heads causally contribute to GPT-2 small's choice of the indirect object (e.g. "Mary") over the repeated subject (e.g. "John") in sentences like *"When John and Mary went to the store, John gave a drink to ___"*, and compare against the published IOI circuit (Wang et al., "Interpretability in the Wild").

Status: "Head sweep complete (date). Path patching and an extension are next."
Results: paste Cell 12's output and embed results/heatmaps.png and results/cumulative.png. Add 2 or 3 sentences in your own words saying what stands out, with no claim stronger than the numbers support.
Reproducing: the install cell (pip install -U transformer_lens transformers, then restart), the TransformerBridge.boot_transformers("gpt2") plus enable_compatibility_mode() lines, the versions and GPU from results/environment.json, seed 0, and "run cells top to bottom".
Limitations: templated prompts only (two templates), GPT-2 small only, single-head and cumulative patching but no path patching yet, corrupted-patching and zero-ablation each have known caveats, N=100, and this reproduces a published result rather than adding a new one.

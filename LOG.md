# Research log

## 2026-09-28: Head sweep on clean notebook

**What I did:** rebuilt the environment from scratch (TransformerBridge with compatibility mode, transformer_lens 4.0.0). Built 100 templated IOI prompts, swept all 144 heads with corrupted-activation patching and zero-ablation, bootstrapped the top heads, replicated on a different template/seed, checked attention patterns, and tested cumulative patching against random-head controls.

**Setup problems and what fixed them:** HookedTransformer is not importable in transformer_lens 4.0.0 (use TransformerBridge); it also needs a newer transformers than Colab's default (`pip install -U transformers`, then restart).

**Results:** <paste Cell 12 output>

**Surprises / negative results:** <write what didn't match expectations, incl. heads with negative scores, weak agreement between methods, anything that failed to replicate>

**Comparison to the published circuit:** <fill in after reading Wang et al.; see below>

**Next:** <one sentence>

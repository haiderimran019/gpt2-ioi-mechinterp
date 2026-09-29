
# Wang et al. (2022)

## Paper

**Interpretability in the Wild: A Circuit for Indirect Object Identification in GPT-2 Small**

Kevin Wang, Alexandre Variengien, Arthur Conmy, Buck Shlegeris & Jacob Steinhardt (2022)

---

## Research Question

**How does GPT-2 Small internally perform the task of indirect object identification (IOI), and can we identify a human-understandable circuit of model components responsible for this behavior?**

The authors are not simply asking whether GPT-2 can solve the task. They already know that it can. Their goal is to understand **how the model computes the answer internally**.

The IOI task is illustrated by a sentence such as:

> “When Mary and John went to the store, John gave a drink to ___”

The correct answer is **Mary**.

A human can describe the rule simply:

> There are two names, Mary and John. John is the subject of the final clause, so the model should output the other name, Mary.

The researchers wanted to know whether they could find a corresponding mechanism inside GPT-2 Small that implements something like this rule.

They therefore tried to reverse-engineer the model's computation and identify a **circuit**: a smaller subgraph of the model's computational graph whose components work together to produce the IOI behavior.

---

## Main Findings

The researchers identified a circuit involving **26 attention heads**, organized into **7 functional categories**, that accounts for most of GPT-2 Small's IOI behavior.

The seven categories are:

1. **Name Mover Heads** — move the indirect object's name toward the output.
2. **Negative Name Mover Heads** — push information in the opposite direction and reduce the probability of the correct answer.
3. **S-Inhibition Heads** — help prevent the model from selecting the subject instead of the indirect object.
4. **Induction Heads** — identify repeated patterns and provide positional information.
5. **Duplicate Token Heads** — detect that a token has appeared before and communicate its previous position.
6. **Previous Token Heads** — provide information about preceding tokens that helps the induction mechanism.
7. **Backup Name Mover Heads** — compensate when the main Name Mover Heads are removed.

Together, these components form an information-processing pathway from the names in the input to the final prediction.

The authors found that their proposed circuit achieved about **87% of the full model's performance** on their IOI metric, demonstrating that the circuit captured a large portion of the model's behavior.

However, the authors also found important evidence that their explanation was **not complete**. When they systematically removed groups of components, the full model could sometimes continue performing the task because other components compensated for the removed ones. Their completeness tests therefore revealed gaps in the proposed explanation.

One particularly interesting finding was the existence of **Backup Name Mover Heads**. When the main Name Mover Heads were knocked out, GPT-2 Small's performance dropped by only about 5%, and other heads became responsible for moving names toward the output. This demonstrates that a model may contain redundant mechanisms that are difficult to discover if we only study the model under normal conditions.

---

## Methods

The researchers used several mechanistic interpretability techniques.

### 1. Circuit analysis

They treated GPT-2 Small as a computational graph.

The nodes are model components such as attention heads, while the edges represent interactions between those components.

A **circuit** is a smaller subgraph that is hypothesized to be responsible for a particular behavior.

In this paper, the behavior is IOI.

### 2. Knockouts / ablations

The researchers remove or replace the output of particular model components and observe what happens to the model's behavior.

The basic question is:

> “If I turn this component off, does the model become worse at IOI?”

If performance decreases substantially, that component may be involved in the mechanism.

The authors primarily use **mean ablation**, replacing a component's activation with its average activation rather than simply setting it to zero. This avoids some problems caused by zero ablation.

### 3. Path patching

This is one of the most important techniques in the paper.

Instead of simply asking:

> “Does head X matter?”

they ask:

> “Does information from head X reach the output through this particular pathway?”

They run the model on an original input and a modified input. They then replace activations along a particular pathway with activations from the modified input and observe how the model's output changes.

This allows them to trace **causal information flow** through the network.

They use this method repeatedly to work backward from the final logits and identify which attention heads influence which other components.

### 4. Attention-pattern analysis

After identifying candidate heads, the researchers inspect where those heads attend.

For example, they examine whether a head attends to:

- the indirect object,
- the subject,
- the previous occurrence of a token,
- or the token immediately before another token.

These attention patterns help them form hypotheses about what each head is doing.

### 5. Projections into the embedding space

They examine what information individual attention heads write into the residual stream and compare this with token directions such as the embeddings of the names.

This helps determine whether a head is actually **moving information about a particular name**.

### 6. Quantitative circuit validation

The researchers did not want to stop at a plausible story about what the heads were doing.

They therefore proposed three criteria:

**Faithfulness:**  
Does the proposed circuit perform the task similarly to the full model?

**Completeness:**  
Does the circuit contain the important components actually used by the model?

**Minimality:**  
Does the circuit avoid including components that are unnecessary for the behavior?

These criteria are important because simply finding components that correlate with a behavior does not necessarily mean that you have correctly identified the mechanism.

---

## Evidence

The evidence comes from several different types of experiments.

### Evidence 1 — GPT-2 Small actually solves IOI

Using more than 100,000 generated IOI examples, GPT-2 Small strongly preferred the correct indirect object.

Its mean logit difference between the indirect object and subject was **3.56**, and it predicted the indirect object over the subject **99.3% of the time**.

This establishes the behavior they are trying to explain.

### Evidence 2 — Name Mover Heads directly affect the output

Path-patching experiments identified attention heads that directly influence the final logits.

These heads preferentially copy the indirect-object name toward the output position.

This provided the first major part of the proposed circuit.

### Evidence 3 — S-Inhibition Heads control what the Name Movers attend to

The researchers found four heads that influence the queries of Name Mover Heads.

When these heads were patched, Name Mover Heads paid more attention to the subject rather than the indirect object.

The researchers therefore concluded that these heads help **inhibit the subject** and allow the Name Movers to preferentially select the indirect object.

### Evidence 4 — Duplicate Token and Induction Heads provide positional information

The researchers traced the S-Inhibition mechanism backward and found Duplicate Token Heads and Induction Heads.

Duplicate Token Heads attend to previous occurrences of the same token.

Induction Heads recognize patterns resembling:

**[A] [B] ... [A]**

and can help predict **[B]** after the repeated [A].

In the IOI circuit, these mechanisms provide information about where the first occurrence of the subject appeared.

### Evidence 5 — Removing the proposed circuit reduces performance

The identified circuit achieves approximately **87% of the full model's performance** according to the paper's faithfulness measurement.

That is evidence that the proposed circuit explains a substantial portion of the model's IOI computation.

### Evidence 6 — The model contains redundancy

When all of the main Name Mover Heads were knocked out, the model lost only about 5% of its logit-difference performance.

The researchers then found other heads that took over the Name Mover role.

They called these **Backup Name Mover Heads**.

This was especially important because it showed that simply ablating components can reveal a different mechanism that was hidden during normal operation.

---

## Limitations

The authors explicitly acknowledge that their circuit is **not a complete explanation of GPT-2's IOI mechanism**.

### 1. Some components remain unexplained

The researchers did not fully understand the attention patterns of the S-Inhibition Heads, nor the roles played by the MLPs and layer normalization.

So the proposed circuit is detailed, but it is not a complete account of every computation involved.

### 2. The completeness test revealed missing mechanisms

The circuit performed well according to faithfulness, but more difficult completeness tests found subsets of components whose removal affected the full model differently from the proposed circuit.

In other words:

> The circuit can reproduce much of the behavior, but that does not prove that it contains every mechanism the original model uses.

This is one of the most important lessons of the paper.

### 3. GPT-2 Small is much smaller than modern language models

GPT-2 Small has 12 layers and 12 attention heads per layer.

The authors explicitly note that it is orders of magnitude smaller than state-of-the-art transformer language models, so it remains uncertain how well these methods scale to much larger models.

### 4. The IOI task is deliberately narrow

The study focuses on one specific behavior in one specific model.

That makes the problem tractable, but it also means we cannot automatically conclude that the same circuit structure exists for other tasks or models.

The authors describe this tension themselves: the investigation is highly detailed but narrow.

### 5. Some of the proposed mechanisms need further validation

For example, the authors state that they would like to perform more detailed parameter-level analysis of the Duplicate Token and Induction Heads.

So some of their mechanistic descriptions are well-supported hypotheses rather than completely established explanations.

### 6. Adversarial examples expose additional unknowns

When the researchers constructed examples containing additional occurrences of the indirect object, GPT-2's performance degraded substantially.

On one such distribution, the model selected the subject instead of the indirect object **23.4% of the time**.

The researchers also state that they do not fully understand the mechanism responsible for this behavior.

---

## My Understanding

My current understanding of this paper is:

**The researchers are trying to open GPT-2's black box and figure out which internal components work together to produce one specific behavior.**

The behavior is simple:

> Given something like “Mary and John ... John gave something to ___”, output **Mary** rather than **John**.

Instead of looking at the whole GPT-2 model, they break its computation into smaller pieces, especially attention heads.

The important idea I take from the paper is that **an attention head is not necessarily doing the whole task by itself**.

Several heads can communicate with one another.

A simplified version of the mechanism is:

**1. Detect the repeated subject**  
Some heads notice that the subject appears again.

↓

**2. Figure out where the earlier subject occurred**  
Other heads communicate positional information.

↓

**3. Tell the Name Mover Heads what to avoid**  
S-Inhibition Heads make the Name Movers less likely to copy the subject.

↓

**4. Find the indirect object**  
The Name Mover Heads attend to the other name.

↓

**5. Copy the indirect object toward the output**  
The information about that name is moved through the residual stream toward the final prediction.

↓

**6. GPT-2 gives the indirect object's token a higher logit**  
Therefore it predicts “Mary.”

The really important methodological lesson for me is that **seeing a correlation is not enough**.

For example, if I notice that Head X attends to “Mary” whenever the model predicts “Mary,” I cannot immediately say:

> “Head X is responsible for predicting Mary.”

The authors instead use **causal interventions** such as path patching and knockouts to test whether changing the information flowing through that component actually changes the model's behavior.

That is what makes this paper important for mechanistic interpretability.

The second major lesson is that **finding one working circuit is not necessarily the same as finding the complete circuit**.

The Backup Name Mover experiment demonstrates this particularly well: removing the obvious Name Mover Heads did not destroy the behavior because other heads compensated for them.

So, in my own words:

> **This paper shows that a language model's behavior can sometimes be reverse-engineered into a relatively small set of interacting components, but proving that the discovered circuit is the complete mechanism is much harder than finding components that appear to matter.**

That distinction—**finding a plausible mechanism vs. causally establishing a complete mechanism**—is one of the main things I should carry forward when reading the next papers.

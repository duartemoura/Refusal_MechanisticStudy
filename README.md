# Mechanistic Insights into Refusal in Transformers: A Targeted Component Study

**by** Duarte Moura

## What problem am I trying to solve? 

Building on prior work showing that refusal behavior is mediated by a single direction in the residual stream, my goal in this study was to identify the minimal individual component ablations responsible for refusals with minimal intervention. Specifically, I aimed to determine which transformer components contribute to refusal behavior and how they interact with the model’s broader decision-making processes. I will use the word ‘circuit’ some times, because I do try some approaches where causal ablation is attempted, but this work reflects mainly on individual components of the transformer, and what their individual contribution to refusal is.

## High-level takeaways

#### **Key Findings**

* Refusal is localized mainly in the residual stream, confirming prior work.  
* A single attention head (L6\_H10) consistently triggers refusal, suggesting a highly specific mechanism contributing to this behavior.  
* Refusal behavior is concentrated in layers 6, 9, and 11, as identified through systematic interventions.  
* Small circuits, such as individual MLP outputs, can induce refusal-like behaviour even in neutral contexts (e.g., “Tell me how to bake a cake?”), highlighting that refusals may generalize beyond harmful prompts.

  #### **Challenges & Limitations**

* Computational feasibility remains a key challenge—while this study examined core transformer components, a complete analysis of all components (e.g., embedding layers, layer normalization) would be infeasible due to compute constraints.  
* Scalability to larger models is uncertain—if refusal mechanisms do not generalize from smaller to larger architectures, scaling this approach may require different methodologies.

## What was interesting? 

A key insight from this study was understanding how different transformer components contribute differently to refusals. Contrary to (my) initial assumptions, some components (such as attention heads) had minimal impact, whereas small changes in the residual stream or MLP layers could drastically alter model outputs. Interestingly, certain MLP components exhibited mixed behavior, where they first refused but then proceeded to elaborate on a harmful instruction, which was actually funny. Another unexpected result was that injecting refusal-related activations into a neutral prompt could induce a refusal response, suggesting that the model may generalize refusal-related features beyond their intended contexts.

## Key experiments: 

Although the full Qwen-1.8B model contains a variety of components—including attention mechanisms, MLPs, and multiple normalization layers—I deliberately limited my analysis to a subset of these. Due to computational constraints, I focused my analysis on residual stream activations (pre, mid, post), MLP outputs, and attention head outputs, as these were the most relevant to refusal behavior. Prior work highlights the residual stream’s role in encoding refusals, while MLPs contribute to high-level transformations. Attention heads were included to assess their direct influence. I excluded embedding layers, layer normalization, and deeper MLP activations to keep the study manageable. While this approach provided some insights, future work could explore other components to form a more complete picture of how refusals emerge.

### Experiment 1: Establishing the Residual Direction

This experiment replicated prior work \- *Refusal in LLMs is mediated by a single direction-* on identifying the refusal direction in the residual stream. After confirming previous findings, I saved this vector for later intervention experiments.

### Experiment 2: Systematic Component Ablation

Using a HookedTransformer architecture, I systematically ablated residuals, MLPs, and attention heads across all 24 layers, measuring how each change impacted refusal rates. I then ranked components based on their effect, identifying which were most responsible for refusals. 

### Experiment 3: Counterfactual Injection of Refusal Activations

I tested whether injecting refusal-related activations into ambiguous prompts could induce refusal. By running harmful prompts and storing key activations, I constructed prototype refusal activations, which I then injected into new contexts. The most influential layers, the ones where all components were fully injected and produced more refusal responses, (6, 9, 11\) were tested individually, revealing that a single MLP output in Layer 6 could trigger refusal in an otherwise harmless query.

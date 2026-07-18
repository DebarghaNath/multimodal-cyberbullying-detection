# Results

This document summarizes the training process and evaluation results of the proposed **Multimodal Cyberbullying Detection** framework built on **InternVL2.5-8B**, **LoRA-based Supervised Fine-Tuning (SFT)**, and **Direct Preference Optimization (DPO)**.

---

# Training Dynamics

## Stage I: Supervised Fine-Tuning (SFT)

The first training stage adapts the pretrained **InternVL2.5-8B** model to the multimodal cyberbullying detection task using supervised learning.

### Observations

- Training loss decreased rapidly from **1.69** to approximately **1.03** within the first **50 global steps**, indicating fast adaptation to domain-specific patterns.
- Training remained stable despite challenging samples involving sarcasm, emojis, and intersemiotic dissonance.
- Validation loss gradually converged around **0.91**, while the final training loss reached **0.86**, indicating effective generalization without significant overfitting.

<p align="center">
  <img src="/assets/SFT_Loss.jpg" width="700" alt="SFT Training Loss">
</p>

---

## Stage II: Direct Preference Optimization (DPO)

The second stage improves the model's reasoning capabilities by optimizing preferred responses over rejected responses using **Direct Preference Optimization (DPO)**.

### Observations

- Training loss started near **0.693** (corresponding to **−ln(0.5)**) and steadily decreased to approximately **0.47**.
- Validation loss closely followed the training curve, converging around **0.49**, demonstrating stable preference learning.
- DPO successfully refined the model's decision boundaries, reducing literal interpretations and improving detection of subtle multimodal harm.

<p align="center">
  <img src="/assets/DPO_Loss.jpg" width="700" alt="DPO Training Loss">
</p>

---
## 3. Confusion Matrix Analysis

### Stage I: Supervised Fine-Tuning (SFT)

The confusion matrix obtained after **Supervised Fine-Tuning (SFT)** reflects the model's initial adaptation to the multimodal cyberbullying domain. Through supervised learning, the model successfully acquired the forensic output schema and learned to recognize explicit bullying patterns. However, its decision boundaries were still largely influenced by literal multimodal interpretations.

<p align="center">
  <img src="/assets/SFT_confusion_matrix.jpg" width="500" alt="SFT Confusion Matrix">
</p>

#### Key Observations

- The model successfully detected many explicit instances of cyberbullying, demonstrating effective transfer of domain knowledge from the pretrained InternVL2.5-8B backbone.
- A considerable number of **false negatives** remained, indicating that the model frequently interpreted subtle multimodal memes as benign content.
- Memes relying on **sarcastic dissonance**, **coded symbolism**, **normative comparison**, and other implicit rhetorical mechanisms were particularly challenging.
- Several **false positives** were also observed, suggesting uncertainty when distinguishing between satirical humor and genuine targeted harassment.

Overall, the SFT model learned the **forensic annotation format**, but its reasoning remained primarily pattern-based rather than context-aware.

---

### Stage II: Direct Preference Optimization (DPO)

Following Direct Preference Optimization, the confusion matrix exhibits a substantially better calibrated classifier. Rather than learning only *what* labels to predict, DPO teaches the model *why* one reasoning process should be preferred over another by directly optimizing expert-preferred forensic explanations.

<p align="center">
  <img src="/assets/DPO_confusion_matrix.jpg" width="500" alt="DPO Confusion Matrix">
</p>

#### Key Observations

- The number of correctly identified bullying instances increased, resulting in an **84% recall** for the bullying class.
- False negatives were noticeably reduced, demonstrating improved recognition of subtle multimodal cyberbullying that had previously been interpreted literally.
- The model became significantly more robust when analyzing memes involving sarcasm, symbolic imagery, emojis, and multimodal contextual interactions.
- Although some false positives remain, these predominantly correspond to intentionally ambiguous memes where satire closely resembles implicit harassment, reflecting a deliberate bias toward safer moderation.

The DPO stage therefore improved the model's **normative judgment**, enabling it to better differentiate genuine harmful intent from superficially similar but benign content.

---

### Comparative Analysis

The progression from SFT to DPO clearly illustrates the contribution of preference optimization beyond conventional supervised learning.

| Supervised Fine-Tuning (SFT) | Direct Preference Optimization (DPO) |
|------------------------------|--------------------------------------|
| Learns the forensic output structure | Refines normative decision boundaries |
| Strong performance on explicit bullying | Improved detection of implicit bullying |
| Higher false-negative rate | Reduced false negatives |
| Literal multimodal reasoning | Context-aware multimodal reasoning |
| Greater boundary ambiguity | Better calibrated decision boundaries |

Rather than simply improving classification accuracy, **Direct Preference Optimization fundamentally changes the model's reasoning behavior**. The model transitions from recognizing explicit multimodal patterns to performing structured sociolinguistic reasoning, allowing it to identify subtle cyberbullying that emerges only through the interaction between visual content, textual cues, emojis, sarcasm, and cultural context.

This progression demonstrates that while **Supervised Fine-Tuning** provides the foundational forensic reasoning capability, **Direct Preference Optimization** is responsible for aligning that reasoning with expert human judgment, thereby producing more reliable, interpretable, and trustworthy moderation decisions.

---

# Performance by Bullying Type

| Bullying Type | Detection Accuracy | Labeling Accuracy |
|---------------|------------------:|------------------:|
| Identity-Based | 97.0% | 63.6% |
| Gender-Based | 89.5% | 63.2% |
| Physical Appearance | 84.0% | 60.0% |
| Religious / Political | 77.8% | 44.4% |
| Relational / Social | 73.7% | 36.8% |
| Cognitive / Intellectual | 71.4% | 0.0% |

### Analysis

The framework performs exceptionally well on **identity-based** and **gender-based** cyberbullying, where harmful intent is more consistently represented. Performance gradually decreases for categories requiring deeper sociocultural reasoning, such as **cognitive/intellectual** and **relational** bullying.

---

# Performance by Rhetorical Mechanism

| Mechanism | Detection Accuracy | Labeling Accuracy |
|-----------|------------------:|------------------:|
| Punching Down | 100.0% | 0.0% |
| Coded Symbolism | 93.3% | 6.7% |
| Violent Juxtaposition | 90.0% | 33.3% |
| Normative Comparison | 86.2% | 17.2% |
| Metaphorical Dehumanization | 80.0% | 35.0% |
| Sarcastic Dissonance | 77.1% | 54.2% |
| None (Benign) | 73.1% | 73.1% |

### Analysis

The model effectively **detects harmful intent**, particularly for mechanisms involving social power imbalance such as **Punching Down** and **Coded Symbolism**. However, assigning the exact rhetorical mechanism remains substantially more difficult, highlighting the gap between recognizing harm and explicitly explaining it.


---

# Performance Across Severity Levels

| Severity | Detection Accuracy | Labeling Accuracy |
|----------|------------------:|------------------:|
| High | 87.3% | 58.7% |
| Medium | 88.6% | 50.0% |
| Low | 69.2% | 30.8% |
| None | 75.0% | 73.5% |

### Analysis

The framework performs strongly on **medium** and **high-severity** cyberbullying, where explicit multimodal evidence is available. Performance decreases noticeably for **low-severity** attacks, which often rely on sarcasm, subtle stereotypes, or cultural references that closely resemble benign humor.


---

# Qualitative Analysis

The proposed **Explainable Forensic Schema** enables transparent and auditable decision-making by requiring the model to justify every prediction through structured multimodal reasoning.

After DPO alignment, the model demonstrates:

- Improved multimodal reasoning across image and text.
- Better understanding of sarcasm and contextual humor.
- Stronger recognition of coded symbolism and implicit hostility.
- Reduced literal interpretations of multimodal content.
- More consistent severity estimation and target identification.

Instead of producing only a binary prediction, the model generates an interpretable reasoning chain linking **visual evidence**, **textual cues**, and **contextual interpretation**, allowing moderators to verify each decision.

---

# Key Takeaways

- **80.60%** overall classification accuracy.
- **84% recall** for bullying detection.
- Strong performance on implicit multimodal cyberbullying.
- DPO significantly improves reasoning quality and decision boundary calibration.
- The Explainable Forensic Schema transforms the system from a black-box classifier into an interpretable forensic moderation framework.

Although challenges remain for culturally specific and highly subtle memes, the proposed **InternVL2.5-8B + LoRA + DPO** pipeline demonstrates that combining multimodal reasoning with structured explainability provides a robust and transparent solution for modern cyberbullying detection.

# Results

This document summarizes the training dynamics and evaluation results of the proposed **Multimodal Cyberbullying Detection** framework based on **InternVL2.5-8B**, **LoRA**, and **Direct Preference Optimization (DPO)**.

---

# Training Dynamics

## Stage I: Supervised Fine-Tuning (SFT)

The first stage adapts the pretrained InternVL2.5-8B model to the multimodal cyberbullying detection task using supervised learning.

### Observations

- The training loss decreased rapidly from **1.69** to approximately **1.03** within the first **50 global steps**, indicating fast adaptation to explicit cyberbullying patterns.
- Despite minor oscillations caused by complex multimodal cases (e.g., sarcasm and intersemiotic dissonance), training remained stable throughout optimization.
- The validation loss gradually converged and stabilized around **0.91**, indicating effective generalization without noticeable overfitting.
- The small gap between the final training loss (**0.86**) and validation loss (**0.91**) suggests successful domain adaptation.

### Training Curve

<p align="center">
  <img src="../assets/sft_loss_curve.png" width="700" alt="SFT Training Loss">
</p>

---

## Stage II: Direct Preference Optimization (DPO)

The second stage refines the model's reasoning ability using **Direct Preference Optimization (DPO)** with a conservative learning rate of **5 × 10⁻⁷**.

### Observations

- Training loss began near **0.693** (corresponding to **−ln(0.5)**) and steadily decreased to approximately **0.47**.
- The decreasing loss indicates that the model increasingly preferred expert-quality reasoning over inferior responses.
- Validation loss closely followed the training curve and converged around **0.49**, demonstrating strong generalization and stable preference learning.

### Training Curve

<p align="center">
  <img src="../assets/dpo_loss_curve.png" width="700" alt="DPO Training Loss">
</p>

---

# Final Model Evaluation

The final aligned model was evaluated on a held-out test set containing **201 multimodal meme samples**.

## Overall Performance

| Metric | Value |
|---------|-------:|
| **Accuracy** | **80.60%** |
| **Macro F1** | **0.79** |
| **Weighted F1** | **0.81** |

The model correctly classified **162 out of 201** test samples.

### Key Observations

- High sensitivity toward implicit cyberbullying.
- Strong precision for bullying detection.
- Conservative decision boundaries occasionally produced false positives on ambiguous humor.
- Remaining false negatives primarily involved culturally specific or highly implicit memes.

---

## Classification Report

| Class | Precision | Recall | F1-score | Support |
|------|----------:|--------:|---------:|--------:|
| Bullying | 0.86 | 0.84 | 0.85 | 133 |
| Non-Bullying | 0.70 | 0.74 | 0.72 | 68 |

---

## Confusion Matrix

| | Predicted Bullying | Predicted Non-Bullying |
|---|---:|---:|
| **Actual Bullying** | **112** | **21** |
| **Actual Non-Bullying** | **18** | **50** |

<p align="center">
  <img src="../assets/confusion_matrix.png" width="600" alt="Confusion Matrix">
</p>

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

### Observations

- Identity-based and gender-based cyberbullying were detected with the highest accuracy.
- Long-tail categories such as cognitive or intellectual bullying remained challenging to classify precisely.
- Performance decreased as the semantic complexity and contextual dependence of the bullying category increased.

<p align="center">
  <img src="../assets/type_accuracy.png" width="700" alt="Performance by Bullying Type">
</p>

---

# Performance by Bullying Mechanism

| Mechanism | Detection Accuracy | Labeling Accuracy |
|-----------|------------------:|------------------:|
| Punching Down | 100.0% | 0.0% |
| Coded Symbolism | 93.3% | 6.7% |
| Sarcastic Dissonance | 77.1% | 54.2% |

### Observations

- The model consistently recognized harmful intent expressed through implicit rhetorical mechanisms.
- Explicit labeling of nuanced sociolinguistic mechanisms remained more challenging.
- Sarcastic dissonance achieved the strongest labeling performance due to its relatively distinctive multimodal structure.

<p align="center">
  <img src="../assets/mechanism_accuracy.png" width="700" alt="Performance by Mechanism">
</p>

---

# Performance Across Severity Levels

### Observations

- **High Severity:** 87.3% detection accuracy.
- **Medium Severity:** 88.6% detection accuracy.
- **Low Severity:** 69.2% detection accuracy with only **30.8%** accurate severity labeling.

Lower-severity cyberbullying often relied on subtle multimodal cues and semantic ambiguity, making it more difficult to distinguish from benign humor.

<p align="center">
  <img src="../assets/severity_accuracy.png" width="700" alt="Performance by Severity">
</p>

---

# Qualitative Analysis

The Explainable Forensic Schema enables the model to produce transparent and interpretable predictions rather than simple binary classifications.

### Key Improvements after DPO

- Improved multimodal reasoning.
- Better understanding of sarcasm and contextual humor.
- Stronger detection of coded symbolism.
- Reduced literal interpretations.
- More reliable identification of implicit cyberbullying.

The generated forensic reasoning chain explicitly links visual evidence, textual cues, and contextual interpretation before assigning structured labels.

---

# Summary

The proposed **InternVL2.5-8B + LoRA + DPO** framework demonstrates strong performance on implicit multimodal cyberbullying detection.

## Highlights

- **80.60%** overall test accuracy.
- Robust detection of implicit harmful content.
- High recall (**84%**) for bullying instances.
- Explainable forensic reasoning for every prediction.
- Improved robustness through Direct Preference Optimization.

Although challenges remain for culturally specific and highly subtle cases, the framework successfully shifts cyberbullying detection from **binary classification** toward **transparent multimodal forensic reasoning**.

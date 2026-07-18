# Explainable Forensic Schema

## Motivation

Traditional cyberbullying detection systems typically produce a single binary prediction (e.g., **Bullying** or **Non-Bullying**) without explaining the reasoning behind the decision. Such black-box predictions are difficult to verify, audit, or trust, particularly for high-stakes content moderation.

To address this limitation, the proposed framework adopts an **Explainable Forensic Schema**, requiring the model to generate structured evidence alongside every prediction. Rather than only determining whether a meme is harmful, the model explains **why** it reached that conclusion by explicitly reasoning over both visual and textual modalities.

This transforms the model from a simple classifier into an interpretable decision-support system suitable for forensic analysis and human moderation.

---

## Design Objectives

The forensic schema was designed to:

- Improve transparency of model predictions.
- Provide an auditable reasoning trail for every decision.
- Encourage explicit multimodal reasoning rather than implicit pattern matching.
- Reduce hallucinated explanations by enforcing a fixed output structure.
- Produce machine-readable outputs suitable for downstream moderation systems.

---

## Two-Stage Reasoning Process

The schema separates prediction into two complementary components:

### 1. Analysis Module

The model first constructs a multimodal reasoning chain by identifying and relating evidence from both modalities.

The analysis captures:

- **Visual evidence** extracted from the image.
- **Textual evidence** obtained from embedded OCR text or captions.
- **Multimodal interpretation** explaining how the image and text interact to convey meaning.
- **Confidence estimation** for the generated reasoning.

This intermediate reasoning step forces the model to justify its prediction before assigning labels.

---

### 2. Labeling Module

After constructing the reasoning chain, the model predicts structured categorical labels describing the detected cyberbullying.

The annotation taxonomy captures:

- **Bullying status**
- **Target**
- **Bullying mechanism**
- **Bullying type**
- **Severity level**

All labels are selected from predefined **closed vocabularies**, ensuring consistency across annotations while preventing the generation of unsupported categories.

---
### Closed-Set Taxonomy

To ensure consistent annotations and prevent label hallucinations, all predictions are constrained to predefined categorical label sets.

| Field | Allowed Labels |
|-------|----------------|
| **Bullying** | `Yes`, `No` |
| **Target** | `individual`, `individual(multiple)`, `demographic_group`, `organization`, `occupational_group`, `none` |
| **Mechanism** | `sarcastic_dissonance`, `violent_juxtaposition`, `normative_comparison`, `coded_symbolism`, `metaphorical_dehumanization`, `punching_down`, `other`, `none` |
| **Type** | `relational_social`, `gender_based`, `identity_based`, `physical_appearance`, `cognitive_intellectual`, `religious_political`, `other`, `none` |
| **Severity** | `none`, `low`, `medium`, `high` |

Restricting the output space to these closed vocabularies improves annotation consistency, simplifies quantitative evaluation, and enables reliable supervised learning and preference optimization without introducing arbitrary or unsupported labels.

## Why a Closed-Set Taxonomy?

Large Language Models naturally tend to generate diverse terminology for similar concepts, resulting in inconsistent annotations.

Restricting predictions to predefined label sets provides several advantages:

- Standardized annotations across the dataset.
- Reduced label ambiguity.
- Easier quantitative evaluation.
- Improved compatibility with supervised fine-tuning and preference optimization.
- Reliable downstream analytics and moderation workflows.

---

## Role in the Training Pipeline

The Explainable Forensic Schema serves as the common output format throughout the entire training pipeline.

- During **Supervised Fine-Tuning (SFT)**, the model learns to generate structured forensic analyses together with standardized labels.
- During **Direct Preference Optimization (DPO)**, preference pairs are constructed using responses following the same schema, allowing the model to improve the quality of its reasoning while preserving a consistent output structure.

Using a unified schema across both training stages ensures that improvements in reasoning directly translate into more accurate, transparent, and auditable predictions.

---

## Benefits

Compared to conventional binary classification, the proposed forensic schema provides:

- Explainable multimodal reasoning.
- Transparent and auditable moderation decisions.
- Structured outputs for human review.
- Improved consistency through closed-set annotations.
- Enhanced interpretability for subtle and implicit cyberbullying cases.

By requiring the model to explicitly justify every prediction, the framework shifts cyberbullying detection from **black-box classification** to **forensic understanding**, making its decisions more reliable for real-world content moderation.

# Multimodal Cyberbullying Detection using LVLMs

**B.Tech Project Report — IIT Bhubaneswar (Department of Computer Science and Engineering)**
**Author:** Debargha Nath (22CS01070)
**Supervisor:** Dr. Padmalochan Bera
**Date:** 5 May 2026

## Overview

This project develops an explainable multimodal cyberbullying detection framework for **implicit and non-compositional harmful memes**. Unlike traditional moderation systems that analyze text and images separately, the proposed system detects harm that emerges only from the interaction between visual cues, textual cues, emojis, sarcasm, and cultural context.

The framework is built on **InternVL2.5-8B**, adapted through a two-stage transfer learning pipeline:

1. **Supervised Fine-Tuning (SFT)** with LoRA for domain adaptation.
2. **Direct Preference Optimization (DPO)** for normative alignment and boundary calibration.

The final system achieves **80.60% test accuracy** for binary bullying classification while also producing structured forensic explanations.

---

## Problem Addressed

Modern online harassment often appears as memes where both the image and caption are individually benign, but their combination conveys harmful intent. Traditional unimodal or late-fusion systems fail because they cannot capture this **intersemiotic gap**.

The project formalizes the target problem as:

```text
f(I) = 0, f(T) = 0, but f(I, T) = 1
```

Where:

* `I` = image
* `T` = text (including emojis)
* `f(I, T)` = harmful intent emerging from multimodal interaction

---

## Key Contributions

* Introduces a framework focused on **implicit multimodal cyberbullying** rather than explicit toxicity.
* Bridges the **intersemiotic gap** between image and text.
* Creates a specialized dataset of high-complexity implicit memes.
* Proposes a **Forensic XAI schema** that generates auditable reasoning chains.
* Uses **DPO** to calibrate decision boundaries between benign satire and harmful intent.
* Achieves **80.60% binary classification accuracy** on the final test set.

---

## Forensic XAI Schema

Instead of returning only a harmful/not harmful label, the model outputs a structured **Multi-modal Evidence Chain**.

### Analysis Module

* `visual_cues`: important visual evidence
* `text_cues`: important textual evidence
* `interpretation`: reasoning bridge explaining how the modalities interact
* `confidence`: confidence in the reasoning

### Labeling Module

* `bullying`: Yes / No
* `target`: individual, demographic group, organization, etc.
* `mechanism`: sarcastic dissonance, coded symbolism, metaphorical dehumanization, etc.
* `type`: identity-based, gender-based, physical appearance, religious-political, etc.
* `severity`: none, low, medium, high

This converts moderation from a black-box prediction into a verifiable audit trail.

---

## Dataset

### SFT Dataset

* **1,003** high-complexity multimodal meme samples.
* Includes both harmful and benign examples to reduce censorship bias.
* Covers multiple severity levels: none, low, medium, high.
* Includes diverse targets: individuals, demographic groups, organizations, and occupational groups.
* Emphasizes subtle mechanisms such as:

  * sarcastic dissonance
  * coded symbolism
  * metaphorical dehumanization
  * violent juxtaposition
  * punching down

### DPO Dataset

* **210** curated multimodal samples.
* Uses contrastive triplets:

  * prompt
  * preferred response (`y_w`)
  * rejected response (`y_l`)
* Designed to correct false negatives, over-refusal, and literal interpretations.

---

## Model Architecture

### Backbone: InternVL2.5-8B

Selected because it showed the best **reasoning resilience** on high-complexity implicit memes.

### Core Components

* **Vision Encoder (ViT-6B):** extracts fine-grained visual features.
* **Dynamic high-resolution tiling:** preserves OCR text and micro-cues.
* **MLP Projector:** maps visual tokens into the language embedding space.
* **LLM Backbone:** performs unified multimodal reasoning.

### Baseline Model Selection

On a 60-sample zero-shot benchmark, InternVL2.5-8B achieved the best Chain-of-Thought reasoning accuracy (**56.67%**), outperforming other tested LVLMs for implicit harm analysis.

---

## Training Pipeline

### Stage I: Supervised Fine-Tuning (SFT)

**Objective:** Teach the model the forensic schema and structured reasoning format.

**Method:** LoRA-based parameter-efficient fine-tuning.

**Key Configuration:**

* Backbone: InternVL2.5-8B
* LoRA rank: 32
* LoRA alpha: 32
* Target modules: all linear layers
* Learning rate: 2e-5
* Epochs: 5
* Effective batch size: 16
* Max sequence length: 4096
* Precision: BFloat16

**Outcome:** The model learned to generate structured forensic explanations, but still struggled with borderline cases.

### Stage II: Direct Preference Optimization (DPO)

**Objective:** Calibrate normative judgment and resolve ambiguity.

**Why DPO:**

* Avoids training a separate reward model.
* More stable than RLHF/PPO for large vision-language models.
* Optimizes preferred reasoning over rejected reasoning.

**DPO Objective:**

```math
\mathcal{L}_{\mathrm{DPO}}(\pi_\theta;\pi_{\mathrm{ref}})
=
-\mathbb{E}_{(x,y_w,y_l)\sim\mathcal{D}}
\left[
\log \sigma
\left(
\beta
\left(
\log\frac{\pi_\theta(y_w|x)}{\pi_{\mathrm{ref}}(y_w|x)}
-
\log\frac{\pi_\theta(y_l|x)}{\pi_{\mathrm{ref}}(y_l|x)}
\right)
\right)
\right].
```
**Key Configuration:**

* Reference model: merged SFT checkpoint
* DPO beta: 0.1
* Learning rate: 5e-7
* Epochs: 5
* Effective batch size: 4
* Max sequence length: 4096
* Precision: BFloat16

DPO specifically targeted the **Hallucination of Harmlessness**, where the SFT model preferred literal benign interpretations over subtle harmful intent.

---

## Experimental Setup

* **Hardware:** NVIDIA A100 (80GB VRAM)
* **Framework:** PyTorch 2.2.0 + CUDA 12.1
* **Training Framework:** MS-Swift
* **Memory Optimization:** Gradient checkpointing
* **Precision:** BFloat16

---

## Results

### Final Binary Classification Performance (201 test samples)

| Metric       | Value      |
| ------------ | ---------- |
| **Accuracy** | **80.60%** |
| Macro F1     | 0.79       |
| Weighted F1  | 0.81       |

### Class-wise Performance

| Class             | Precision | Recall | F1-score | Support |
| ----------------- | --------- | ------ | -------- | ------- |
| Bullying (Yes)    | 0.86      | 0.84   | 0.85     | 133     |
| Non-Bullying (No) | 0.70      | 0.74   | 0.72     | 68      |

### Confusion Matrix Summary

* True Positives: 112
* True Negatives: 50
* False Positives: 18
* False Negatives: 21

### Key Findings

* High recall for bullying cases indicates strong sensitivity to implicit harm.
* DPO improved detection of sarcastic dissonance and coded symbolism.
* The model became more cautious, leading to some false positives on edgy humor.
* Remaining false negatives were mostly culturally specific or highly subtle cases.

---

## What Improved After DPO

* Better distinction between benign satire and implicit harassment.
* Reduced literalism in multimodal reasoning.
* Improved handling of emojis as semantic anchors.
* Stronger detection of sarcastic dissonance.
* More reliable severity grading and target identification.

---

## Qualitative Capabilities

The final model can:

* Link visual regions to textual phrases.
* Explain how a caption weaponizes an image.
* Identify the rhetorical mechanism of harm.
* Classify the target and bullying typology.
* Assign a severity level.
* Produce an auditable reasoning chain for moderators.

This represents a shift from binary trigger detection to **forensic understanding**.

---

## Limitations

* Scarcity of high-quality implicit multimodal harm data.
* Difficulty teaching cultural and meme-specific literacy.
* Computational cost of training large LVLMs.
* Rapid evolution of meme culture and coded symbolism.
* Remaining challenges with sarcasm and niche community references.
* Potential over-reliance on textual anchors.

---

## Future Work

* Online learning and real-time adaptation.
* Expansion of the forensic taxonomy.
* Lightweight deployment for decentralized moderation.
* Human-in-the-loop refinement.
* Continuous updating for evolving meme culture and emoji semantics.

---

## Conclusion

This project demonstrates that detecting modern cyberbullying requires more than identifying offensive words or images. The proposed **InternVL2.5 + SFT + DPO** framework successfully models the interaction between modalities, produces explainable forensic reasoning, and achieves **80.60% test accuracy** on implicit multimodal bullying detection.

The primary contribution is not only improved classification performance, but the creation of a transparent moderation framework that provides **target, mechanism, typology, severity, and reasoning evidence** for every decision.

---


# Backbone Specification: InternVL2.5-8B

## Overview

The proposed framework is built upon **InternVL2.5-8B**, a Large Vision-Language Model (LVLM) designed for unified multimodal reasoning. It combines a high-capacity Vision Transformer with a powerful Large Language Model, enabling end-to-end understanding of both visual and textual information.

InternVL2.5-8B serves as the backbone throughout both stages of the training pipeline:

- **Stage I:** Supervised Fine-Tuning (SFT)
- **Stage II:** Direct Preference Optimization (DPO)

---
## Why InternVL2.5-8B?

Several open-source Large Vision-Language Models (LVLMs) were evaluated on a custom benchmark consisting of **60 high-complexity multimodal memes** containing implicit cyberbullying.

Unlike conventional toxicity detection, these memes require joint reasoning over:

- Visual context
- Embedded text (OCR)
- Sarcasm
- Emojis
- Cultural references
- Symbolic imagery

To assess the reasoning capabilities of each model, three zero-shot prompting strategies were designed:

| Prompt | Description |
|--------|-------------|
| **Prompt A (Binary Classification)** | Direct zero-shot instruction requesting only a **"Harmful / Not Harmful"** prediction, evaluating surface-level recognition without explicit reasoning. |
| **Prompt B (System Persona Framing)** | Assigns the model a professional role (e.g., *"You are an expert Cyberbullying Detector"*) to evaluate whether task-specific role conditioning improves decision quality. |
| **Prompt C (Chain-of-Thought)** | Instructs the model to **"Think step-by-step"**, encouraging explicit multimodal reasoning by jointly analyzing visual evidence, textual cues, and contextual information before producing a prediction. |

### Zero-Shot Baseline Performance

| Model | Prompt A | Prompt B | Prompt C |
|-------|---------:|---------:|---------:|
| LLaVA-1.5-7B | 70.00 | 5.00 | 8.33 |
| Qwen2-VL-7B | 16.67 | 45.00 | 25.00 |
| Qwen2.5-VL-7B | 16.67 | 10.00 | 11.67 |
| **InternVL2.5-8B** | 31.67 | 25.00 | **56.67** |

<p align="center">
  <em>Table 1. Zero-shot baseline accuracy (%) on 60 high-complexity multimodal memes.</em>
</p>

Although **LLaVA-1.5-7B** achieved the highest accuracy under **Prompt A**, its performance deteriorated substantially under alternative prompting strategies, indicating limited robustness to changes in prompting style. Similarly, **Qwen2-VL-7B** performed best only under **Prompt B**, while **Qwen2.5-VL-7B** consistently underperformed across all prompts.

In contrast, **InternVL2.5-8B** achieved the highest performance (**56.67%**) under **Prompt C**, demonstrating superior reasoning capability when encouraged to perform explicit **Chain-of-Thought (CoT)** reasoning. Rather than relying solely on lexical cues, the model effectively integrated visual semantics, embedded text, and contextual information to infer implicit harmful intent.

Since the proposed framework emphasizes **forensic multimodal reasoning** over simple binary classification, **InternVL2.5-8B** was selected as the backbone model for subsequent **Supervised Fine-Tuning (SFT)** and **Direct Preference Optimization (DPO)**.

---

## Architecture Overview

InternVL2.5-8B consists of three primary components:

```text
               Image
                  │
                  ▼
      Vision Transformer (ViT-6B)
                  │
                  ▼
          Visual Feature Tokens
                  │
                  ▼
        Cross-Modal MLP Projector
                  │
                  ▼
     Unified Multimodal Embeddings
                  │
                  ▼
        Large Language Model (LLM)
                  │
                  ▼
      Structured Forensic Reasoning
```

---

## Vision Encoder

The visual encoder is based on a **ViT-6B (Vision Transformer)** architecture.

Its responsibilities include:

- Extracting high-level semantic visual features.
- Preserving object relationships.
- Capturing fine-grained facial expressions.
- Identifying symbolic imagery frequently used in memes.

Unlike CNN-based feature extractors, the transformer architecture enables global attention across the entire image, which is particularly important for multimodal reasoning tasks.

---

## Dynamic High-Resolution Patch Tiling

Rather than resizing an image into a single low-resolution input, InternVL2.5 dynamically divides images into multiple **448 × 448 pixel tiles**.

This strategy significantly improves the preservation of:

- OCR text
- Small visual edits
- Facial expressions
- Reaction images
- Meme templates
- Background details

These subtle visual cues often determine whether otherwise harmless content becomes cyberbullying when combined with text.

---

## Cross-Modal MLP Projector

The extracted visual features are projected into the language embedding space using a lightweight **Multi-Layer Perceptron (MLP) Projector**.

This component aligns visual tokens with textual embeddings, enabling the language model to reason jointly over both modalities.

The projector serves as the semantic bridge between vision and language.

---

## Unified Multimodal Reasoning

After projection, visual and textual representations share a common latent space.

This early fusion allows the language model to learn relationships such as:

- sarcasm expressed through images,
- emoji semantics,
- contextual irony,
- symbolic visual references,
- contradictions between image and caption.

Joint reasoning is particularly important for addressing the **intersemiotic gap**, where neither the image nor the text is harmful individually, but their interaction conveys abusive intent.

---

## Advantages for Cyberbullying Detection

InternVL2.5-8B provides several properties that make it well suited for forensic multimodal cyberbullying detection:

- Strong zero-shot reasoning capability.
- High-resolution visual understanding.
- Robust OCR preservation through dynamic tiling.
- Effective cross-modal semantic alignment.
- Long-context reasoning (up to 4096 tokens during training).
- Compatibility with parameter-efficient fine-tuning techniques such as LoRA.

These characteristics make the model particularly effective for detecting subtle, implicit, and context-dependent cyberbullying.

---

## Role in the Proposed Framework

Within the proposed pipeline, InternVL2.5-8B acts as the pretrained foundation model.

The model is subsequently adapted using:

1. **LoRA-based Supervised Fine-Tuning (SFT)** to learn the forensic explanation schema.

2. **Direct Preference Optimization (DPO)** to refine decision boundaries and improve reasoning quality on ambiguous multimodal cases.

The final aligned model produces both structured cyberbullying classifications and explainable forensic reasoning chains.

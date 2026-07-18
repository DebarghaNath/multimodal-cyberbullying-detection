# Backbone Specification: InternVL2.5-8B

## Core Selection Rationale
InternVL2.5-8B serves as the foundational backbone for this architecture, selected for its high "Reasoning Resilience" under Chain-of-Thought (CoT) prompting. The model established a strong zero-shot/baseline accuracy of 56.67% on the custom multimodal dataset prior to domain adaptation.

## Visual Encoding & Dynamic Patch Tiling
* **Vision Transformer:** ViT-6B.
* **Tiling Strategy:** Images are dynamically segmented into 448x448 pixel blocks. 
* **Purpose:** This resolution preserves the structural integrity of Optical Character Recognition (OCR) and fine-grained visual artifacts (e.g., subtle facial modifications in memes) critical for detecting cyberbullying.

## Unified Latent Space
A Cross-Modal MLP Projector acts as the semantic bridge. It handles the early fusion of visual features and tokenized text prompts, aligning them within a unified latent space. This prevents the "intersemiotic gap" where benign text is weaponized by visual context (or vice versa).

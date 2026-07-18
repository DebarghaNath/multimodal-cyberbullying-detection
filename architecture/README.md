# Architecture: Multimodal Cyberbullying Detection

## Overview
This directory documents the architectural specifications and engineering decisions for the "Multimodal Cyberbullying Detection" framework. The system utilizes a dual-stage transfer learning pipeline to map visual features and textual tokens into a unified latent space, followed by alignment to normative sociolinguistic boundaries.

## Directory Index
* **`01_backbone_specification.md`**: Foundational model selection (InternVL2.5-8B) and cross-modal projection.
* **`02_explainable_forensic_schema.md`**: Structural guidelines for the JSON-based causal auditability and closed-set taxonomy.
* **`03_stage_1_sft_lora.md`**: Supervised Fine-Tuning methodology using Low-Rank Adaptation (LoRA).
* **`04_stage_2_dpo_alignment.md`**: Direct Preference Optimization (DPO) logic for normative boundary calibration.
* **`05_hardware_and_constraints.md`**: Compute scaling limits, VRAM management, and environment setup.

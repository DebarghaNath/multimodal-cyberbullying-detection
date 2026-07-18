# Trained Model Adapters

This directory provides access to the trained **LoRA adapters** developed for the proposed Multimodal Cyberbullying Detection framework.

The complete **InternVL2.5-8B** model is **not included** in this repository due to its size and licensing restrictions. Instead, only the lightweight LoRA adapters are distributed through Hugging Face.

---

# Available Adapters
**🤗 Hugging Face Repository:**  
https://huggingface.co/debarghaNath/BullyingMemeDetector

| Stage | Description | Adapter Directory |
|------|-------------|-------------------|
| **Stage I — Supervised Fine-Tuning (SFT)** | Learns the forensic reasoning schema and structured multimodal annotations. | `1000_32_best_model` |
| **Stage II — Direct Preference Optimization (DPO)** | Aligns the model with expert preferences, improving reasoning on implicit cyberbullying and reducing boundary ambiguity. | `DPO_v2_best_model` |
---

# Base Model

Both adapters require the official **InternVL2.5-8B** checkpoint.

🤗 **Base Model:** https://huggingface.co/OpenGVLab/InternVL2_5-8B

---

# Adapter Workflow

```text
InternVL2.5-8B
        │
        ▼
 Stage I: SFT Adapter
        │
        ▼
Merged SFT Checkpoint
        │
        ▼
 Stage II: DPO Adapter
        │
        ▼
 Final Aligned Model
```

---

# Notes

- Only **LoRA adapter weights** are distributed.
- The adapters were trained using **MS-Swift**.
- Users must download the official InternVL2.5-8B model separately before loading either adapter.
- The DPO adapter is built upon the merged SFT checkpoint and represents the final model used for evaluation in this project.

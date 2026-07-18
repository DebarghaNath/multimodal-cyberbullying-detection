# Notebooks

This directory contains the complete training and evaluation pipeline for the proposed **Multimodal Cyberbullying Detection** framework based on **InternVL2.5-8B**, **LoRA**, and **Direct Preference Optimization (DPO)**.

The notebooks are designed to be executed sequentially, following the two-stage training pipeline described in the project report.

---

# Notebook Structure

| Notebook | Description |
|----------|-------------|
| **01_SFT_Training_InternVL2.5.ipynb** | Performs **Stage I: Supervised Fine-Tuning (SFT)** of InternVL2.5-8B using LoRA adapters on the curated multimodal cyberbullying dataset. |
| **02_DPO_Alignment.ipynb** | Performs **Stage II: Direct Preference Optimization (DPO)** to align the model using preference pairs (Chosen vs. Rejected responses). |
| **03_Inference_Evaluation.ipynb** | Loads the final aligned model, performs inference on unseen multimodal memes, and evaluates the model using standard classification metrics. |

---

# 01_SFT_Training_InternVL2.5.ipynb

## Purpose

Specializes the pretrained **InternVL2.5-8B** model for multimodal cyberbullying detection using **LoRA-based Supervised Fine-Tuning (SFT)**.

## Workflow

- Install required dependencies
- Download the InternVL2.5-8B backbone
- Prepare the SFT dataset
- Convert JSON annotations to JSONL format
- Create train/validation splits
- Configure LoRA parameters
- Perform Supervised Fine-Tuning
- Save LoRA adapter checkpoints
- Upload the trained LoRA adapters to Hugging Face

## Output

- Fine-tuned LoRA adapters
- SFT model ready for preference alignment (DPO)

---

# 02_DPO_Alignment.ipynb

## Purpose

Performs **Direct Preference Optimization (DPO)** to refine the decision boundaries learned during SFT by optimizing preference pairs consisting of preferred and rejected responses.

## Workflow

- Download the InternVL2.5-8B backbone
- Load the SFT LoRA adapters
- Merge the backbone with the SFT adapters
- Load the DPO preference dataset
- Configure DPO training
- Fine-tune using Chosen vs. Rejected responses
- Save DPO LoRA adapter checkpoints
- Upload the trained DPO adapters to Hugging Face

## Objective

The DPO stage improves the model's ability to distinguish between subtle harmful and benign multimodal content by reducing:

- Hallucination of Harmlessness
- Literal interpretations of multimodal inputs
- False negatives on implicit cyberbullying
- Ambiguity between satire and targeted harassment

## Output

- Preference-aligned DPO LoRA adapters
- Final model ready for inference

---

# 03_Inference_Evaluation.ipynb

## Purpose

Loads the final aligned model and evaluates its performance on unseen multimodal cyberbullying samples.

## Workflow

- Download the InternVL2.5-8B backbone
- Load the SFT LoRA adapters
- Load the DPO LoRA adapters
- merge the InternVL2.5-8B backbone with SFT adapters
- merge the InternVL2.5-8B backbone with DPO adapters
- Load the evaluation dataset
- Perform multimodal inference
- Generate structured forensic reasoning
- Predict bullying labels
- Compute evaluation metrics
- Generate prediction outputs

## Evaluation Metrics

The notebook reports:

- Accuracy
- Precision
- Recall
- F1-score
- Confusion Matrix
- Classification Report

It also demonstrates inference on individual meme samples with structured reasoning outputs.

---

# Training Pipeline

```text
                 Pretrained InternVL2.5-8B
                           │
                           ▼
         Stage I: Supervised Fine-Tuning (LoRA)
                           │
                           ▼
                  SFT LoRA Adapters
                           │
                           ▼
      Stage II: Direct Preference Optimization
                           │
                           ▼
                  DPO LoRA Adapters
                           │
                           ▼
      Load Base Model + SFT Adapter + DPO Adapter
                           │
                           ▼
               Inference & Performance Evaluation
```

---

# Requirements

The notebooks were developed using:

- Python 3.12
- PyTorch
- Transformers
- PEFT
- MS-Swift
- Hugging Face Hub
- Hugging Face Datasets
- Accelerate
- scikit-learn
- Pillow
- CUDA-enabled GPU (NVIDIA A100 80GB recommended)

---

# Notes

- The notebooks were developed and executed on **Google Colab**.
- Intermediate and final LoRA adapters are stored on **Hugging Face Hub**.
- The pretrained **InternVL2.5-8B** backbone is downloaded automatically during training and inference.
- Due to storage limitations, the datasets and trained model weights are **not included** in this repository.
- Update the dataset paths, Hugging Face repository names, and authentication tokens before executing the notebooks.
- Execute the notebooks sequentially: **SFT → DPO → Inference & Evaluation**.

# Stage 1: Supervised Fine-Tuning (SFT)

## Structural Domain Adaptation
The first stage of the transfer learning pipeline focuses on adapting the base model to the specific forensic schema without losing its general world knowledge.

## LoRA Configuration
* **Method:** Low-Rank Adaptation (LoRA).
* **Rank:** r=32.
* **Target Modules:** Full-Linear Tuning (applied across all linear layers).
* **Weight Management:** The main 8B parameter weight base is strictly frozen. This prevents catastrophic forgetting, ensuring the model retains the cultural context necessary to parse complex visual cues (like memes).

## Known Limitations of SFT
While SFT enforces the JSON structure, Maximum Likelihood Estimation (MLE) often results in token-mimicry rather than true safety alignment. SFT alone leaves the model vulnerable to literalism, leading to false negatives on sarcastic or highly contextual boundary cases. This necessitates Stage 2 (DPO).

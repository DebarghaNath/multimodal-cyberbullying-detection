# Stage 2: Direct Preference Optimization (DPO)

## Normative Preference Alignment
To resolve the limitations of MLE and statistical literalism, Stage 2 implements DPO to shift the model's decision boundary toward high-acuity sociolinguistic reasoning. This stage is responsible for elevating the final research accuracy to 80.60%.

## Hyperparameters & Serialization
* **Optimization Algorithm:** Direct Preference Optimization.
* **Beta Parameter:** β = 0.1 (controlling the strength of the KL divergence penalty).
* **Data Structure:** Contrastive triplet serialization format:
  * `messages` (Context/Prompt)
  * `chosen_response` (The aligned, sociolinguistically accurate JSON)
  * `rejected_response` (The literal, flawed, or unaligned JSON)

## Algorithmic Rationale
By contrasting chosen and rejected responses directly against a static reference checkpoint, DPO avoids the instability of training separate RLHF actor-critic reward models. It mathematically adjusts the policy model to penalize literalism and reward nuanced boundary detection.

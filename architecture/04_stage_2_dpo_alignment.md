# Stage 2: Direct Preference Optimization (DPO)

## Normative Preference Alignment
To resolve the limitations of MLE and statistical literalism, Stage 2 implements DPO to shift the model's decision boundary toward high-acuity sociolinguistic reasoning. This stage is responsible for elevating the final research accuracy to 80.60%.

## DPO Configuration & Hyperparameters
The optimization environment is configured as follows:
* **Optimization Algorithm:** Direct Preference Optimization (`rlhf_type: dpo`).
* **Beta Parameter:** $\beta = 0.1$ (controlling the strength of the KL divergence penalty).
* **Model Base:** The merged checkpoint from the SFT stage (`v2_last_meme_model_merged`).
* **Precision & Memory:** `bf16` precision with gradient checkpointing enabled to optimize the dual-model VRAM footprint.
* **Optimization Parameters:**
  * **Epochs:** 5
  * **Learning Rate:** $5 \times 10^{-7}$
  * **Batch Scaling:** Per-device batch size of 1 with 4 gradient accumulation steps.
  * **Max Sequence Length:** 4096 tokens.
* **Data Structure:** Contrastive triplet serialization format utilized in `fixed_swift_data_v3.jsonl` (with a 0.1 validation split):
  * `messages` (Context/Prompt)
  * `chosen_response` (The aligned, sociolinguistically accurate JSON)
  * `rejected_response` (The literal, flawed, or unaligned JSON)

## Algorithmic Rationale
By contrasting chosen and rejected responses directly against a static reference checkpoint, DPO avoids the instability of training separate RLHF actor-critic reward models. It mathematically adjusts the policy model to penalize literalism and reward nuanced boundary detection.

## Execution Script
The preference optimization process is executed utilizing the `swift rlhf` framework with the following exact configuration:

```bash
swift rlhf \
    --rlhf_type dpo \
    --model /content/v2_last_meme_model_merged \
    --model_type internvl_chat \
    --tuner_type lora \
    --dataset fixed_swift_data_v3.jsonl \
    --split_dataset_ratio 0.1 \
    --beta 0.1 \
    --learning_rate 5e-7 \
    --num_train_epochs 5 \
    --per_device_train_batch_size 1 \
    --gradient_accumulation_steps 4 \
    --max_length 4096 \
    --bf16 true \
    --gradient_checkpointing true \
    --evaluation_strategy steps \
    --eval_steps 50 \
    --save_strategy steps \
    --save_steps 50 \
    --save_total_limit 2 \
    --load_best_model_at_end true \
    --output_dir output_DPO_MemeExpert_V2 \
    --overwrite_output_dir true

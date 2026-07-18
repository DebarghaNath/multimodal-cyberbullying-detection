# Hardware and Compute Constraints

## Environment Setup
* **Target Hardware:** NVIDIA A100 GPU.
* **VRAM Capacity:** 80GB.
* **Precision:** `bfloat16` (BF16) mixed precision.
* **Memory Optimization:** Gradient checkpointing enabled to manage activation memory.

## Dual-Model Residency Bottlenecks
During Stage 2 (DPO), the A100 faces strict VRAM ceilings. DPO requires hosting both the active policy model and the frozen reference model in memory simultaneously. 

## Impact on Scaling
* The dual-model residency capped the allowable LoRA scaling matrix sizes (restricted to rank-32).
* Future iterations seeking to scale up the LoRA rank or the base model size will require advanced memory management techniques, such as Fully Sharded Data Parallel (FSDP) across multiple GPUs or quantization-aware DPO tuning loops.

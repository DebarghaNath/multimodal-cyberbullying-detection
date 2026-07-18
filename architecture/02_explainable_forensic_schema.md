# Explainable Forensic Schema

## The Shift to Causal Auditability
Opaque binary classification (0 = benign, 1 = cyberbullying) is insufficient for complex multimodal moderation. This architecture strictly enforces an Explainable Forensic Schema that outputs structured JSON, ensuring complete causal auditability.

## The Analysis Module
Before arriving at a final classification, the model is forced to construct a structural reasoning bridge:
1. **`visual_cues`**: Explicit identification of isolated image elements.
2. **`text_cues`**: Extraction of embedded text (OCR) or user captions.
3. **`interpretation`**: Synthesizing how the visual and textual elements interact to create meaning or harm.

## The Labeling Module (Closed-Set Taxonomy)
To prevent hallucinations and standardize outputs, downstream execution is restricted to a closed-set sociolinguistic taxonomy:
* **Target:** (e.g., Individual, Group, Concept)
* **Mechanism:** (e.g., Mockery, Threat, Exclusion)
* **Typology:** (e.g., Body-shaming, Racism, General Harassment)
* **Severity:** (e.g., Low, Medium, High)

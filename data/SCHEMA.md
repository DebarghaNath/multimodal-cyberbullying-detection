# Dataset Schema

This document describes the JSON schemas used by the datasets in this repository.

The project employs two datasets corresponding to the two-stage training pipeline:

- **SFT Dataset** — Used for **Supervised Fine-Tuning (SFT)**.
- **DPO Dataset** — Used for **Direct Preference Optimization (DPO)**.

Both datasets follow a unified forensic annotation framework designed for explainable multimodal cyberbullying detection.

---

# SFT Dataset Schema

The SFT dataset is used to teach the model how to perform structured multimodal bullying analysis. Each sample consists of an image, a user prompt, and an expert-annotated assistant response.

## Sample Structure

```json
{
  "id": "meme_749",
  "system": "...",
  "images": [
    "images/image749.jpg"
  ],
  "conversations": [
    {
      "from": "user",
      "value": "<image>\nPerform a multimodal bullying analysis on this content."
    },
    {
      "from": "assistant",
      "value": {
        "analysis": {
          "visual_cues": "...",
          "text_cues": "...",
          "text_location": "embedded_in_image",
          "interpretation": "...",
          "confidence": "high"
        },
        "labels": {
          "bullying": "Yes",
          "target": "individual",
          "mechanism": "coded_symbolism",
          "type": "relational_social",
          "severity": "medium"
        }
      }
    }
  ]
}
```

---

## Top-Level Fields

| Field | Type | Description |
|------|------|-------------|
| `id` | string | Unique meme identifier |
| `system` | string | System prompt defining the annotation task and allowed label space |
| `images` | list[string] | Path(s) to the associated meme image(s) |
| `conversations` | list | User-assistant conversation used for supervised training |

---

## Analysis Object

| Field | Type | Description |
|------|------|-------------|
| `visual_cues` | string | Important visual evidence contributing to the decision |
| `text_cues` | string | Important textual evidence extracted from the meme |
| `text_location` | enum | Location of textual content (currently `embedded_in_image`) |
| `interpretation` | string | Multimodal reasoning connecting visual and textual evidence |
| `confidence` | enum | Confidence level (`low`, `medium`, `high`) |

---

## Label Object

| Field | Allowed Values |
|------|----------------|
| `bullying` | Yes, No |
| `target` | individual, individual(multiple), demographic_group, organization, occupational_group, none |
| `mechanism` | sarcastic_dissonance, violent_juxtaposition, normative_comparison, coded_symbolism, metaphorical_dehumanization, punching_down, other, none |
| `type` | relational_social, gender_based, identity_based, physical_appearance, cognitive_intellectual, religious_political, other, none |
| `severity` | none, low, medium, high |

---

# DPO Dataset Schema

The DPO dataset is used during Stage II to align the model's reasoning using preference optimization. Each sample contains a user prompt together with a preferred (**chosen**) response and a less preferred (**rejected**) response.

Unlike the SFT dataset, the objective is **not** to learn labels directly, but to learn which reasoning process is preferable.

## Sample Structure

```json
{
  "id": "meme_1037_dpo",
  "system": "...",
  "images": [
    "V2Images/image1037.jpg"
  ],
  "prompt": [
    {
      "role": "user",
      "content": "<image>\nPerform a multimodal bullying analysis on this content."
    }
  ],
  "chosen": {
    "role": "assistant",
    "content": "{...}"
  },
  "rejected": {
    "role": "assistant",
    "content": "{...}"
  }
}
```

---

## Top-Level Fields

| Field | Type | Description |
|------|------|-------------|
| `id` | string | Unique meme identifier |
| `system` | string | System prompt defining the annotation task |
| `images` | list[string] | Path(s) to the associated meme image(s) |
| `prompt` | list | User prompt presented to the model |
| `chosen` | object | Preferred assistant response |
| `rejected` | object | Less preferred assistant response |

---

## Response Structure

Both the **chosen** and **rejected** responses contain the same JSON structure.

```text
assistant
└── content
    ├── analysis
    │   ├── visual_cues
    │   ├── text_cues
    │   ├── text_location
    │   ├── interpretation
    │   └── confidence
    └── labels
        ├── bullying
        ├── target
        ├── mechanism
        ├── type
        └── severity
```

---

# Preference Construction

The DPO dataset was constructed using the following pipeline:

1. Train the initial **SFT model** using the expert-annotated SFT dataset.
2. Run the SFT model on the multimodal bullying dataset to generate model responses.
3. Compare the generated responses against the expert annotations.
4. Store the expert annotation as the **chosen** response.
5. Store the SFT-generated response (or a manually refined lower-quality variant) as the **rejected** response.
6. Train the model using **Direct Preference Optimization (DPO)** to prefer higher-quality forensic reasoning.

> **Note**
>
> The chosen and rejected responses may predict the **same final labels**. Preference is determined by the overall quality of the reasoning, including completeness, grounding, and adherence to the annotation guidelines, rather than by differences in classification alone.

---

# Closed Label Sets

## Bullying

```text
Yes
No
```

---

## Target

```text
individual
individual(multiple)
demographic_group
organization
occupational_group
none
```

---

## Mechanism

```text
sarcastic_dissonance
violent_juxtaposition
normative_comparison
coded_symbolism
metaphorical_dehumanization
punching_down
other
none
```

---

## Type

```text
relational_social
gender_based
identity_based
physical_appearance
cognitive_intellectual
religious_political
other
none
```

---

## Severity

```text
none
low
medium
high
```

---

# Design Philosophy

The dataset is designed to support **explainable multimodal cyberbullying detection** rather than simple binary classification.

Each annotation is **multimodal**, meaning labels are assigned jointly from the image and embedded textual content instead of considering either modality in isolation.

The annotation framework separates reasoning from prediction:

- The **analysis** object captures the forensic reasoning process through visual evidence, textual evidence, and multimodal interpretation.
- The **labels** object contains the structured categorical annotations used during supervised training, preference optimization, and evaluation.

All categorical annotations are restricted to predefined **closed label sets**, ensuring annotation consistency, reducing ambiguity, and enabling reliable supervised and preference-based learning.

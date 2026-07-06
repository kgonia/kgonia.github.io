---
title: "Z-Image Turbo — student text encoder"
date: 2025-11-01   # approximate, used for ordering — adjust if needed
description: >-
  Fine-tuned Qwen3-1.7B with a small adapter to imitate Qwen3-4B as Z-Image
  Turbo's text encoder — cutting VRAM usage while keeping quality.
---

I fine-tuned **Qwen3-1.7B** with a small adapter to imitate **Qwen3-4B** as the
text encoder for Z-Image Turbo. The idea is simple: recreate the hidden states
of Qwen3-4B with the smaller model and pass them to the DiT (Diffusion
Transformer) — cutting VRAM usage while keeping quality.

Tested in fp16:

| Metric          | Original (4B) | Student (1.7B) | Savings          |
| --------------- | ------------- | -------------- | ---------------- |
| Weight VRAM     | 20.70 GB      | 16.30 GB       | 4.40 GB (21%)    |
| Peak VRAM       | 21.35 GB      | 16.76 GB       | 4.59 GB (22%)    |
| Generation time | 3.9 s         | 3.5 s          | —                |

I haven't released a quantized version of this specific model yet, but existing
Z-Image quants already range from ~6 GB (Q3_K_S) to ~12 GB (Q8_0), so a
quantized student should be even more VRAM-efficient.

- **Model:** [SearchingMan/Z-Image-Turbo-student-adapter on Hugging Face](https://huggingface.co/SearchingMan/Z-Image-Turbo-student-adapter)
- **Write-up:** [r/StableDiffusion discussion](https://www.reddit.com/r/StableDiffusion/comments/1t71hvm/i_finetuned_qwen317b_to_imitate_original_zimage/)

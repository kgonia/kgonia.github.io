---
title: "FLUX.2-klein Text Encoder — Pruned to 5.1B"
date: 2026-06-01   # approximate, used for ordering — adjust if needed
description: >-
  A structurally pruned, distillation-recovered drop-in replacement for the
  8.2B Qwen3 text encoder of FLUX.2-klein-9B — 38% fewer parameters, under the
  12–16 GB VRAM thresholds, with matched image quality.
---

A structurally pruned drop-in replacement for the 8.2B Qwen3 text encoder of
[FLUX.2-klein-9B](https://huggingface.co/black-forest-labs/FLUX.2-klein-9B):
**8.19B → 5.10B parameters (−38%)**, recovered by hidden-state distillation
against the original encoder. The DiT and VAE are untouched — this is a text
encoder only, and it's what brings the fp8 pipeline under the 24 / 20 / 16 GB
VRAM thresholds (the original bf16 encoder needs ~26 GiB fully resident).

| | Original | **Pruned** | Pruned (fp8) |
| --- | --- | --- | --- |
| Parameters (encode path) | 7.57B | **5.10B** | 5.10B |
| Weights | 14.1 GiB | **9.5 GiB** | ~4.8 GiB |
| Encode-phase peak VRAM | 15.5 GiB | **10.6 GiB** | **6.8 GiB** |
| Embedding fidelity (masked token cos) | 1.0 | 0.9755 | 0.9750 |

## How it works

FLUX.2-klein's pipeline consumes only three intermediate hidden states of its
text encoder (layers 9/18/27 of 36) — the encoder is a feature extractor, not a
language model. That makes large parts of it removable:

- **Tail drop** — layers 28–35 are never read: removed exactly, no quality cost.
- **Layer merge** — layers 10 and 19 SLERP-merged into their neighbours.
- **FFN pruning** — activation-aware (Wanda) pruning of MLP width 12288 → 8192.
- **GQA head pruning** — whole key-value groups removed per layer, guided by a
  per-layer sensitivity probe rather than a uniform budget.
- **Export-time removals** — the `lm_head` and the final unused decoder layer.

After each stage the model is **recovery-distilled against the original encoder**
with per-tap hidden-state losses and a DiT-proxy loss through the frozen
transformer.

## On quality

Images conditioned by this encoder are *different renders* of a prompt, not
degraded ones. Over 25 prompts they score SSIM ≈ 0.59 against the original — and
for calibration, **the original encoder against itself on two different GPUs
scores SSIM 0.36**. In other words, this encoder diverges from the original
*less than the original diverges from itself across hardware*.

- **Model:** [SearchingMan/FLUX.2-klein-9B-Text-Encoder-Pruned-5.1B on Hugging Face](https://huggingface.co/SearchingMan/FLUX.2-klein-9B-Text-Encoder-Pruned-5.1B)

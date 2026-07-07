---
title: "FLUX.2-klein Text Encoder Pruned to 5.1B"
date: 2026-06-01   # approximate, used for ordering — adjust if needed
description: >-
  A structurally pruned, distillation recovered drop in replacement for the
  8.2B Qwen3 text encoder of FLUX.2-klein-9B. 38% fewer parameters, under the
  12 to 16 GB VRAM thresholds, with matched image quality.
---

A structurally pruned drop in replacement for the 8.2B Qwen3 text encoder of
[FLUX.2-klein-9B](https://huggingface.co/black-forest-labs/FLUX.2-klein-9B):
**8.19B → 5.10B parameters (38% smaller)**, recovered by hidden state
distillation against the original encoder. The DiT and VAE are untouched. This
is a text encoder only, and it's what brings the fp8 pipeline under the
24 / 20 / 16 GB VRAM thresholds (the original bf16 encoder needs ~26 GiB fully
resident).

| | Original | **Pruned** | Pruned (fp8) |
| --- | --- | --- | --- |
| Parameters (encode path) | 7.57B | **5.10B** | 5.10B |
| Weights | 14.1 GiB | **9.5 GiB** | ~4.8 GiB |
| Encode phase peak VRAM | 15.5 GiB | **10.6 GiB** | **6.8 GiB** |
| Embedding fidelity (masked token cos) | 1.0 | 0.9755 | 0.9750 |

## How it works

FLUX.2-klein's pipeline consumes only three intermediate hidden states of its
text encoder (layers 9, 18 and 27 of 36). The encoder is a feature extractor,
not a language model. That makes large parts of it removable:

- **Tail drop:** layers 28 to 35 are never read. Removed exactly, no quality cost.
- **Layer merge:** layers 10 and 19 SLERP merged into their neighbours.
- **FFN pruning:** activation aware (Wanda) pruning of MLP width 12288 → 8192.
- **GQA head pruning:** whole key value groups removed per layer, guided by a
  per layer sensitivity probe rather than a uniform budget.
- **Export time removals:** the `lm_head` and the final unused decoder layer.

After each stage the model is **recovery distilled against the original encoder**
with per tap hidden state losses and a DiT proxy loss through the frozen
transformer.

## On quality

The pruned encoder gives you a different render of the prompt, not a worse one.

To verify this I generated the same 25 prompts with both encoders and compared
the results with SSIM. The pruned encoder scores **0.59** against the original.
For scale: run the *original* encoder on two different GPUs and its own images
only agree at **0.36**, because tiny numeric differences are enough to change a
render. By that yardstick, the pruned encoder stays closer to the original than
the original stays to itself across hardware.

Base on the left, pruned on the right. The last three rows test text rendering:

![Base vs pruned comparison grid: eight prompts covering painting, illustration, photo and text rendering styles]({{ '/assets/images/projects/flux2-klein-grid.jpg' | relative_url }})

- **Model:** [SearchingMan/FLUX.2-klein-9B-Text-Encoder-Pruned-5.1B on Hugging Face](https://huggingface.co/SearchingMan/FLUX.2-klein-9B-Text-Encoder-Pruned-5.1B)

---
layout: post
title: "Giving a frozen 284B text-only MoE basic vision with a 40M connector"
date: 2026-08-16
---

The question I wanted to answer was narrow: can you bolt vision onto a large text-only mixture-of-experts model without touching the language model at all?

The answer is yes, at least for a basic version of "vision." I froze DeepSeek V4 Flash, froze a 417M-parameter MoonViT image encoder, and trained only the 40.1M-parameter connector sitting between them — on 100,000 image-text examples, for one epoch. The resulting NVFP4 checkpoint loads across four B200s under a patched SGLang and answers questions about real images.

It is not a competitive VLM. It is a working proof that the path is sound, and a fairly precise measurement of what the real version would cost.

Weights and artifacts:

- [DeepSeek-V4-Flash-Vision-NVFP4](https://huggingface.co/webbrain-one/DeepSeek-V4-Flash-Vision-NVFP4)
- [DeepSeek-V4-Flash-Vision-BF16](https://huggingface.co/webbrain-one/DeepSeek-V4-Flash-Vision-BF16) (vision tower + connector)
- [DeepSeek-V4-Flash-0731-Vision-NVFP4](https://huggingface.co/webbrain-one/DeepSeek-V4-Flash-0731-Vision-NVFP4)
- [Laguna-XS-2.1-Vision-NVFP4](https://huggingface.co/webbrain-one/Laguna-XS-2.1-Vision-NVFP4)

## The architecture

<img src="{{site.baseurl}}/assets/2026/08/frozen-vision-retrofit-architecture.png" alt="Adding vision without retraining the LLM: a frozen 417M MoonViT-3d encoder feeds 1152-d image features through a trained 40.1M connector into a frozen 284B DeepSeek V4 Flash" data-width="1080" data-height="607">

Three components, only one of which learns anything:

- **DeepSeek V4 Flash** — 284B total, 13B active MoE. Frozen.
- **MoonViT-3d** — 417M-parameter image encoder, borrowed from Kimi K2.6. Frozen.
- **The connector** — 40.1M parameters. Trained.

The forward path:

```
image → MoonViT (frozen) → 1152-dim features → 2×2 patch merge
      → MLP connector (trained) → 4096-dim embeddings → DeepSeek (frozen)
```

Images consumed up to 512 visual tokens against a 2,048-token maximum training sequence length. Neither the backbone nor the encoder received a single weight update.

## The data, and what "100K examples" actually means

I sampled 100,000 examples from [the_cauldron](https://huggingface.co/datasets/HuggingFaceM4/the_cauldron), spread across general captions and VQA, OCR-heavy images, documents and infographics, charts and plots, science questions, spatial reasoning, and website/UI screenshots.

The number that mattered turned out to be a different one. Those 100,000 examples contain only **39,619 unique images** — several subsets ask multiple questions about the same picture. So the connector saw about 40K distinct visual scenes, not 100K. That distinction explains most of what the model gets wrong.

A practical consequence: since each unique image recurs, I cached MoonViT's output for every one of them up front. The 417M encoder then never runs again during training, which is most of why the throughput numbers below are as good as they are.

## Training numbers

| | |
|---|---|
| Trainable parameters | 40,119,040 |
| Examples | 100,000 (39,619 unique images) |
| Epochs | 1 |
| Global batch size | 128 |
| Optimizer steps | 782 |
| Optimizer | AdamW, LR 1e-3 |
| Precision | BF16 |
| Hardware | 5× H200 |
| Throughput (clean portion) | ~4,938 examples/hour |
| Theoretical uninterrupted runtime | ~20.25 hours |
| Clean training pass | ~$445 |
| End-to-end project cost | ~$2,000 |

Loss over the final portion of training fell from 1.0225 to 0.8301.

The gap between those last two rows is the interesting part. The training pass itself is cheap — about $445 of GPU time. The other $1,500 went to dataset preparation, storage, model conversion, hosts that turned out to be broken, repeated loading attempts, and inference validation. Anyone extrapolating from GPU-hours alone will underestimate this kind of project by a wide margin.

## Does it actually see?

At a basic level, yes. On a street photo it picked out people, boxes and a storefront sign. On a browser screenshot it located an "Upload file" control in the sidebar. On a UI-grounding question it identified the correct form field and correctly noted which menu was open.

An earlier runtime produced a 36-token image response in 7.49 seconds — roughly 4.81 tokens/sec. Not fast, but the full chain demonstrably closes: MoonViT encodes, the connector projects into DeepSeek's embedding space, the visual embeddings measurably steer the output, and the whole thing serves from NVFP4 weights.

The failure modes are equally clear. Answers run too short. Small text gets missed. It will occasionally invent visual details. Complicated GUI grounding falls apart.

I read those failures as a data-scale problem rather than an indictment of the method. Forty thousand unique images is simply not enough coverage for reliable OCR, dense document layout, web interfaces, charts, spatial reasoning and the long tail of visual concepts — all at once.

## What the real run looks like

The next attempt should use roughly one million curated image-text pairs, with a far higher ratio of unique images and deliberate coverage of: websites and application interfaces, documents/tables/charts, OCR at multiple resolutions, grounded QA, spatial relationships, natural scenes, multi-step visual instructions, and hard negatives that penalize invented text and objects.

Straight extrapolation puts training at ~202.5 hours on the same 5× H200 setup — about 8.4 days continuous. But a credible project also needs dataset curation, high-resolution MoonViT feature generation, short LR and architecture pilots, at least one ablation, formal evaluation, and B200 deployment validation. Scaling the observed 100K cost accordingly, I estimate **$15,000–$20,000** to do it properly.

One million examples would not automatically produce something competitive with Qwen3.6-VL. Dataset quality, resolution, visual-token budget, connector architecture and training objectives all still matter. But the pilot gives me enough evidence that data scale is now the most informative variable to move next.

## Engineering notes

Three things cost me more time than expected:

**Training needed the BF16 language model even though it was frozen.** Gradients still have to flow backward through DeepSeek to reach the connector, and the FP8/NVFP4 inference kernels don't produce the required input gradients. Frozen does not mean cheap.

**DeepSeek's hash-based MoE routing expects token IDs, not just embeddings.** At image positions there are no token IDs. I kept the original IDs for text and assigned deterministic routing IDs to the visual positions.

**Serving needed real integration work** — a custom image processor, a model wrapper that injects visual embeddings while preserving routing IDs, and a small SGLang patch.

None of these were signs the approach was wrong. They were the tax on retrofitting a model that was never designed to take embeddings from outside.

## The same recipe on a smaller backbone

I ran the identical method on Laguna XS 2.1 — 33B total, 3B active, same frozen MoonViT, a 30.7M-parameter connector, the same 100,000 examples and 782 steps. Loss went 0.8452 → 0.7318.

The goal there is a version that runs on a single 96GB Blackwell GPU. The smaller backbone should also make future training and evaluation substantially cheaper than iterating on DeepSeek.

## Credit

The direct inspiration was Baseten's [GLM-5.2-Vision-NVFP4](https://huggingface.co/baseten/GLM-5.2-Vision-NVFP4), which demonstrated the practical recipe: freeze the large text-only MoE, freeze MoonViT, train only a modest PatchMerger connector between them. Their 49.5M projector maps MoonViT's 1152 dims into GLM-5.2's 6144-dim token space.

I adapted the same design to DeepSeek — 1152 in, 4608 hidden, 4096 out, 40.1M trainable — plus the hash-MoE routing ID handling and the SGLang integration.

Moonshot AI for MoonViT, DeepSeek for the backbone, Baseten for showing the retrofit could be packaged into a real deployment.

Since I posted the first results, [TechMDAI](https://x.com/TechMDAI/status/2086186864105464007) has published a Qwen-based follow-on using the same approach. That is exactly why the artifacts and integration code are public — I would rather this become a reproducible technique for upgrading strong text-only models than a one-off release.

## Why I care about this

I work on browser agents, and browser agents cannot get everything they need from the DOM. They have to see screenshots, dashboards, charts, dialogs, rich-text editors, and where controls physically sit on a page. In my own open-model testing, DeepSeek V4 Flash and Laguna XS 2.1 were the standouts in their respective size classes — and both were text-only.

Replacing them with much larger native VLMs meant giving up the language quality I picked them for. So the question became whether visual perception could be added on top instead. It can.

If you want to test the checkpoints, contribute datasets or evaluation cases, or you have opinions on the next run, I'm easy to find. The open question I keep going back and forth on: with a fixed next budget, does the money go to more data, a stronger connector, or a different vision encoder?

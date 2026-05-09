---
layout: post
title: "8 Open Vision Models for a Local AI"
date: 2026-05-23
---

For the past few months I've been working on WebBrain — an open-source browser agent, basically a free alternative to the Claude Browser Plugin that runs on whatever local LLM you point it at. To make it work, the model has to actually see what's on the screen. So we spent a few weeks running eight open vision models through the same test, and the result wasn't what the leaderboards predicted. Notes below.

## Why a vision model decides whether the agent is useful

A browser agent reads the screen, decides what to click or type, and then does it. If the model misreads a button label, the agent clicks the wrong thing. If it hallucinates an email address, it tells you it logged in as someone you've never heard of. If it confidently mistakes an error state for a focus state, the agent submits an empty form and never realizes anything went wrong.

For an agent that's supposed to handle the boring parts of your day — logins, forms, dashboards, account settings — getting these little judgments right is everything. So I made a tiny test script and pointed eight different models at the same Google sign-in screen, with the same prompt, and read what they wrote back.

## What we tested

Eight open vision models you can actually run yourself: Google's Gemma 4 in two sizes, Alibaba's Qwen 3.5 and 3.6 across four variants, NVIDIA's Nemotron Omni 30B, and Xiaomi's brand-new MiMo V2.5 (a 308-billion-parameter omni-modal flagship). Sizes ranged from 2 billion parameters up to 308 billion. VRAM requirements ranged from 3 GB to 110 GB.

## The Lessons

**Bigger is not always better.** A small, older, aggressively-compressed Qwen 3.5–9B beat a 308-billion-parameter MiMo at correctly identifying that an email field was a clickable dropdown. Some capabilities live in the vision encoder, not in the language model size. The leaderboards don't measure this.

**The most important skill is "knowing what you don't know."** Out of eight models, exactly one consistently flagged ambiguous parts of the screen instead of confidently making things up. That one is Qwen 3.6–35B-A3B. For a browser agent, this turned out to be the single most important property — an agent that knows it's uncertain can re-check; an agent that doesn't will silently act on a hallucination. We didn't expect this going in. None of the standard benchmarks measure it.

**Big models with bad quantization can be slower than small models with good quantization.** The 308B MiMo at low precision took over a minute per screenshot on my hardware. A 9B int4 model on the right inference engine took 10 seconds. For an agent that takes a screenshot after every action, that's the difference between something I'd actually use and something I'd close the tab on.

**Most "English-only" models can't help most users.** NVIDIA's Nemotron is faster and cheaper per image than the alternatives, but it can't read pages in Spanish, Turkish, French, or Chinese. For anyone running an agent on local-language sites, it's a non-starter regardless of how fast it is.

## What I'd actually use

If you have a 22 GB GPU or better, run Qwen 3.6–35B-A3B. If you have ~16 GB, the Qwen 3.6–27B dense variant. If you only have a 6–8 GB GPU, Qwen 3.5–9B-int4 is shockingly competent for its size. The nice thing about WebBrain being open and modular is that the agent and the vision model are independent — you swap them based on what your hardware can actually handle, instead of being locked into whatever a vendor decided to ship.

That last part is the whole reason I'm building this in the open in the first place. Closed agents lock you into one provider's models, one billing relationship, one privacy posture. I want a browser agent that runs on my hardware, on my data, on the model I picked — and I want everyone else to have one too.

WebBrain is on GitHub (MIT licensed), and you can try it at [webbrain.one](https://webbrain.one). If you bench a vision model worth adding to the table, open an issue.

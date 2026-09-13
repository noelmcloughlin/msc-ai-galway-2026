---
type: Reference
genre: reference
title: Vaswani et al. 2017 - Attention Is All You Need
description: The paper that introduced the Transformer - an encoder-decoder built from attention alone, with no recurrence or convolution.
resource: https://arxiv.org/abs/1706.03762
authors: [Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N. Gomez, Łukasz Kaiser, Illia Polosukhin]
year: 2017
venue: NeurIPS 2017
about:
  - "[[module-ct5146]]"
tags: [paper, transformers, example]
created: 2026-09-12T09:30:00Z
---

# Vaswani et al. 2017 - Attention Is All You Need

> [!example] Seeded note
> A paper note. Graduated into the bundle as `sources/vaswani-2017-attention-is-all-you-need` (the exhibition vault, beside this one) - a `Reference` record other concepts cite.

**Claim.** Sequence transduction does not need recurrence or convolution: stacked self-attention and position-wise feed-forward layers, plus positional encodings, do the job and parallelise far better.

**Method.** Encoder-decoder; multi-head scaled dot-product attention ([[Self-Attention]]); residual connections and layer normalisation around every sub-layer; sinusoidal positional encodings.

**Result.** State of the art on WMT 2014 English-German and English-French translation at a fraction of the training cost of the recurrent models it replaced.

**For me.** Filed under NLP (CT5146) because that is where I met it; the Deep Learning notes point at it too - which module actually introduces it first is an open question the bundle carries, see `concepts/transformer`.

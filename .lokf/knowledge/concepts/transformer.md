---
type: Explanation
id: "https://msc-ai.example/knowledge/concepts/transformer"
title: Transformer
description: An encoder-decoder sequence model built from stacked self-attention and position-wise feed-forward layers with positional encodings, and no recurrence or convolution.
genre: explanation
resource: "https://arxiv.org/abs/1706.03762"
status: draft
version: 1.0.0
generated:
  by: process:lokf-librarian
  at: "2026-09-12T15:30:00Z"
verified:
  - by: process:lokf-librarian
    at: "2026-09-14T13:00:00Z"
tags:
  - "assertion:inferred"
about:
  - "https://msc-ai.example/knowledge/modules/module-ct5145"
  - "https://msc-ai.example/knowledge/modules/module-ct5146"
references:
  - "https://msc-ai.example/knowledge/sources/vaswani-2017-attention-is-all-you-need"
dependsOn:
  - "https://msc-ai.example/knowledge/concepts/self-attention"
---

# Transformer

The Transformer is an encoder-decoder for sequence transduction in which every layer is built from multi-head self-attention and a position-wise feed-forward network, each wrapped in a residual connection and layer normalisation, with sinusoidal positional encodings supplying the order that the absence of recurrence would otherwise lose. The decoder adds masked self-attention over its own outputs and cross-attention over the encoder's. Because nothing in it is sequential, it trains in parallel over a whole sequence.

Derived on 2026-09-12 from two vault notes that disagree about where it belongs: the Deep Learning notes (CT5145) point at it, and the paper note is filed under Natural Language Processing (CT5146).

## Record profile

- **LOKF type:** `Explanation`
- **Assertion basis:** `inferred`
- **Version:** `1.0.0`
- **Status:** `draft` - sent to the curator with the open question below

## Bundle navigation

- **about:** [https://msc-ai.example/knowledge/modules/module-ct5145](../modules/module-ct5145.md)
- **about:** [https://msc-ai.example/knowledge/modules/module-ct5146](../modules/module-ct5146.md)
- **references:** [https://msc-ai.example/knowledge/sources/vaswani-2017-attention-is-all-you-need](../sources/vaswani-2017-attention-is-all-you-need.md)
- **dependsOn:** [https://msc-ai.example/knowledge/concepts/self-attention](self-attention.md)

## Curation note

Material changes should retain source evidence and pass human review before publication in a shared bundle.

## Open questions

- Which module introduces the Transformer first, CT5145 (Deep Learning) or CT5146 (Introduction to Natural Language Processing)? The University's public module pages list no syllabus detail, so the librarian could not settle it from the sources; the two `about` links record both candidates until a person who has the module descriptors decides. (process:lokf-librarian, 2026-09-12)

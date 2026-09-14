---
type: Explanation
id: "https://msc-ai.example/knowledge/concepts/self-attention"
title: Self-attention
description: Scaled dot-product attention lets every token weight every other token; the √d_k scaling keeps the softmax from saturating; multi-head attention runs several in parallel.
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
  - "assertion:source-backed"
about:
  - "https://msc-ai.example/knowledge/modules/module-ct5145"
references:
  - "https://msc-ai.example/knowledge/sources/vaswani-2017-attention-is-all-you-need"
relatedTo:
  - "https://msc-ai.example/knowledge/concepts/transformer"
---

# Self-attention

Every token's new representation is a weighted average of all tokens' value vectors, the weights being a softmax over the scaled dot products of its query with every key: `Attention(Q, K, V) = softmax(QKᵀ / √d_k) V`. The weights depend on the content, so one layer routes information differently for every input, and any two positions are one step apart. The `√d_k` factor stops the logits growing with the key dimension, which would push the softmax into a region of vanishing gradients. Multi-head attention runs several such attentions in parallel with separate projections and concatenates the results.

Derived on 2026-09-12 from the vault note `30-Knowledge/Concepts/Self-Attention.md`, whose claims match section 3.2 of the source. Filed under CT5145 because the vault files it there; the module's published page carries no syllabus detail, so that link is the student's, not the University's.

## Record profile

- **LOKF type:** `Explanation`
- **Assertion basis:** `source-backed`
- **Version:** `1.0.0`
- **Status:** `draft` - nobody has checked this yet; it is the first concept to confirm

## Bundle navigation

- **about:** [https://msc-ai.example/knowledge/modules/module-ct5145](../modules/module-ct5145.md)
- **references:** [https://msc-ai.example/knowledge/sources/vaswani-2017-attention-is-all-you-need](../sources/vaswani-2017-attention-is-all-you-need.md)
- **relatedTo:** [https://msc-ai.example/knowledge/concepts/transformer](transformer.md)

## Curation note

Material changes should retain source evidence and pass human review before publication in a shared bundle.

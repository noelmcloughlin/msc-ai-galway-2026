---
type: Explanation
genre: explanation
title: Self-Attention
description: How scaled dot-product attention lets every token weight every other token, and why the scaling by √d_k is there.
resource: https://arxiv.org/abs/1706.03762
about:
  - "[[module-ct5145]]"
tags: [transformers, deep-learning, example]
created: '2026-09-12T09:00:00Z'
---

# Self-Attention

> [!example] Seeded note
> A concept note that meets the graduation rule - it has stopped changing, other notes link to it, and it would be annoying to find wrong. It graduated into the bundle as `concepts/self-attention` on 2026-09-12. This copy stays in the workshop; the bundle record is the exhibit, and the one a person confirms.

## In one sentence

Every token's new representation is a weighted average of all tokens' *value* vectors, where the weights are a softmax over scaled dot products of its *query* with every *key*: `Attention(Q, K, V) = softmax(QKᵀ / √d_k) V`.

## Why it matters

- The weights are computed from the content, so the same layer can route information differently for every input - no recurrence needed, and every pair of positions is one step apart.
- The `√d_k` scaling keeps the logits from growing with the key dimension; without it softmax saturates and the gradients vanish.
- Multi-head attention runs several of these in parallel with separate projections and concatenates them, so different heads can attend to different kinds of relation.

Source: Vaswani et al. 2017, section 3.2 - see [[Vaswani et al. 2017 - Attention Is All You Need]].

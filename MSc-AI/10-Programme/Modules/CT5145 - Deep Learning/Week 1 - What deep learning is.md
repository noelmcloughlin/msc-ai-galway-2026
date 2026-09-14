---
title: Week 1 - What deep learning is
description: My notes from the first Deep Learning session - representation learning, why depth, what changes versus hand-crafted features.
module: CT5145
tags: [lecture, example]
created: '2026-09-12T10:00:00Z'
---

# Week 1 - What deep learning is

> [!example] Seeded note
> Written to show the pattern, not a record of a real session. A lecture note is *episodic*: it stays in the vault and never graduates. What you learn from it becomes a concept note, which can.

**Module:** CT5145 - Deep Learning
**Date:** 2026-09-12

- Deep learning = representation learning with many composed layers: each layer re-describes its input so the next has an easier job.
- The contrast is with hand-crafted features. The pipeline does not change; who designs the features does.
- Training is [[Gradient Descent]] on a loss, end to end, through every layer at once.
- Attention (see [[Self-Attention]]) is the layer type behind transformers - worth a concept note of its own.

Questions I still have: when does depth stop paying for itself? Ask in the lab.

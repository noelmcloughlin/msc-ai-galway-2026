---
type: Explanation
id: "https://msc-ai.example/knowledge/concepts/gradient-descent"
title: Gradient descent
description: Iteratively move the parameters against the gradient of the objective, θ ← θ − η∇J(θ); the learning rate η sets the step, and too large a step oscillates or diverges.
genre: explanation
resource: "https://www.deeplearningbook.org/contents/numerical.html"
status: stable
version: 1.0.0
generated:
  by: process:lokf-librarian
  at: "2026-09-12T15:30:00Z"
verified:
  - by: process:lokf-librarian
    at: "2026-09-12T15:30:00Z"
tags:
  - "assertion:source-backed"
about:
  - "https://msc-ai.example/knowledge/modules/module-ct5170"
---

# Gradient descent

Gradient descent minimises a differentiable objective `J(θ)` by repeating `θ ← θ − η ∇J(θ)`: the gradient points in the direction of steepest increase, so a small step against it decreases `J`. The learning rate `η` sets the step. Too small and progress is slow; too large and the iterate overshoots, oscillates, or diverges - on `f(θ) = (θ − 3)²` a rate of 0.25 halves the distance to the minimum each step, a rate of 1 oscillates for ever, and 1.1 diverges. On a convex objective the method converges to the global minimum; on the non-convex objectives of deep learning it finds a local minimum or a saddle region, and the stochastic and minibatch variants estimate the gradient from a subset of the data at each step.

Derived on 2026-09-12 from the vault's worked tutorial `20-Learning/Lab - Gradient descent by hand.md` and re-checked against section 4.3, *Gradient-Based Optimization*, of the source. Filed under CT5170 because the vault files it there.

## Record profile

- **LOKF type:** `Explanation`
- **Assertion basis:** `source-backed`
- **Version:** `1.0.0`
- **Status:** `stable` - checked by automation only; no person has confirmed it

## Bundle navigation

- **about:** [https://msc-ai.example/knowledge/modules/module-ct5170](../modules/module-ct5170.md)

## Curation note

Material changes should retain source evidence and pass human review before publication in a shared bundle.

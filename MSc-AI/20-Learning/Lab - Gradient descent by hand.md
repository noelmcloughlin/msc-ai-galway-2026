---
type: Tutorial
genre: tutorial
title: Lab - Gradient descent by hand
description: Minimise f(θ) = (θ − 3)² with gradient descent, step by step on paper, to see what the learning rate does.
module: CT5170
about:
  - "[[module-ct5170]]"
tags: [lab, optimisation, example]
created: '2026-09-12T11:00:00Z'
---

# Lab - Gradient descent by hand

> [!example] Seeded note
> A worked tutorial in the vault. Its settled facts live in the bundle as [[gradient-descent]]; this note is the exercise, which stays here.

**Goal:** feel the update rule `θ ← θ − η · f′(θ)` before trusting a library with it.

Take `f(θ) = (θ − 3)²`, so `f′(θ) = 2(θ − 3)`. Start at `θ = 0` with learning rate `η = 0.25`.

| step | θ | f′(θ) | θ − η·f′(θ) |
| --- | --- | --- | --- |
| 0 | 0 | −6 | 1.5 |
| 1 | 1.5 | −3 | 2.25 |
| 2 | 2.25 | −1.5 | 2.625 |
| 3 | 2.625 | −0.75 | 2.8125 |

Each step halves the distance to the minimum at 3, because with `η = 0.25` the update is `θ + 0.5·(3 − θ)`.

Now redo it with `η = 1`: the iterate jumps to 6, then back to 0 - it oscillates and never settles. With `η = 1.1` it diverges. That is the whole lesson about learning rates in one line: too small is slow, too large never arrives.

Q, K, V - three linear projections of the same input. Why divide by sqrt(d_k)? Because dot products grow with dimension, and a large logit pushes softmax into a region with tiny gradients.

Multi-head = several attention "views" in parallel, concatenated, then projected once more.

TODO turn this into a concept note. Half of it is already in [[Self-Attention]].

(Example note: a raw inbox capture - no frontmatter at all. Obsidian is fine with it; LOKF Registrar ignores it, because a note with no frontmatter is not a concept.)

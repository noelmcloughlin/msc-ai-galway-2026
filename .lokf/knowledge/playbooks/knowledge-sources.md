---
type: Playbook
id: "https://msc-ai.example/knowledge/playbooks/knowledge-sources"
title: Knowledge sources map
description: Where this bundle's concepts come from, and how to re-check each source.
genre: how-to
status: draft
version: 1.0.0
generated:
  by: process:lokf-librarian
  at: "2026-09-13T16:00:00Z"
tags:
  - "assertion:user-defined"
references:
  - "https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-public-page"
  - "https://msc-ai.example/knowledge/sources/source-snapshot-reference-ict-skillnet-page"
  - "https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-extract-2026"
---

# Knowledge sources map

This bundle's initial 50 concepts came from a hand-curated migration (`inputs/`, see `log.md`), not from a repository sweep - so this map was never written at the time. It is added retroactively on the bundle's first steady-state refresh, per the bootstrap step this run should have performed originally.

## External sources

- **University of Galway public programme page** - `source-snapshot-reference-uog-public-page`. Live URL, re-fetched directly (not summarised) on each refresh. Yields the `Programme`, `Module`, `Person`/`Role`/`Organization`, `DeliveryPattern`, `AssessmentPattern`, `LearningOutcomeSet` and `ProjectPattern` concepts, plus most of the `VerificationIssue` concepts. Re-check by fetching the page and diffing against the frontmatter of the concepts whose `resource` points at it.
- **Technology Ireland ICT Skillnet partner page** - `source-snapshot-reference-ict-skillnet-page`. Live URL. Independently states the same programme's module list with a Core/Optional split and a Year 1/Year 2 breakdown the official page doesn't give - corroborating and, in places, contradicting the official page. Re-check the same way.
- **Supplied 2026 programme extract** - `source-snapshot-reference-uog-extract-2026`. Historically a locally-supplied text file; the file itself no longer exists (only `inputs/`, kept as a placeholder, remains) so this source can no longer be independently re-fetched. Treat facts whose only `derivedFrom` is this source as unverifiable until re-derived from a live page.

## Repository sweep

The host vault (`MSc-AI/`) was re-swept on 2026-09-12. It carries a small set of seeded notes, each tagged `example`, that show the path from workshop to exhibition:

- `30-Knowledge/Concepts/Self-Attention.md` - a concept note with bundle-shaped frontmatter; graduated as `concepts/self-attention` (a draft until a person confirms it). Re-check: compare the note's claims with the record and with Vaswani et al. 2017 §3.2.
- `20-Learning/Lab - Gradient descent by hand.md` - a worked tutorial; its settled facts are `concepts/gradient-descent`. Re-check against chapter 4 of the Deep Learning book (the record's `resource`).
- `40-Research/Literature-Notes/Vaswani et al. 2017 - Attention Is All You Need.md` - a paper note; graduated as `sources/vaswani-2017-attention-is-all-you-need`. Re-check: the arXiv abstract page.
- The CT5145 lecture note and the paper note disagree about which module introduces the Transformer; `concepts/transformer` carries that as an open question rather than a guess.
- `30-Knowledge/Concepts/Gradient Descent.md`, `00-Inbox/Attention scribbles.md`, the CT5145 lecture note and `01-Dashboard/MOCs/Deep Learning.md` - workshop notes that do not meet the graduation rule (no `resource`, still changing, or episodic by nature); left in the vault on purpose.

Re-swept on 2026-09-13 after the repository README was rewritten Obsidian-first: the vault gained `02-Daily/` (one note per study day, by year, from a new `Daily note` template), `01-Dashboard/Vault.base` (five live views over the vault's properties) and Templater's folder setting, and `Home` embeds the base's Inbox view. None of these holds knowledge to derive - a daily note is a log, a base is a view - but the procedure they implement does, and it is `playbooks/obsidian-workshop.md`, derived from the README's Part 1 and `01-Dashboard/CONVENTIONS.md`. Re-check that playbook against those two files whenever either changes.

`50-Projects/`, `60-Assets/`, `99-Archive/` and the other module folders hold only placeholders. Re-walk this list on each refresh; a `30-Knowledge/Concepts/` note that has stopped changing and is linked from two or more other notes is what graduates next (see `01-Dashboard/CONVENTIONS.md`).

## Record profile

- **LOKF type:** `Playbook`
- **Assertion basis:** `user-defined`
- **Version:** `1.0.0`
- **Status:** `draft`

## Bundle navigation

- **references:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-public-page](../sources/source-snapshot-reference-uog-public-page.md)
- **references:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-ict-skillnet-page](../sources/source-snapshot-reference-ict-skillnet-page.md)
- **references:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-extract-2026](../sources/source-snapshot-reference-uog-extract-2026.md)

## Curation note

Material changes should retain source evidence and pass human review before publication in a shared bundle.

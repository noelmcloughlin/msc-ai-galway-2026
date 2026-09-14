---
type: Playbook
id: "https://msc-ai.example/knowledge/playbooks/knowledge-sources"
title: Knowledge sources map
description: Where this bundle's concepts come from, and how to re-check each source.
genre: how-to
status: draft
version: 1.1.0
generated:
  by: process:lokf-librarian
  at: "2026-09-14T12:45:00Z"
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

- **University of Galway public programme page** - `source-snapshot-reference-uog-public-page`. Live URL, re-fetched directly (not summarised) on each refresh. Yields the `Programme`, `Module`, `Person`/`Role`/`Organization`, `DeliveryPattern`, `AssessmentPattern`, `LearningOutcomeSet` and `ProjectPattern` concepts, plus most of the `VerificationIssue` concepts. Re-check by fetching the page and diffing against the frontmatter of the concepts whose `resource` points at it. Last re-fetched 2026-09-13: every module's semester and credits, the ten names, the "12 taught modules" sentence, the "Year 1 (90 Credits)" heading and the stray grammar-exam sentence are all still there.
- **Technology Ireland ICT Skillnet partner page** - `source-snapshot-reference-ict-skillnet-page`. Live URL. Independently states the same programme's module list with a Core/Optional split and a Year 1/Year 2 breakdown the official page doesn't give - corroborating and, in places, contradicting the official page. Re-check the same way.
- **Deep Learning, chapter 4 (Numerical Computation)** - `https://www.deeplearningbook.org/contents/numerical.html`, the `resource` of `concepts/gradient-descent`. A pdf2htmlEX rendering that splits words with markup, so a plain string search misses its headings; re-check with whitespace-tolerant matching for section 4.3, *Gradient-Based Optimization* (found 2026-09-13).
- **Supplied 2026 programme extract** - `source-snapshot-reference-uog-extract-2026`. Historically a locally-supplied text file; the file itself no longer exists (only `inputs/`, kept as a placeholder, remains) so this source can no longer be independently re-fetched. Treat facts whose only `derivedFrom` is this source as unverifiable until re-derived from a live page.

## Repository sweep

The host vault (`MSc-AI/`) was re-swept on 2026-09-12 and again on 2026-09-13, when nothing had changed but plugin manifests. It carries a small set of seeded notes, each tagged `example`, that show the path from workshop to exhibition:

- `30-Knowledge/Concepts/Self-Attention.md` - tagged `concept`, with a `source`; graduated as `concepts/self-attention` (a draft until a person confirms it). Re-check: compare the note's claims with the record and with Vaswani et al. 2017 §3.2.
- `20-Learning/Lab - Gradient descent by hand.md` - a worked tutorial, tagged `lab`; its settled facts are `concepts/gradient-descent`. Re-check against chapter 4 of the Deep Learning book (the record's `resource`).
- `40-Research/Literature-Notes/Vaswani et al. 2017 - Attention Is All You Need.md` - a paper note, tagged `paper`; graduated as `sources/vaswani-2017-attention-is-all-you-need`. Re-check: the arXiv abstract page.
- The CT5145 lecture note and the paper note disagree about which module introduces the Transformer; `concepts/transformer` carries that as an open question rather than a guess.
- `30-Knowledge/Concepts/Gradient Descent.md`, `00-Inbox/Attention scribbles.md`, the CT5145 lecture note and `01-Dashboard/MOCs/Deep Learning.md` - workshop notes that do not meet the graduation rule (no `source`, still changing, or episodic by nature); left in the vault on purpose.

Re-swept on 2026-09-13 after the repository README was rewritten Obsidian-first: the vault gained `02-Daily/` (one note per study day, by year, from a new `Daily note` template), `01-Dashboard/Vault.base` (five live views over the vault's properties) and Templater's folder setting, and `Home` embeds the base's Inbox view. None of these holds knowledge to derive - a daily note is a log, a base is a view - but the procedure they implement does, and it is `playbooks/obsidian-workshop.md`, derived from the README's Part 1 and `01-Dashboard/CONVENTIONS.md`. Re-check that playbook against those two files whenever either changes.

Re-swept again on 2026-09-14: the vault dropped the bundle's own vocabulary (`type`, `genre`, `about`, `resource`) from every seeded note and template. What a note is is now a tag (`lecture`, `lab`, `paper`, `concept`, `daily`, `moc`); where a claim comes from is `source:`, the property the Web Clipper already writes; `module:` is unchanged. `CONVENTIONS.md`'s Frontmatter section became a Properties section stating this in one table. The bundle's own vocabulary - `type`, `resource`, `status`, typed relations - now belongs only here; the librarian derives it when a note graduates, never the note itself. `playbooks/obsidian-workshop.md` rewritten to match; this row's re-check guidance above updated with it.

`50-Projects/`, `60-Assets/`, `99-Archive/` and the other module folders hold only placeholders. Re-walk this list on each refresh; a `30-Knowledge/Concepts/` note that has stopped changing and is linked from two or more other notes is what graduates next (see `01-Dashboard/CONVENTIONS.md`).

## The vocabulary this host validates against

`.lokf/justfile` validates with `--schema msc-ai.yaml`, so this host's vocabulary is the core
fifteen classes **plus** that schema's own (`Programme`, `Module`, `VerificationIssue`,
`SourceSnapshotReference`, `IngestionPlaybook`, ...), and a record names the subclass, never the
parent. Read `msc-ai.yaml` before choosing a class for a new record, as the librarian skill's
Rule 3 says; adding a class to it is the maintainer's decision, not a refresh's. Beside it,
`lokf.yaml` is a pinned copy of the core schema, currently 0.7.0 - matching the `lokf[build]`
floor in `.lokf/pyproject.toml` and the latest release on PyPI. Refresh that copy in the same
change as any future floor bump, or the bundle validates against a vocabulary the toolkit has
moved past.

## Consciously left out

The repository's own engineering - `README.md`, `SECURITY.md`, `AI_COVENANT.md`, `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, the issue and pull-request templates and `dependabot.yml` under `.github/`, the workflows under `.github/workflows/` and the sidecar tooling under `.lokf/` - yields no concept here. This bundle's scope is the programme and what graduates from the vault (`index.md`: entities, not episodes), and the sibling repositories' own bundles describe the skills, plugins and CI this host reuses. `.retired/` and `inputs/` are history, not sources. Revisit if the host ever grows code of its own.

## Record profile

- **LOKF type:** `Playbook`
- **Assertion basis:** `user-defined`
- **Version:** `1.1.0`
- **Status:** `draft`

## Bundle navigation

- **references:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-public-page](../sources/source-snapshot-reference-uog-public-page.md)
- **references:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-ict-skillnet-page](../sources/source-snapshot-reference-ict-skillnet-page.md)
- **references:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-extract-2026](../sources/source-snapshot-reference-uog-extract-2026.md)

## Curation note

Material changes should retain source evidence and pass human review before publication in a shared bundle.

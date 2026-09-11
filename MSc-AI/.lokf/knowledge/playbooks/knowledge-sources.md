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
  at: "2026-09-11T00:00:00Z"
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

The host vault (`MSc-AI/`) was swept for orphan content on 2026-09-11: `10-Programme/Modules/<CODE> - <Title>/`, `20-Learning/`, `30-Knowledge/Concepts/`, `40-Research/`, `50-Projects/`, `60-Assets/`, `99-Archive/` all contain only `.gitkeep` placeholders, and `00-Inbox/` holds two empty untitled notes. None yielded a concept. Re-walk this list on each refresh; a populated `30-Knowledge/Concepts/` note that has stopped changing and is linked from two or more other notes is the main thing expected to eventually graduate (see `01-Dashboard/CONVENTIONS.md`).

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

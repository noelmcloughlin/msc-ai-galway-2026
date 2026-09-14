---
type: VerificationIssue
id: "https://msc-ai.example/knowledge/issues/verification-issue-module-count"
title: Module-count contradiction
description: The source claims 12 taught modules but names 13 topics and lists 15 curriculum entries.
resource: "https://www.universityofgalway.ie/courses/taught-postgraduate-courses/online-artificial-intelligence.html"
status: draft
version: 1.1.0
generated:
  by: process:lokf-librarian
  at: "2026-09-11T00:00:00Z"
verified:
  - by: process:lokf-librarian
    at: "2026-09-14T13:00:00Z"
tags:
  - "assertion:inferred"
derivedFrom:
  - "https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-extract-2026"
  - "https://msc-ai.example/knowledge/sources/source-snapshot-reference-ict-skillnet-page"
about:
  - "https://msc-ai.example/knowledge/programme/programme-uog-1mao3"
claimed_taught_modules: 12
observed_named_topics: 13
observed_curriculum_entries: 15
observed_non_capstone_entries: 14
---

# Module-count contradiction

The source claims 12 taught modules, names 13 taught topics and exposes 15 curriculum entries. No interpretation is selected.

The same shape of contradiction was independently found in a second source on 2026-09-11: the Technology Ireland ICT Skillnet partner page states its taught-module total as "Core foundational modules (30 ECTS)" plus "Optional advanced modules (30 ECTS)" - 60 ECTS, i.e. 12 modules at 5 ECTS each - but its own itemised Year 1/Year 2 module list names 13 modules (65 ECTS). Two independently maintained pages about the same programme both undercount their own module lists by exactly one module; neither error has been reconciled.

## Record profile

- **LOKF type:** `VerificationIssue`
- **Assertion basis:** `inferred`
- **Version:** `1.1.0`
- **Status:** `draft`

## Bundle navigation

- **derivedFrom:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-extract-2026](../sources/source-snapshot-reference-uog-extract-2026.md)
- **derivedFrom:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-ict-skillnet-page](../sources/source-snapshot-reference-ict-skillnet-page.md)
- **about:** [https://msc-ai.example/knowledge/programme/programme-uog-1mao3](../programme/programme-uog-1mao3.md)

## Curation note

Material changes should retain source evidence and pass human review before publication in a shared bundle.

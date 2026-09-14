---
type: VerificationIssue
id: "https://msc-ai.example/knowledge/issues/verification-issue-core-optional-status"
title: Core and optional classification conflict
description: The official page labels every module Optional; the partner page classifies 13 of the 14 as Core or Optional.
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
  - "https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-public-page"
  - "https://msc-ai.example/knowledge/sources/source-snapshot-reference-ict-skillnet-page"
about:
  - "https://msc-ai.example/knowledge/programme/programme-uog-1mao3"
partner_core_modules:
  - CT5170
  - CT5148
  - CT5144
  - CT5136
  - CT5152
  - CT5146
partner_optional_modules:
  - ST5001
  - CT5150
  - CT5153
  - CT5145
  - PH504
  - CT5130
  - CT5188
missing_from_partner_modules:
  - CT5186
resolution: compare current registration rules and module pages
---

# Core and optional classification conflict

The official University of Galway page labels every one of the 14 taught modules "Optional" (confirmed directly against the live page on 2026-09-11: only one "Required" badge exists anywhere on the page, and it is the glossary definition of the term, not a module label). The Technology Ireland ICT Skillnet partner page, fetched the same day, instead gives 13 of the 14 taught modules an explicit Core/Optional split - 6 Core, 7 Optional - and omits the 14th (`CT5186`, "Future of Artificial Intelligence") from its module list entirely. Both claims are current and both are directly source-backed; they simply disagree, and `CT5186`'s status per the partner source cannot be determined because it is not listed there at all. The partner page's own Core/Optional totals (30 ECTS + 30 ECTS = 60 ECTS taught) also undercount its own itemised 13-module list (65 ECTS) by one module - see `verification-issue-module-count` for that finding and its UoG-side counterpart.

## Record profile

- **LOKF type:** `VerificationIssue`
- **Assertion basis:** `inferred`
- **Version:** `1.1.0`
- **Status:** `draft`

## Bundle navigation

- **derivedFrom:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-extract-2026](../sources/source-snapshot-reference-uog-extract-2026.md)
- **derivedFrom:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-public-page](../sources/source-snapshot-reference-uog-public-page.md)
- **derivedFrom:** [https://msc-ai.example/knowledge/sources/source-snapshot-reference-ict-skillnet-page](../sources/source-snapshot-reference-ict-skillnet-page.md)
- **about:** [https://msc-ai.example/knowledge/programme/programme-uog-1mao3](../programme/programme-uog-1mao3.md)

## Curation note

Material changes should retain source evidence and pass human review before publication in a shared bundle.

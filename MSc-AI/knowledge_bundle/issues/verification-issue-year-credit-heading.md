---
type: VerificationIssue
id: "https://msc-ai.example/knowledge/issues/verification-issue-year-credit-heading"
title: Year and credit heading inconsistency
description: "The source's 'Year 1 (90 Credits)' heading conflicts with the programme's stated 2-year, 90-ECTS structure."
resource: "https://www.universityofgalway.ie/courses/taught-postgraduate-courses/online-artificial-intelligence.html"
status: draft
version: 1.1.0
generated:
  by: process:lokf-librarian
  at: "2026-09-11T00:00:00Z"
tags:
  - "assertion:inferred"
derivedFrom:
  - "https://msc-ai.example/knowledge/sources/source-snapshot-reference-uog-extract-2026"
  - "https://msc-ai.example/knowledge/sources/source-snapshot-reference-ict-skillnet-page"
about:
  - "https://msc-ai.example/knowledge/programme/programme-uog-1mao3"
source_heading: Year 1 (90 Credits)
programme_duration_years: 2
programme_total_ects: 90
---

# Year and credit heading inconsistency

The extracted heading “Year 1 (90 Credits)” conflicts with the source-backed two-year, 90-ECTS programme structure and requires verification.

Corroborating detail found on 2026-09-11: the Technology Ireland ICT Skillnet partner page does not use a single "Year 1" heading at all - it spreads the same taught modules across explicit "Year 1" and "Year 2" groupings (three or four modules per semester, two semesters per year). It independently confirms the programme runs across two distinct years; it does not, by itself, say which specific modules the official page intends for which year, so `verification-issue-core-optional-status` (which records the partner page's per-module classification) is the concept to consult for that detail. This does not resolve the contradiction - the official page's own heading is still "Year 1 (90 Credits)" - but it narrows what a correction would need to say.

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

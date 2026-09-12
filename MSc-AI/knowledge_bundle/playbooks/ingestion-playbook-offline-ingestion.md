---
type: IngestionPlaybook
id: "https://msc-ai.example/knowledge/playbooks/ingestion-playbook-offline-ingestion"
title: Offline LOKF knowledge acquisition
description: How this bundle is refreshed from source material without overwriting personal learning.
status: draft
version: 0.1.0
tags:
  - "assertion:user-defined"
inputs:
  - raw source snapshots
  - future University module pages
  - current knowledge bundle
outputs:
  - proposed official-evidence notes
  - verification issues
  - change manifest
  - validation report
  - human-review queue
mode: incremental-upsert-by-stable-id
protected_layers:
  - personal learning
  - human review decisions
---

# Offline LOKF knowledge acquisition

A rerunnable, source-first acquisition activity for lokf-agent-skills. It preserves snapshots, extracts candidate claims, validates identifiers and links, records conflicts, and leaves material acceptance to human review. Official-source refreshes never overwrite personal learning prose.

## Record profile

- **LOKF type:** `IngestionPlaybook`
- **Assertion basis:** `user-defined`
- **Version:** `0.1.0`
- **Status:** `draft`

## Curation note

Material changes should retain source evidence and pass human review before publication in a shared bundle.

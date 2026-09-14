---
type: Playbook
id: "https://msc-ai.example/knowledge/playbooks/obsidian-workshop"
title: Working the workshop in Obsidian
description: How the MSc-AI vault is run day to day - capture into the inbox (web clips, PDFs, scribbles), one daily note per study day, templated lecture, lab, paper and concept notes, live Bases views, a weekly inbox sweep - and the rule by which a concept note leaves it for the exhibition.
genre: how-to
resource: README.md
status: draft
version: 1.1.0
generated:
  by: process:lokf-librarian
  at: "2026-09-14T08:00:00Z"
tags:
  - "assertion:user-defined"
sources:
  - resource: README.md
    title: Repository README, Part 1 - The workshop
  - resource: MSc-AI/01-Dashboard/CONVENTIONS.md
    title: Vault conventions
relatedTo:
  - "https://msc-ai.example/knowledge/playbooks/knowledge-sources"
---

# Working the workshop in Obsidian

The `MSc-AI/` vault is the **workshop**; this bundle is the **exhibition**. The workshop runs on Obsidian's own features - properties, links and backlinks, tags, daily notes, templates, Bases, the PDF viewer - plus Templater for the four templates with logic in them and, in the browser, the official Obsidian Web Clipper. It speaks Obsidian's own vocabulary throughout, never this bundle's: derived on 2026-09-14 from the repository README (Part 1) and `MSc-AI/01-Dashboard/CONVENTIONS.md`, after both dropped `type`, `genre`, `about` and `resource` from every vault note and template.

## The daily loop

1. Open today's daily note (the Daily notes core plugin writes `02-Daily/<year>/YYYY-MM-DD` from the `Daily note` template): what was studied, the questions, `- [ ]` tasks, and the concepts to write up.
2. Capture into `00-Inbox`. New notes land there by default; web clips arrive there from the Web Clipper carrying `title`, `source`, `author`, `published` and `created` properties; PDFs go to `60-Assets/PDFs` and are linked by page (`[[paper.pdf#page=7]]`).
3. Write the lecture note in `10-Programme/Modules/<CODE> - <Title>/` from the `Lecture note` template, wikilinking every concept met. What a note is is a tag from its template - `lecture`, `lab` for a worked tutorial, `paper` for a reading note (always with a `source`), `concept` for a synthesised note - not a bundle class.
4. Read the vault through `01-Dashboard/Vault.base` - Inbox, Lectures grouped by `module`, Papers (filtered on `#paper`, reading `source`), Concepts, Daily - live tables over the notes' properties; `Home` embeds the Inbox view.

## The weekly sweep

Empty the inbox: every note gets its tags and a `title` and moves to its lifecycle folder, or is deleted. Then ask which concept note in `30-Knowledge/Concepts` has stopped changing, is linked from two or more notes (the Backlinks pane shows this), names a `source`, and would be annoying to find wrong in six months. That note graduates: `lokf-librarian` derives a record from it into `concepts/` here, in this bundle's own vocabulary (`type`, `resource`, `status`, typed relations) - the note itself keeps Obsidian's properties, unchanged; `lokf-curator`, or the LOKF Curator plugin in this vault, confirms the record.

## What the folders mean

Folders carry lifecycle, properties carry meaning, links carry relationships. `00-Inbox` is capture; `01-Dashboard` navigation (`Home`, `CONVENTIONS`, `Vault.base`, `MOCs/`); `02-Daily` the log, which never graduates; `10-Programme/Modules` holds a module's material while it runs, then `99-Archive`; `20-Learning` is cross-module work distilled into `30-Knowledge`; `30-Knowledge/Concepts` holds the notes that graduate; `40-Research` the reading queue, literature notes and dissertation; `50-Projects` coursework and builds; `60-Assets` attachments; `90-Meta/Templates` the five templates. There are no subject folders on purpose: subjects live in `tags` and in `01-Dashboard/MOCs/`.

## Record profile

- **LOKF type:** `Playbook`
- **Assertion basis:** `user-defined`
- **Version:** `1.1.0`
- **Status:** `draft` - the intended procedure as the README states it, not yet observed over a semester

## Curation note

Material changes should retain source evidence and pass human review before publication in a shared bundle.

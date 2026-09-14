# Vault conventions

The short version: **folders carry lifecycle, properties carry meaning, links carry relationships.** If you are deciding where to put something, ask what *stage* it is at - not what it is *about*. What it is about goes in the properties, where a note can belong to many things at once.

The repository `README.md` walks the loop from a workshop note to a confirmed record.

## Where things go

| Folder | Holds | Leaves when |
| --- | --- | --- |
| `00-Inbox` | Anything uncaptured. Default location for new notes and web clips. | You file it. Aim for empty weekly. |
| `01-Dashboard` | `Home`, this file, `Vault.base` (live views over the vault's properties) and `MOCs/` - the maps you navigate by | never |
| `02-Daily/<year>` | One note per study day, from the `Daily note` template: what you studied, questions, tasks | never - a log is a log |
| `10-Programme/Modules/<CODE> - <Title>` | Per-module material: lecture notes, labs, assignments | module ends -> `99-Archive` |
| `20-Learning` | Cross-module lectures, labs, tutorials | distilled into `30-Knowledge` |
| `30-Knowledge/Concepts` | **The synthesised notes. The valuable ones.** | graduates to the bundle |
| `40-Research` | `Reading-Queue/`, `Literature-Notes/`, `Dissertation/` | - |
| `50-Projects` | Coursework and personal builds | finished -> `99-Archive` |
| `60-Assets` | PDFs, images, data, slides. Default attachment location. | - |
| `90-Meta/Templates` | Templates: Lecture, Lab, Paper and Concept notes (Templater), and the Daily note (core Templates syntax, so the Daily notes plugin fills it in) | - |
| `99-Archive` | Done, but kept for links and history | never |
| *(not here)* | **The LOKF bundle** lives beside this vault, not in it - see below. | - |

There are deliberately **no** subject folders (`Machine Learning/`, `NLP/`). `Self-Attention` belongs to Deep Learning *and* NLP *and* CT5145 *and* the dissertation. **A folder forces one choice; properties and links don't**. Subjects live in `tags:` and in `01-Dashboard/MOCs/`.

There are also **no** per-module `Lectures/ Labs/ Assignments/` subfolders. That would be 75 folders to file into by hand. A tag plus a Bases view does the same job and is queryable.

## Properties

This vault uses Obsidian's own properties - `tags`, `aliases` - plus the ones the [Web Clipper](https://obsidian.md/help/web-clipper) writes on every clip (`title`, `source`, `author`, `published`, `created`, `description`), so a clip and a note you wrote by hand answer the same queries. Nothing from the bundle's vocabulary: no `type`, `genre`, `resource` or typed relation. Those are the librarian's to write when a note graduates, and the registrar's to check, in the bundle.

| What you're writing | Tag | Also set |
| --- | --- | --- |
| A lecture's notes | `lecture` | `module` |
| A lab worked end to end | `lab` | `module` |
| A paper or reading note | `paper` | `source`, `authors`, `year`, `venue`, `module` |
| A synthesised concept note | `concept` | `description`, `source`, `module` |
| A study day | `daily` | (the template does it) |
| A map of content | `moc` | - |

Anything finer goes in `tags:` too - `#todo/ask`, `optimisation`, `transformers`. `module:` is the code (`CT5145`) and groups the Lectures view in `Vault.base`; `created:` sorts it; `source:` is the DOI or URL a claim comes from, the one property the exhibition will ask of a concept note. Link to other notes with `[[wikilinks]]`; a module or a person in the bundle is named by path (`modules/module-ct5145`), never by wikilink, since a link cannot cross vaults.

```yaml
---
title: Self-Attention
description: How scaled dot-product attention lets a token weight every other token.
source: https://arxiv.org/abs/1706.03762
module: CT5145
tags: [concept, transformers, deep-learning]
---
```

## The bundle (`knowledge_bundle`, beside this vault)

The LOKF knowledge bundle is `.lokf/knowledge/` at the repository root, one level above this vault, and `knowledge_bundle` beside it is a link onto the same folder - the name people and Obsidian open. Open *that* as a vault of its own to curate; this vault never lists it, and a wikilink cannot cross into it, so notes here name records by path (`concepts/self-attention`). Don't reorganise it by hand: the skills and CI address it as `.lokf/knowledge`. This vault is the **workshop**; that folder is the **exhibition**.

It holds **entities, not episodes**: the programme, the 15 modules, the teaching staff, the sources, the open verification issues, and concept notes that have settled. These are the stable things everything else points at. Your daily notes link *into* it.

**A note graduates into the bundle when all three hold:**

1. it has stopped changing week to week;
2. two or more other notes link to it;
3. you would be annoyed to find it wrong six months from now.

By then it has a `source` (where the claim comes from) and a `description`. Graduating means the librarian derives a record from it in the bundle's own vocabulary - `type`, `resource`, `status`, typed relations - and the curator confirms it; the note itself stays here, unchanged. Everything else stays in the vault too - that is what keeps the review queue reviewable by one person. The bundle's vocabulary and its rules are `.lokf/README.md`'s to state; validate from `.lokf/` at the repository root with `just lokf-validate` and `just lokf-check-refs`.

## Daily habit

Open today's daily note first (`02-Daily/`, the calendar icon in the ribbon). New note or web clip -> lands in `00-Inbox` -> gets its tags and a `title` -> moves to its folder; a PDF goes to `60-Assets/PDFs` and is linked by page (`[[paper.pdf#page=7]]`). Weekly, empty the inbox (`Home` embeds it) and ask which concept notes are ready to graduate. The Obsidian features this leans on, and what to use each for, are in the repository `README.md`, Part 1.

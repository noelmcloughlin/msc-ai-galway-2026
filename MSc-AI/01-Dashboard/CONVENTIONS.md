# Vault conventions

The short version: **folders carry lifecycle, frontmatter carries meaning, links carry relationships.** If you are deciding where to put something, ask what *stage* it is at - not what it is *about*. What it is about goes in the frontmatter, where a note can belong to many things at once.

Full reasoning: the repository `README.md`.

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

There are deliberately **no** subject folders (`Machine Learning/`, `NLP/`). `Self-Attention` belongs to Deep Learning *and* NLP *and* CT5145 *and* the dissertation. **A folder forces one choice; frontmatter and links don't**. Subjects live in `tags:` and in `01-Dashboard/MOCs/`.

There are also **no** per-module `Lectures/ Labs/ Assignments/` subfolders. That would be 75 folders to file into by hand. `type:` in frontmatter plus a Bases view does the same job and is queryable.

## Frontmatter

Every note gets a `type`. Use **LOKF's own classes** - the same vocabulary the bundle uses, so there is one vocabulary in this vault, not two:

| What you're writing | `type` | `genre` |
| --- | --- | --- |
| A lab worked end to end | `Tutorial` | `tutorial` |
| A synthesised concept note | `Explanation` | `explanation` |
| A paper or reading note | `Reference` | `reference` |
| A term you keep re-looking-up | `GlossaryTerm` | `reference` |
| A procedure you'll repeat | `Playbook` | `how-to` |
| Project data | `Dataset` | `reference` |
| Anything else | `Document` | - |

`genre` is a closed set of exactly four values (`tutorial`, `how-to`, `reference`, `explanation`) - it says how the prose *serves the reader*, not what the note is about. It cannot carry "lecture" or "paper"; that is what `type` and `tags` are for.

Anything finer goes in `tags:`. Link to a module or person in the bundle with `about:` or `isPartOf:`. Two more properties the templates set and `Vault.base` reads: `module:` (the code, `CT5145`) groups the Lectures view, and `created:` sorts it. A web clip brings its own - `source`, `author`, `published` - and keeps them.

```yaml
---
type: Explanation
genre: explanation
title: Self-Attention
description: How scaled dot-product attention lets a token weight every other token.
about:
  - "[[module-ct5145]]"
tags: [transformers, nlp, deep-learning]
---
```

## The bundle (`knowledge_bundle`, beside this vault)

The LOKF knowledge bundle is `.lokf/knowledge/` at the repository root, one level above this vault, and `knowledge_bundle` beside it is a link onto the same folder - the name people and Obsidian open. Open *that* as a vault of its own to curate; this vault never lists it, and a wikilink cannot cross into it, so notes here name records by path (`concepts/self-attention`). Don't reorganise it by hand: the skills and CI address it as `.lokf/knowledge`. This vault is the **workshop**; that folder is the **exhibition**.

It holds **entities, not episodes**: the programme, the 15 modules, the teaching staff, the sources, the open verification issues, and concept notes that have settled. These are the stable things everything else points at. Your daily notes link *into* it.

**A note graduates into the bundle when all three hold:**

1. it has stopped changing week to week;
2. two or more other notes link to it;
3. you would be annoyed to find it wrong six months from now.

Graduating means giving it a `resource` (where the claim comes from), a `description`, and a `status`, then letting `lokf-curator` confirm it. Everything else stays in the vault - that is what keeps the review queue reviewable by one person.

Bundle frontmatter is stricter than vault frontmatter:

- `status` is exactly `draft` | `stable` | `deprecated`. Nothing else.
- **Every relation is a list, even with one value.** `about:` then `- <iri>` on the next line.
  A bare `about: <iri>` fails validation.
- Actor strings are `human:<id>` or `process:<id>` - `human:noelmcloughlin`, never an email.
- `timestamp` and `citations` are deprecated; use `generated.at` and `sources`.

Validate from `.lokf/` at the repository root with `just lokf-validate` and `just lokf-check-refs`.

## Daily habit

Open today's daily note first (`02-Daily/`, the calendar icon in the ribbon). New note or web clip -> lands in `00-Inbox` -> gets a `type` and a `title` -> moves to its folder; a PDF goes to `60-Assets/PDFs` and is linked by page (`[[paper.pdf#page=7]]`). Weekly, empty the inbox (`Home` embeds it) and ask which concept notes are ready to graduate. The Obsidian features this leans on, and what to use each for, are in the repository `README.md`, Part 1.

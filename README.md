# msc-ai-galway-2026

One Obsidian vault for the work, and a knowledge bundle beside it opened as a vault of its own. A working showcase of the [LOKF](https://lokf.nolan-nichols.com) sidecar pattern, built around a two-year, part-time MSc in Computer Science (Artificial Intelligence) at University of Galway.

**The vault is the workshop. The bundle is the exhibition.** Everyday notes stay in the vault and change freely; what has settled graduates into the bundle, where every record says what it is, where it came from and who last checked it - and two Obsidian plugins keep that visible while you work there. The two never share an index: the workshop vault does not see the bundle, and the bundle vault holds nothing but records.

> **Two ways in.** This README is one; the other is a docent. Install [`lokf-docent`](https://github.com/noelmcloughlin/lokf-agent-skills) into whatever agent you already use - `npx skills add noelmcloughlin/lokf-agent-skills --skill lokf-docent --yes` - and ask it anything about this project - *What is self-attention, and who checked it?*, say. It answers from `.lokf/knowledge/`, the checked part of what the project knows, says how far each answer has been trusted (still a draft, checked by automation only, or confirmed by a named person), opens the source for exact values, and records what it couldn't answer so the gap gets filled. One door for a person at a prompt, an agent reading this file, or a chatbot that can load a skill. **Agents:** if `.lokf/knowledge/index.md` exists, read it first - `llms.txt` says how to weigh it.

## Layout

```text
msc-ai-galway-2026/                 the host: one git repository
├── MSc-AI/                          THE WORKSHOP - open this folder in Obsidian for everyday notes
│   ├── 00-Inbox/ … 99-Archive/      lifecycle folders
│   ├── 01-Dashboard/                Home, CONVENTIONS, MOCs/
│   └── .obsidian/                   vault config; LOKF Registrar, LOKF Curator, Templater installed
├── .lokf/                           the sidecar: toolkit, schema extension, justfile, CI wrapper
│   └── knowledge/                   THE EXHIBITION - the bundle, one record per file
│       └── .obsidian/               its own vault config; both LOKF plugins installed
├── knowledge_bundle -> .lokf/knowledge    the doorway: open THIS in Obsidian to curate
├── .github/workflows/               registrar (validate + provenance gate), librarian (scheduled refresh)
├── llms.txt                         tells an agent to read the bundle first
└── inputs/                          the original programme-data export (historical)
```

One folder, two names. The skills, the `lokf` toolkit and CI address `.lokf/knowledge`; people and Obsidian open `knowledge_bundle`, the link `lokf-sidecar` lays beside every sidecar so folder pickers, which hide dot-folders, have a name to pick. The `MSc-AI` vault never lists it: Obsidian skips a link that resolves inside the tree it sits in, and never indexes a dot-folder.

## Open it

1. **Obsidian → Open folder as vault → `MSc-AI/`** for the workshop. Nothing about the bundle shows here; the status bar reads *LOKF: no bundle* and *Curate: no bundle*, which is correct.
2. **Obsidian → Open folder as vault → `knowledge_bundle`** for the exhibition - the link itself, not the repository root. Its `.obsidian/` is committed with both plugins installed and already listing this bundle's custom types; **trust community plugins** when asked.
3. Read the exhibition vault's status bar: **LOKF ✓** means every record is well-formed; **Confirmed 0/55** means nobody has confirmed anything yet. That second number is yours to raise.

## Your first ten minutes

1. In the workshop vault, open `01-Dashboard/MOCs/Deep Learning` - the map over the seeded notes and the records they became.
2. In the exhibition vault, open `concepts/self-attention`. Its frontmatter carries the **Draft** badge, and the curator panel (the ribbon gem, or *Open curator panel*) lists it under *Worth ten minutes today*.
3. Press **Review this note**. The card puts the source (arXiv 1706.03762) beside the claim and asks whether the source still says this. Read §3.2 of the paper, then **Confirm**. The plugin asks for your curator id once (`noelmcloughlin`), appends a `human:` event under `verified`, clears `draft`, and writes a Curation line into `log.md`. **Confirmed 1/55.**
4. Open `concepts/transformer`: a draft with an `## Open questions` section the librarian left - which module introduces it first? If you know, **Wrong - I corrected it** and fix the `about` list; if not, **Later**.
5. Commit with a signed commit. The registrar workflow accepts a `human:` confirmation only when its author approved the pull request or signed the commit; [knowledge-registrar.yaml](.github/workflows/knowledge-registrar.yaml) carries the three `git config` lines.
6. **Ask the docent.** Install `lokf-docent` into your agent ([Tooling](#tooling), below) and ask *What is self-attention, and who checked it?* The answer's footer reads *confirmed by a person* - you, a minute ago. That is the exhibition answering for itself.

That is the whole loop: the librarian (an agent) derives and refreshes, you confirm, the registrar keeps it honest, and the docent answers from it.

## What the seeded notes show

Every seeded note is tagged `example`; delete them when the real ones arrive.

| State in the workshop | Note |
| --- | --- |
| No frontmatter - a raw capture | `00-Inbox/Attention scribbles` |
| Obsidian-native only (`aliases`, `tags`); not bundle-ready | `30-Knowledge/Concepts/Gradient Descent` |
| Episodic - stays in the vault for good | `10-Programme/Modules/CT5145 - Deep Learning/Week 1 - What deep learning is` |
| Bundle-shaped (`type`, `genre`, `description`, `resource`, `about`) | `30-Knowledge/Concepts/Self-Attention`, the lab in `20-Learning/`, the paper note in `40-Research/` |

| Stage in the exhibition | Record |
| --- | --- |
| Draft, nobody has checked it | `concepts/self-attention` |
| Stable, checked by automation only | `concepts/gradient-descent`, `sources/vaswani-2017-attention-is-all-you-need` |
| Draft with an open question for the curator | `concepts/transformer`, and the four records under `issues/` |
| Confirmed by a person | none yet - step 3 above writes the first |
| Retired | none yet - the **Retire** verb writes it |

A workshop note names the record it became by path, never by wikilink: the two vaults do not share an index, which is the point. The rule for graduating a note, and the frontmatter each kind of note gets, is `MSc-AI/01-Dashboard/CONVENTIONS.md`.

## Tooling

```bash
cd .lokf
just lokf-install      # once: uv sync
just lokf-validate     # every record against msc-ai.yaml (LOKF core + this bundle's classes)
just lokf-check-refs   # every typed relation points at a record that exists
just lokf-serve        # SPARQL endpoint + graph explorer
just lokf-link         # recreate the knowledge_bundle doorway if a sync service or a Windows checkout dropped it
```

The agent skills install at the repository root (gitignored):

```bash
npx skills add noelmcloughlin/lokf-agent-skills \
  --skill lokf-sidecar --skill lokf-librarian --skill lokf-curator --skill lokf-docent --yes
```

Run `lokf-librarian` from the repository root to refresh the bundle from the vault; `lokf-curator` is the same review session as the plugin, in a terminal; `lokf-docent` answers questions from the bundle first - for you at a prompt, or for any agent or chatbot pointed at this repository. CI runs `lokf validate` and the provenance gate on every pull request that touches the bundle; the scheduled librarian workflow stays inert until the `KNOWLEDGE_LIBRARIAN_ENABLED` repository variable is set.

## Status and caveats

- Four `VerificationIssue` records are open: the public sources contradict each other on module count, core/optional status, the year/credit heading and one assessment sentence. Nothing downstream depends on them.
- `base_iri` is the placeholder `msc-ai.example` (RFC 2606). It mints every `id`; migrate it before anyone links in.
- `msc-ai.yaml` exists because the generated LOKF schema rejects unknown types and keys despite the spec's tolerance rule; `.lokf/knowledge/log.md`, "Schema extension", has the story.
- `.retired/` holds what the 2026-09-12 restructure took out - a second vault config at the repository root, a plugin enhancement plan, two empty notes. `git rm -r .retired` once you agree.

## Releases

`CHANGELOG.md`'s `## [Unreleased]` section is written as changes happen. On merge to `main`, [`semantic-release.yml`](.github/workflows/semantic-release.yml) computes the next version from the commits since the last tag, refuses to proceed if that section is empty, retitles it to a dated version heading, and publishes a GitHub Release from it - gated behind the `release` Environment (configure required reviewers on it once, in Settings → Environments).

## Upstream

- [`lokf-agent-skills`](https://github.com/noelmcloughlin/lokf-agent-skills) · [`obsidian-lokf-registrar`](https://github.com/noelmcloughlin/obsidian-lokf-registrar) · [`obsidian-lokf-curator`](https://github.com/noelmcloughlin/obsidian-lokf-curator)
- [LOKF specification](https://lokf.nolan-nichols.com) · [`lokf` toolkit](https://pypi.org/project/lokf/)

Apache-2.0 - see [LICENSE](LICENSE) and [NOTICE](NOTICE). AI-assisted work here follows [AI_COVENANT.md](AI_COVENANT.md).

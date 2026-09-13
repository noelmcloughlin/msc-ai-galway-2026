# msc-ai-galway-2026

One Obsidian vault for the work, and a knowledge bundle beside it, opened as a vault of its own. A working showcase of the [LOKF](https://lokf.nolan-nichols.com) sidecar pattern, built around a two-year, part-time MSc in Computer Science (Artificial Intelligence) at University of Galway.

The vault is the **workshop**: everyday notes live there and change freely. The bundle is the **exhibition**: what has settled graduates into it, and every record there says what it is, where it came from and who last checked it, with two Obsidian plugins keeping that visible while you work. The two never share an index. The workshop does not see the bundle, and the bundle holds nothing but records.

> **Two ways in.** This README is one; the other is a docent. Install [`lokf-docent`](https://github.com/noelmcloughlin/lokf-agent-skills) into whatever agent you already use - `npx skills add noelmcloughlin/lokf-agent-skills --skill lokf-docent --yes` - and ask it *What is self-attention, and who checked it?* It answers from `.lokf/knowledge/`, says how far each answer has been trusted (a draft, checked by automation only, or confirmed by a named person), opens the source for exact values, and records what it could not answer so the gap gets filled. **Agents:** if `.lokf/knowledge/index.md` exists, read it first - `llms.txt` says how to weigh it.

## Layout

```text
msc-ai-galway-2026/                 the host: one git repository
├── MSc-AI/                          THE WORKSHOP - open this folder in Obsidian for everyday notes
│   ├── 00-Inbox/ … 99-Archive/      lifecycle folders
│   ├── 01-Dashboard/                Home, CONVENTIONS, MOCs/
│   └── .obsidian/                   vault config; Templater on, the two LOKF plugins installed but off
├── .lokf/                           the sidecar: toolkit, schema extension, justfile, CI wrapper
│   └── knowledge/                   THE EXHIBITION - the bundle, one record per file
│       └── .obsidian/               its own vault config; both LOKF plugins installed and on
├── knowledge_bundle -> .lokf/knowledge    the doorway: open THIS in Obsidian to curate
├── .github/workflows/               registrar (validate + provenance gate), librarian (scheduled refresh), lint, release
├── llms.txt                         tells an agent to read the bundle first
└── inputs/                          where the 2026 programme extract was imported from; the file is gone, its records remain
```

One folder, two names. The skills, the `lokf` toolkit and CI address `.lokf/knowledge`; people and Obsidian open `knowledge_bundle`, the link `lokf-sidecar` lays beside every sidecar so that folder pickers, which hide dot-folders, have a name to pick. Open the link itself, never the repository root. The `MSc-AI` vault never lists the bundle: both names sit outside that folder.

## Open it

1. **Obsidian → Open folder as vault → `MSc-AI/`** for the workshop. Nothing about the bundle shows here, and that is correct. Templater is the only plugin switched on; the two LOKF plugins are installed but off, and would report *no bundle* if you turned them on.
2. **Obsidian → Open folder as vault → `knowledge_bundle`** for the exhibition - the link itself, not the repository root. Its `.obsidian/` is committed with both plugins installed and already listing this bundle's custom types; **trust community plugins** when asked.
3. Read the exhibition vault's status bar: **LOKF ✓** means every record is well-formed; **Confirmed 0/55** means nobody has confirmed anything yet. That second number is yours to raise.

## Your first ten minutes

1. In the workshop vault, open `01-Dashboard/MOCs/Deep Learning` - the map over the seeded notes and the records they became.
2. In the exhibition vault, open `concepts/self-attention`. Its frontmatter carries the **Draft** badge, and the curator panel (the ribbon gem, or *Open curator panel*) lists it under *Worth ten minutes today*.
3. Press **Review this note**. The card puts the source (arXiv 1706.03762) beside the claim and asks whether the source still says this. Read §3.2 of the paper, then **Confirm**. The plugin asks for your curator id once (your GitHub login), appends a `human:` event under `verified`, clears `draft`, and writes a Curation line into `log.md`. **Confirmed 1/55.**
4. Open `concepts/transformer`: a draft with an `## Open questions` section the librarian left - which module introduces it first? If you know, **Wrong - I corrected it** and fix the `about` list; if not, **Later**.
5. Commit, signed, and open a pull request. The **registrar** workflow accepts a `human:` confirmation only when that person approved the pull request or signed the commit; a solo maintainer signs, and [knowledge-registrar.yaml](.github/workflows/knowledge-registrar.yaml) carries the three `git config` lines.
6. **Ask the docent.** With `lokf-docent` installed ([Tooling](#tooling)), ask *What is self-attention, and who checked it?* The answer's footer now reads *confirmed by a person* - you, a minute ago.

That is the whole loop: the **librarian** (an agent) derives and refreshes, you, the **curator**, confirm, the **registrar** keeps it honest, and the **docent** answers from it.

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
| Draft with an open question for the curator | `concepts/transformer` |
| Draft by nature - a contradiction between sources | the four records under `issues/` |
| Confirmed by a person | none yet - step 3 above writes the first |
| Retired | none yet - the **Retire** verb writes it |

A workshop note names the record it became by path (`concepts/self-attention`), never by wikilink: a wikilink cannot cross vaults, which is the point. The rule for graduating a note, and the frontmatter each kind of note gets, is `MSc-AI/01-Dashboard/CONVENTIONS.md`.

## Tooling

```bash
cd .lokf
just lokf-install      # once: uv sync
just lokf-validate     # every record against msc-ai.yaml (LOKF core + this bundle's classes)
just lokf-check-refs   # every typed relation points at a record that exists
just lokf-serve        # SPARQL endpoint + graph explorer
just lokf-link         # recreate the knowledge_bundle doorway if a sync service or a Windows checkout dropped it
```

The four agent skills install at the repository root (gitignored):

```bash
npx skills add noelmcloughlin/lokf-agent-skills \
  --skill lokf-sidecar --skill lokf-librarian --skill lokf-curator --skill lokf-docent --yes
```

Run `lokf-librarian` from the repository root to refresh the bundle from the vault and the public sources. `lokf-curator` is the plugin's review session, in a terminal; `lokf-docent` is the aside at the top; `lokf-sidecar` only repairs the sidecar's own files. CI runs `lokf validate` and the provenance gate on every pull request that touches the bundle; the scheduled librarian workflow stays inert until the `KNOWLEDGE_LIBRARIAN_ENABLED` repository variable is set. What each skill does in full is in [lokf-agent-skills](https://github.com/noelmcloughlin/lokf-agent-skills).

## Status and caveats

- Four `VerificationIssue` records are open: the public sources contradict each other on module count, core/optional status and the year/credit heading, and one assessment sentence reads as another course's. Nothing downstream depends on them.
- `base_iri` is the placeholder `msc-ai.example` (RFC 2606). It mints every `id`; migrate it before anyone links in.
- `msc-ai.yaml` exists because the generated LOKF schema rejects unknown types and keys despite the spec's tolerance rule; `.lokf/knowledge/log.md`, "Schema extension", has the story.
- `.retired/` holds what the 2026-09-12 restructure took out; `git rm -r .retired` once you agree.

## Upstream

- [`lokf-agent-skills`](https://github.com/noelmcloughlin/lokf-agent-skills) · [`obsidian-lokf-registrar`](https://github.com/noelmcloughlin/obsidian-lokf-registrar) · [`obsidian-lokf-curator`](https://github.com/noelmcloughlin/obsidian-lokf-curator)
- [LOKF specification](https://lokf.nolan-nichols.com) · [`lokf` toolkit](https://pypi.org/project/lokf/)

## Contributing, security, license

[CONTRIBUTING.md](CONTRIBUTING.md) covers the pre-PR checks and how a release is cut; participation is covered by the [Code of Conduct](CODE_OF_CONDUCT.md), and [AI_COVENANT.md](AI_COVENANT.md) sets out how AI-assisted contributions are handled. Report security issues as [SECURITY.md](SECURITY.md) describes. Apache-2.0 - see [LICENSE](LICENSE) and [NOTICE](NOTICE).

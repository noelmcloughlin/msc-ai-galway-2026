# msc-ai-galway-2026

One Obsidian vault, many folders, and a knowledge bundle beside it. A working showcase of the [LOKF](https://lokf.nolan-nichols.com) sidecar pattern, built around a two-year, part-time MSc in Computer Science (Artificial Intelligence) at University of Galway.

**The vault is the workshop. The bundle is the exhibition.** Everyday notes stay in the vault and change freely; what has settled graduates into the bundle, where every record says what it is, where it came from and who last checked it - and two Obsidian plugins keep that visible while you work.

## Layout

```text
msc-ai-galway-2026/                 the host: one git repository
├── MSc-AI/                          THE VAULT - open this folder in Obsidian
│   ├── 00-Inbox/ … 99-Archive/      lifecycle folders for everyday notes (the workshop)
│   ├── 01-Dashboard/                Home, CONVENTIONS, MOCs/
│   ├── knowledge_bundle/            THE BUNDLE - a real folder of the vault (the exhibition)
│   └── .obsidian/                   vault config; LOKF Registrar, LOKF Curator, Templater installed
├── .lokf/                           the sidecar: toolkit, schema extension, justfile, CI wrapper
│   └── knowledge -> ../MSc-AI/knowledge_bundle    the tools' name for the same folder
├── .github/workflows/               registrar (validate + provenance gate), librarian (scheduled refresh)
├── llms.txt                         tells an agent to read the bundle first
└── inputs/                          the original programme-data export (historical)
```

Two names, one folder. People and Obsidian use `MSc-AI/knowledge_bundle/`; the skills, the `lokf` toolkit and CI use `.lokf/knowledge`. That is the `lokf-sidecar` skill's *visible layout*, the right one when the host is a vault: Obsidian indexes the bundle like any other folder, and no second vault is needed.

## Open it

1. **Obsidian → Open folder as vault → `MSc-AI/`.** Not the repository root. (`knowledge_bundle` on its own works too, as a focused desk, but nothing requires it.)
2. **Trust community plugins** when asked. All three are already enabled. LOKF Curator finds `knowledge_bundle/` on its own; LOKF Registrar's saved settings name it under *Bundle root folders* and already list this bundle's custom types.
3. Read the status bar: **LOKF ✓** means every record is well-formed; **Confirmed 0/55** means nobody has confirmed anything yet. That second number is yours to raise.

## Your first ten minutes

1. Open `01-Dashboard/MOCs/Deep Learning` - the map over the seeded notes and the records they became.
2. Open `knowledge_bundle/concepts/self-attention`. Its frontmatter carries the **Draft** badge, and the curator panel (the ribbon gem, or *Open curator panel*) lists it under *Worth ten minutes today*.
3. Press **Review this note**. The card puts the source (arXiv 1706.03762) beside the claim and asks whether the source still says this. Read §3.2 of the paper, then **Confirm**. The plugin asks for your curator id once (`noelmcloughlin`), appends a `human:` event under `verified`, clears `draft`, and writes a Curation line into `log.md`. **Confirmed 1/55.**
4. Open `knowledge_bundle/concepts/transformer`: a draft with an `## Open questions` section the librarian left - which module introduces it first? If you know, **Wrong - I corrected it** and fix the `about` list; if not, **Later**.
5. Commit with a signed commit. The registrar workflow accepts a `human:` confirmation only when its author approved the pull request or signed the commit; [knowledge-registrar.yaml](.github/workflows/knowledge-registrar.yaml) carries the three `git config` lines.

That is the whole loop: the librarian (an agent) derives and refreshes, you confirm, the registrar keeps it honest.

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

The rule for graduating a note, and the frontmatter each kind of note gets, is `MSc-AI/01-Dashboard/CONVENTIONS.md`.

## Tooling

```bash
cd .lokf
just lokf-install      # once: uv sync
just lokf-validate     # every record against msc-ai.yaml (LOKF core + this bundle's classes)
just lokf-check-refs   # every typed relation points at a record that exists
just lokf-serve        # SPARQL endpoint + graph explorer
just lokf-link         # recreate .lokf/knowledge if a sync service dropped the link
```

The agent skills install at the repository root (gitignored):

```bash
npx skills add noelmcloughlin/lokf-agent-skills \
  --skill lokf-sidecar --skill lokf-librarian --skill lokf-curator --skill lokf-docent --yes
```

Run `lokf-librarian` from the repository root to refresh the bundle from the vault; `lokf-curator` is the same review session as the plugin, in a terminal; `lokf-docent` answers questions from the bundle first. CI runs `lokf validate` and the provenance gate on every pull request that touches the bundle; the scheduled librarian workflow stays inert until the `KNOWLEDGE_LIBRARIAN_ENABLED` repository variable is set.

## Status and caveats

- Four `VerificationIssue` records are open: the public sources contradict each other on module count, core/optional status, the year/credit heading and one assessment sentence. Nothing downstream depends on them.
- `base_iri` is the placeholder `msc-ai.example` (RFC 2606). It mints every `id`; migrate it before anyone links in.
- `msc-ai.yaml` exists because the generated LOKF schema rejects unknown types and keys despite the spec's tolerance rule; `knowledge_bundle/log.md`, "Schema extension", has the story.
- `.retired/` holds what the 2026-09-12 restructure took out - a second vault config at the repository root, a plugin enhancement plan, two empty notes. `git rm -r .retired` once you agree.

## Upstream

- [`lokf-agent-skills`](https://github.com/noelmcloughlin/lokf-agent-skills) · [`obsidian-lokf-registrar`](https://github.com/noelmcloughlin/obsidian-lokf-registrar) · [`obsidian-lokf-curator`](https://github.com/noelmcloughlin/obsidian-lokf-curator)
- [LOKF specification](https://lokf.nolan-nichols.com) · [`lokf` toolkit](https://pypi.org/project/lokf/)

Apache-2.0 - see [LICENSE](LICENSE) and [NOTICE](NOTICE). AI-assisted work here follows [AI_COVENANT.md](AI_COVENANT.md).

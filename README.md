# msc-ai-galway-2026

An Obsidian vault for a two-year, part-time MSc in Computer Science (Artificial Intelligence) at University of Galway - built as a working example of the [LOKF](https://lokf.nolan-nichols.com) ecosystem: agent skills that scaffold and maintain a knowledge bundle, a domain schema extension, and two companion Obsidianplugins, all pointed at the same files.

Full rationale in: **[DESIGN.md](DESIGN.md)**.

This file is the front door - open here first.

## Layout

```
msc-ai-galway-2026/                  ← git root; run skills and just recipes from here
├── .agents/skills/         ← the 4 lokf-agent-skills (scaffolding, librarian, curator, docent)
├── inputs/                 ← research material and the original programme-data export
├── DESIGN.md                 ← why everything below looks the way it does
└── MSc-AI/                 ← THE OBSIDIAN VAULT - open THIS folder in Obsidian
    ├── .obsidian/plugins/  ← lokf-enforcer, lokf-curator, templater-obsidian
    ├── .lokf/              ← the LOKF knowledge bundle (hidden from Obsidian's index)
    │   ├── lokf.yaml       ← pinned copy of the core LOKF schema (0.7.0)
    │   ├── msc-ai.yaml     ← this bundle's domain extension (see below)
    │   └── knowledge/      ← 50 concepts: programme, modules, people, sources, issues...
    ├── knowledge_bundle -> .lokf/knowledge   ← visible symlink; how Obsidian sees the bundle
    ├── 00-Inbox/ … 99-Archive/              ← lifecycle folders for everyday notes
    └── 01-Dashboard/CONVENTIONS.md          ← the vault's own house style
```

Two documents, two audiences: **DESIGN.md** is the design record (read it to understand *why*); **`MSc-AI/01-Dashboard/CONVENTIONS.md`** is the vault's own quick reference (read it while actually taking notes). This README is neither - it's how to get the whole thing running.

## Quick start

1. **Open `MSc-AI/` as the vault** in Obsidian - not `msc-ai-galway-2026/`. Obsidian never indexes dot-folders, so the `knowledge_bundle` symlink makes the bundle visible and linkable.

2. **Trust and enable community plugins** the first time Obsidian asks (a one-time safety prompt - the three plugins are already registered in `.obsidian/community-plugins.json`, they just need the toggle).

3. **Write in `00-Inbox/`** using the templates in `90-Meta/Templates/` (Concept, Lecture, Paper, Lab note) - each already emits LOKF-shaped frontmatter, so a note is bundle-ready the moment it's worth graduating. See CONVENTIONS.md's graduation rule for when that is.

4. **Validate the bundle** whenever you touch `.lokf/knowledge/` by hand:
   ```bash
   cd MSc-AI/.lokf
   just lokf-install     # once: uv sync
   just lokf-validate    # schema-check all 50 concepts
   just lokf-check-refs  # no relation points at a concept that doesn't exist
   just lokf-serve       # local SPARQL endpoint + graph explorer
   ```
5. **Run the agent skills** from `msc-ai-galway-2026/` (not `MSc-AI/`) so `.agents/skills/` resolves, naming `MSc-AI/` as the host when a skill asks. `lokf-librarian` maintains the bundle day to day; `lokf-curator` is the human-confirmation pass (there's a live queue: four open verification issues in `.lokf/knowledge/issues/`).

## How the pieces interoperate

Everything downstream reads the same 50 Markdown files under `.lokf/knowledge/` - nothing here has its own private copy of the truth. The two plugins and four skills
split the same registrar/curator/librarian division of labour the wider LOKF ecosystem uses:

| Tool | Role | Touches |
| --- | --- | --- |
| `lokf-scaffolding` (skill) | lays the network - built `.lokf/` once | run only to repair |
| `lokf-librarian` (skill) | binds it into order - derives and maintains concepts | `.lokf/knowledge/` |
| `lokf-curator` (skill) | holds the scales - records a human's confirm/correct/retire verdict | `verified`, `status`, `## Open questions` |
| `lokf-docent` (skill) | guides the visitors - answers questions from the bundle | reads only |
| **LOKF Enforcer** (plugin) | the registrar's desk - flags malformed frontmatter live, in the editor | `knowledge_bundle/` only (scoped via `bundleRoots`) |
| **LOKF Curator** (plugin) | the same review session as the skill, without an agent in the loop | same fields as the skill |
| **`msc-ai.yaml`** (schema) | the shared vocabulary all of the above check against | imports `lokf.yaml`, adds this domain's classes |
| **Templater** | authors new notes already in the shape the rest of this table expects | `90-Meta/Templates/` |

```
starter.json - (one-time fix + migrate)---> .lokf/knowledge/*.md <-- lokf-librarian
                                                   │      ▲
                                     validated against    reviewed by
                                     msc-ai.yaml (+lokf.yaml)   lokf-curator (skill or plugin)
                                                   │
                                     seen by LOKF Enforcer live, and by
                                     Obsidian only via knowledge_bundle/
```

## The knowledge bundle, in one paragraph

`.lokf/knowledge/` holds **entities, not episodes**: the programme, its 15 modules, 10 teaching staff (plus 10 `Role` concepts reifying who holds what), 3 sources, 4
open verification issues, and a growing glossary - the stable things everyday notes in the vault point *at*. It needed its own small schema, `msc-ai.yaml`, because LOKF's generated validator is stricter about unknown fields than its own spec's prose promises (see `log.md`'s "Schema extension" entry, and `DESIGN.md`'s appendix,
for the full story - worth reading if you're extending either upstream project). `base_iri` is currently the RFC 2606 placeholder `msc-ai.example`, consistent with
this author's other LOKF bundles, pending a real namespace.

## Status and known caveats

- `lokf-curator`'s review pass hasn't run yet - four open issues are waiting (module-count contradiction, an assessment-wording anomaly, a year/credit heading
  conflict, and a core/optional classification conflict). Safe to leave; nothing downstream depends on them being resolved.
- `lokf-enforcer` and `lokf-curator` (the plugins) were installed from the best pre-built copies available on this machine - no `node`/`npm` toolchain here yet.
  `lokf-enforcer`'s build trails its manifest's latest version bump by a few hours; functionally low-risk (new checks are warnings, never hard errors), but worth a
  rebuild-and-refresh once npm is available.
- Dataview isn't installed. Bases (Obsidian core) is tried first, per the plan; reach for Dataview only if Bases falls short.

## Upstream

- [`lokf-agent-skills`](https://github.com/noelmcloughlin/lokf-agent-skills) - the four skills
- [`obsidian-lokf-enforcer`](https://github.com/noelmcloughlin/obsidian-lokf-enforcer) - the registrar's-desk plugin
- [`obsidian-lokf-curator`](https://github.com/noelmcloughlin/obsidian-lokf-curator) - the review-session plugin
- [LOKF specification](https://lokf.nolan-nichols.com) · [`lokf` toolkit (PyPI)](https://pypi.org/project/lokf/)

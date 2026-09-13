# msc-ai-galway-2026

An [Obsidian](https://obsidian.md) vault for a two-year, part-time MSc in Computer Science (Artificial Intelligence) at University of Galway, and a knowledge bundle beside it that grows out of the vault as the notes settle.

**The vault is the workshop. The bundle is the exhibition.** The workshop is where the degree happens - lectures, labs, papers, half-formed ideas - and it is the first half of this README. The exhibition is what has settled, kept in a form that tools and agents can check ([LOKF](https://lokf.nolan-nichols.com)). It is the second half, and it can wait until the workshop has something worth showing.

> **Two ways in.** This README is one; the other is a docent. Install [`lokf-docent`](https://github.com/noelmcloughlin/lokf-agent-skills) into whatever agent you already use - `npx skills add noelmcloughlin/lokf-agent-skills --skill lokf-docent --yes` - and ask it anything about this project - *What is self-attention, and who checked it?*, say. It answers from `.lokf/knowledge/`, the checked part of what the project knows, says how far each answer has been trusted (still a draft, checked by automation only, or confirmed by a named person), opens the source for exact values, and records what it couldn't answer so the gap gets filled. **Agents:** if `.lokf/knowledge/index.md` exists, read it first - `llms.txt` says how to weigh it.

## Part 1 - The workshop: study in Obsidian

Open Obsidian → **Open folder as vault** → `MSc-AI/`. Everything in this half is Obsidian doing what it does natively; the [Obsidian Help](https://obsidian.md/help) "Get started" pages (create a vault, first note, link notes) take ten minutes and are worth them.

### Your first hour

1. **Read `Home`**, then skim `CONVENTIONS`, both in `01-Dashboard/`. One rule to carry: folders say what *stage* a note is at; properties and links say what it is *about*.
2. **Install [Obsidian Web Clipper](https://obsidian.md/clipper)** in your browser and point its default template at `00-Inbox`. Clip the programme page and one module page. Every clip arrives with `title`, `source`, `author`, `published` and `created` properties, which is a citation for free; use the Highlighter to keep only the passages that matter.
3. **Drop PDFs into `60-Assets/PDFs/`**: the handbook, the first reading list. Obsidian opens PDFs itself. `[[paper.pdf#page=7]]` links to a page and `![[paper.pdf#page=7]]` embeds it beside your own words ([embeds](https://obsidian.md/help/embeds)).
4. **Open today's daily note** (the calendar icon in the ribbon, or the command *Daily notes: Open today's daily note*). It lands in `02-Daily/<year>/` from the `Daily note` template: what you studied, the questions, the `- [ ]` tasks. This is the habit that carries the rest.
5. **Take one lecture's notes** in `10-Programme/Modules/<CODE> - <Title>/` from the `Lecture note` template (*Templater: Create new note from template*). Wikilink every concept you meet, `[[Gradient Descent]]`, whether or not the note exists yet; Obsidian creates it when you click.
6. **Open `Vault.base`** in `01-Dashboard/`: Inbox, Lectures by module, Papers, Concepts, Daily, as live tables over the properties you just wrote. No plugin, no query language ([Bases](https://obsidian.md/help/bases)).
7. **End the week in the inbox.** Every clip and scribble gets a `type` and moves to its folder, or is deleted. Then ask which concept note has stopped changing: that is what graduates (Part 2).

### What Obsidian gives you, and what to use it for

| Feature | Use it for |
| --- | --- |
| [Properties](https://obsidian.md/help/properties) | `type`, `module`, `tags`, `about`, `created` at the top of every note - what Bases and search read |
| [Links](https://obsidian.md/help/links) and backlinks | `[[Note]]`, `[[Note#Heading]]`; the Backlinks pane shows who cites a concept, which is the graduation signal |
| [Tags](https://obsidian.md/help/tags) | `#lecture`, `#lab`, `#paper`; nested `#todo/ask` for finer slicing; `tag:#lab` in search |
| [Daily notes](https://obsidian.md/help/plugins/daily-notes) | one dated note per study day, templated, by year |
| [Templates](https://obsidian.md/help/plugins/templates) | the five in `90-Meta/Templates`; Templater (installed) runs the four with logic in them, the core plugin runs the daily one |
| [Bases](https://obsidian.md/help/bases) | database views over properties - table, cards, list, kanban, map - in `.base` files you can embed in a note |
| [Canvas](https://obsidian.md/help/plugins/canvas) | a whiteboard for a module map or a thesis argument; `.canvas` files, open JSON |
| [Web viewer](https://obsidian.md/help/plugins/web-viewer) | read a course page inside Obsidian, reader mode, save it to the vault (core plugin, off until you want it) |
| [Import](https://obsidian.md/help/import) | notes from Notion, OneNote, Evernote, Apple Notes and the rest, via the Obsidian team's Importer plugin |

### Plugins

Community plugins run third-party code, so Obsidian starts in *Restricted mode* until you [turn them on](https://obsidian.md/help/community-plugins). Three are installed in this vault:

- **Templater**: the smarter templates (date, title, prompts).
- **LOKF Registrar** and **LOKF Curator**: for the exhibition vault in Part 2. Here they read *no bundle* and stay quiet.

One browser extension, which is not a vault plugin: **[Obsidian Web Clipper](https://obsidian.md/help/web-clipper)**, the official one. For a course that lives on the web, it is the essential piece.

Worth adding when the need is real, not before: **Zotero** with ZotLit or Zotero Integration once the reading list is long enough to need a reference manager (Zotero keeps the references and PDF annotations, Obsidian keeps the notes); **Excalidraw** for diagrams; **Obsidian Git** if you study from more than one machine, since this repository is the backup.

### Habits that hold

- One idea per note. Link before you file.
- Capture fast, file weekly. The inbox is allowed to be a mess for six days.
- The lecture note is the receipt; the concept note is the asset. Write the concept note the same week.
- Every paper note gets a `resource:` (DOI or URL). It is what makes a claim checkable later.
- Use the system before you build it. Add a folder, a tag or a base when a real note needs one.

## Part 2 - The exhibition: when a note has settled (optional)

A concept note graduates when it has stopped changing, two or more notes link to it, and you would be annoyed to find it wrong in six months (`CONVENTIONS.md` has the rule and the frontmatter it gets). Graduated notes live in the **knowledge bundle**: `.lokf/knowledge/` beside the vault, one Markdown record per idea, each saying what it is, where it came from and who last checked it. That is [LOKF](https://lokf.nolan-nichols.com), which schema tools, CI and agents can validate and query. The two never share an index: the workshop vault does not see the bundle, and the bundle holds nothing but records.

**Open it as a second vault.** Obsidian → **Open folder as vault** → `knowledge_bundle`, the link at the repository root (the link itself, not the repository). Its `.obsidian/` is committed with both plugins installed; trust community plugins when asked. The status bar then reads **LOKF ✓**, every record well-formed, and **Confirmed 0/56**, the number this whole arrangement exists to raise.

**The loop.** Four roles from [`lokf-agent-skills`](https://github.com/noelmcloughlin/lokf-agent-skills), two of them also at your desk as plugins:

| Role | Who | Where |
| --- | --- | --- |
| Librarian | an agent derives and refreshes records from the vault, marks them drafts | `lokf-librarian` in a terminal, or the scheduled workflow |
| Curator | you confirm, correct, retire or send back | [LOKF Curator](https://github.com/noelmcloughlin/obsidian-lokf-curator) in the exhibition vault, or `lokf-curator` |
| Registrar | every record stays well-formed | [LOKF Registrar](https://github.com/noelmcloughlin/obsidian-lokf-registrar) as you type; `knowledge-registrar.yaml` on every pull request |
| Docent | answers questions from the bundle, with a trust label on each | `lokf-docent` in any agent |

**Try it once.** In the exhibition vault open `concepts/self-attention`: a **Draft**, listed under *Worth ten minutes today* in the curator panel. Press **Review this note**, read §3.2 of the paper it cites, press **Confirm**. The plugin asks for your curator id once (`noelmcloughlin`), records a `human:` verification and writes a line to `log.md`: **Confirmed 1/56**. Commit with a signed commit, since the registrar workflow accepts a `human:` confirmation only from a signed commit or an approved pull request. Then ask the docent *What is self-attention, and who checked it?* and read the footer.

**Tooling**, from `.lokf/` at the repository root:

```bash
cd .lokf
just lokf-install      # once: uv sync
just lokf-validate     # every record against msc-ai.yaml (LOKF core + this bundle's classes)
just lokf-check-refs   # every typed relation points at a record that exists
just lokf-serve        # SPARQL endpoint + graph explorer
just lokf-link         # recreate the knowledge_bundle link if a sync service or a Windows checkout dropped it
```

The four skills install at the repository root (gitignored):

```bash
npx skills add noelmcloughlin/lokf-agent-skills \
  --skill lokf-sidecar --skill lokf-librarian --skill lokf-curator --skill lokf-docent --yes
```

**Read next:** the [`lokf-agent-skills`](https://github.com/noelmcloughlin/lokf-agent-skills) README for the four roles and why a person always holds the scales; the [LOKF Registrar](https://github.com/noelmcloughlin/obsidian-lokf-registrar) and [LOKF Curator](https://github.com/noelmcloughlin/obsidian-lokf-curator) READMEs for the plugins; [`.lokf/README.md`](.lokf/README.md) for this bundle's tooling and its small schema extension; the [LOKF specification](https://lokf.nolan-nichols.com) for the format itself.

## Layout

```text
msc-ai-galway-2026/                 one git repository
├── MSc-AI/                          THE WORKSHOP - open this folder in Obsidian
│   ├── 00-Inbox/ 02-Daily/ 10-Programme/ 20-Learning/ 30-Knowledge/ 40-Research/ 50-Projects/ 60-Assets/ 90-Meta/ 99-Archive/
│   ├── 01-Dashboard/                Home, CONVENTIONS, Vault.base, MOCs/
│   └── .obsidian/                   vault config; Templater, LOKF Registrar, LOKF Curator installed
├── .lokf/                           the sidecar: toolkit, schema extension, justfile, CI wrapper
│   └── knowledge/                   THE EXHIBITION - the bundle, one record per file, its own .obsidian/
├── knowledge_bundle -> .lokf/knowledge    the doorway: open THIS in Obsidian to curate
├── .github/workflows/               registrar (validate + provenance gate), librarian (scheduled refresh), lint, release
├── llms.txt                         tells an agent to read the bundle first
└── inputs/                          the original programme-data export (historical)
```

Every seeded note in the vault is tagged `example`, one per stage from a raw inbox capture to a graduated concept; `01-Dashboard/MOCs/Deep Learning` maps them. Delete them when the real ones arrive.

## Status and caveats

- Four `VerificationIssue` records are open: the public sources contradict each other on module count, core/optional status, the year/credit heading and one assessment sentence. Nothing downstream depends on them.
- `base_iri` is the placeholder `msc-ai.example` (RFC 2606). It mints every `id`; migrate it before anyone links in.
- `msc-ai.yaml` exists because the generated LOKF schema rejects unknown types and keys despite the spec's tolerance rule; `.lokf/knowledge/log.md`, "Schema extension", has the story.
- `.retired/` holds what the 2026-09-12 restructure took out. `git rm -r .retired` once you agree.

## Releases

`CHANGELOG.md`'s `## [Unreleased]` section is written as changes happen. On merge to `main`, [`semantic-release.yml`](.github/workflows/semantic-release.yml) computes the next version from the commits since the last tag, retitles that section to a dated version heading and publishes a GitHub Release from it, gated behind the `release` Environment. Only `feat:`, `fix:`, `security:` and a breaking-change marker cut a release; `docs:` and `chore:` merge cleanly and release nothing.

Pull requests that add a `by: human:` confirmation to the knowledge bundle need a signed commit - the `provenance` job accepts a verified signature in place of an approving review, which a sole maintainer cannot give themselves. Ordinary changes need no signature, though signing everything is worth the one-time setup: [`lokf-agent-skills`](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/CONTRIBUTING.md#signing-your-commits) walks through GPG and SSH, and how to renew a GPG key before it expires. Do **not** turn on the *require signed commits* branch rule: the release job's own commit is made inside a runner and is unsigned.

## Upstream

- [`lokf-agent-skills`](https://github.com/noelmcloughlin/lokf-agent-skills) · [`obsidian-lokf-registrar`](https://github.com/noelmcloughlin/obsidian-lokf-registrar) · [`obsidian-lokf-curator`](https://github.com/noelmcloughlin/obsidian-lokf-curator)
- [LOKF specification](https://lokf.nolan-nichols.com) · [`lokf` toolkit](https://pypi.org/project/lokf/) · [Obsidian Help](https://obsidian.md/help)

Apache-2.0 - see [LICENSE](LICENSE) and [NOTICE](NOTICE). AI-assisted work here follows [AI_COVENANT.md](AI_COVENANT.md).

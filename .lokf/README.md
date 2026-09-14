# `.lokf/` - MSc Artificial Intelligence's machine-readable knowledge base

A small **sidecar** that captures MSc Artificial Intelligence's own knowledge - its
programme, modules, teaching staff, sources, and open verification questions - as plain
Markdown files that are **also a queryable knowledge graph**. It does not touch the vault
build; it's independent tooling you can run on its own.

## The 60-second version

- **OKF (Open Knowledge Format)** is a folder of Markdown files, one *concept* per file, each with a little YAML frontmatter block (`type`, `title`, `description`, ...). Just files you can read on GitHub or in any editor.
- **LOKF (Linked OKF)** gives every field a precise meaning (schema.org, DCAT, PROV-O), so the same Markdown turns into RDF and is queryable with SPARQL. The [`lokf`](https://pypi.org/project/lokf/) PyPI package is the toolkit that does the turning.

You write normal Markdown; you get a validated, queryable graph for free.

## What's in here

```text
.lokf/
|-- knowledge/            # the bundle, one Markdown file per concept - the exhibition vault, opened via ../knowledge_bundle
|   |-- index.md          # bundle metadata + table of contents (reserved)
|   |-- log.md            # change history (reserved)
|   |-- programme/  modules/  people/  sources/  issues/  glossary/  playbooks/  concepts/
|-- lokf.yaml             # pinned copy of the core LOKF schema (0.7.0), for msc-ai.yaml to import
|-- msc-ai.yaml           # this bundle's domain schema: LOKF core plus its own classes and keys
|-- pyproject.toml        # declares the `lokf` toolkit as a dependency
|-- justfile              # convenience commands (below)
|-- scripts/knowledge-librarian.sh   # the scheduled librarian's wrapper, run by .github/workflows/knowledge-librarian.yaml
|-- feedback.md           # appears once a reader's agent records a gap; input for the librarian, not knowledge
```

`msc-ai.yaml` extends the LOKF vocabulary into this domain: a LinkML schema that imports the
pinned `lokf.yaml` and adds the classes and keys a degree programme needs (`Programme`,
`Module`, `ects`, ...). `just lokf-validate` checks every record against both. The recipe,
and what such a schema does and does not do, is the librarian skill's
[domain-schema reference](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/skills/lokf-librarian/references/domain-schema.md).

`knowledge/` is the real folder, and `../knowledge_bundle` at the repository root is a link onto it - the name people and Obsidian open, as a
vault of its own (the exhibition; the `MSc-AI/` vault is the workshop and never lists it). Open the link itself, never the repository root. Git
carries the link; if a sync service or a Windows checkout drops it, `just lokf-link` recreates it.

## Prerequisites

- [`uv`](https://docs.astral.sh/uv/) - the Python package runner.
- [`just`](https://just.systems/) - optional, for the shortcut recipes.

## Use it

```bash
cd .lokf
just lokf-install    # one-time: install the toolkit (uv sync)
just lokf-validate   # check every concept against the LOKF schema
just lokf-serve      # local SPARQL endpoint + interactive graph explorer
just lokf-convert    # print the whole bundle as RDF (Turtle)
just lokf-check-refs # every typed relation points at a record that exists
```

Without `just`:

```bash
cd .lokf
uv sync
uv run lokf validate --schema msc-ai.yaml knowledge
uv run lokf serve knowledge
uv run lokf convert knowledge --format ttl
```

(`serve`/`convert`/`query` have no `--schema` flag, so they resolve the plain core schema via
the local `lokf.yaml` copy - complete for the ten standard relations `lokf-check-refs` uses,
not yet aware of `msc-ai.yaml`'s classes and slots.)

## Add or edit a concept

1. Create a Markdown file under `knowledge/<kind>/` (e.g. `modules/`, `people/`).
2. Start with frontmatter. OKF requires only `type`; this bundle also sets `id`, `title`, `description`, and `resource` on every concept. A new class or key of this domain's own is declared in `msc-ai.yaml` first, and `type` names the class exactly.
3. Link concepts with typed-relation keys whose values are target `id`s - e.g. `dependsOn:`, `about:`, `references:`, `isPartOf:`. Run `uv run lokf vocab` to list available relations.
4. Add the concept to the table of contents in `knowledge/index.md`.
5. Run `just lokf-validate` before committing.

## Learn more

- LOKF specification & Golden Rules: <https://lokf.nolan-nichols.com/>
- `lokf` toolkit (PyPI): <https://pypi.org/project/lokf/>
- LOKF schema, if Python isn't available: <https://github.com/nicholsn/lokf/blob/main/lokf.yaml>
- OKF spec: <https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md>

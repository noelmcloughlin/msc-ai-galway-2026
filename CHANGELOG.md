# Changelog

All notable changes to this repository are documented here. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Versions are computed by semantic-release from Conventional Commits on `main`, which promotes `## [Unreleased]` into a dated heading, tags it, and publishes the GitHub Release from it; see [CONTRIBUTING.md](CONTRIBUTING.md#releasing-maintainers).

## [Unreleased]

### Added

- **The contributor files the sibling repositories already had**: `CONTRIBUTING.md` (setup, pre-PR checks, how a release is cut, and what the `main` ruleset does and does not enforce), `CODE_OF_CONDUCT.md`, a pull-request template and two issue templates under `.github/`.
- **Daily notes, a base, and Templater's folder.** Daily notes is on, writing `02-Daily/<year>/` from a new `Daily note` template (core `{{date}}` syntax, so Templater is not needed for it); `01-Dashboard/Vault.base` gives five views over the vault's properties - Inbox, Lectures by module, Papers, Concepts, Daily - and `Home` embeds the Inbox one.

### Changed

- **`README.md` rewritten Obsidian-first.** Part 1 is the workshop: a first hour in seven steps, what Obsidian gives you natively, the one plugin that is on and the one browser extension that matters, five habits. Part 2 is the exhibition: the graduation rule, the second vault, the four roles, one confirmation to try, and tooling. The layout tree names every workflow and which plugins are on in which vault; the seeded notes get a table per vault; "Releases" moves to `CONTRIBUTING.md`. Three claims corrected: the workshop vault runs Templater only (both LOKF plugins are installed there but off, since 1.0.1), `inputs/` no longer holds the extract, and the four `issues/` records are drafts by nature. `Home` and `CONVENTIONS` follow. The repository describes itself as a personal vault, not a showcase.
- **The workshop vault speaks Obsidian, not LOKF.** The seeded notes and five templates drop `type`, `genre`, `about` and `resource`. What a note is becomes a tag (`lecture`, `lab`, `paper`, `concept`, `daily`), its module is `module:`, and where a claim comes from is `source:` - the property the Web Clipper already writes. `Vault.base` reads the tags and `source`; `CONVENTIONS` states the vocabulary in one table. The bundle's own vocabulary stays in the bundle, the librarian's to write and the registrar's to check.
- **Knowledge bundle refreshed by `lokf-librarian`** (third steady-state pass): the four live sources re-fetched raw and 49 records re-verified against them, `index.md`'s pointer to `CONVENTIONS.md` corrected for the bundle's new location, and the source map extended with the Deep Learning book chapter and a note on what the bundle consciously leaves out. See `.lokf/knowledge/log.md`.

### Fixed

- The exhibition vault's committed plugin manifests said LOKF Registrar 0.5.0 and LOKF Curator 0.3.0 while carrying the 1.1.0 and 1.0.0 builds; 1.0.1 updated only the workshop vault's copies. Both vaults now agree with the builds they carry.
- `SECURITY.md` claimed pull requests must pass checks before merge; the `main` ruleset blocks deletion and force-pushes and requires linear history, deliberately nothing more, for the reason `CONTRIBUTING.md` gives. `AI_COVENANT.md` pointed at a README section that does not exist here. `.github/dependabot.yml`'s comment said no `actionlint` step exists; one does, and it checks syntax, not staleness.
- `Home.md`'s warning callout predated the 2026-09-11 refresh that confirmed semester and credits for every taught module; it now says what is and is not settled. Four seeded notes still pointed into the bundle by wikilink; they name records by path now.

## [1.0.1] - 2026-09-13

### Changed

- **Two vaults, not one.** The bundle moved out of the vault: `MSc-AI/knowledge_bundle/` is now `.lokf/knowledge/`, the sidecar's one real folder, and `knowledge_bundle` at the repository root is the doorway link `lokf-sidecar` lays down - opened *itself* in Obsidian as the exhibition vault, with its own `.obsidian/` committed (both LOKF plugins installed and pointed at this bundle's custom types) - while `MSc-AI/` stays the workshop and no longer lists the bundle at all. A day of the previous layout showed why: Obsidian indexed the bundle folder with the notes, so link suggestions, the quick switcher, graph and search mixed records with everyday notes. The skills retired that "visible layout" the same day; this repository follows. Every concept `id` is unchanged.
- CI, the wrapper and the justfile address the bundle as `.lokf/knowledge` and `knowledge_bundle`, exactly as the sidecar templates do, and `just lokf-link` now recreates the doorway rather than the tools' link. `llms.txt`, `SECURITY.md`, `CONVENTIONS.md`, `Home.md`, the concept template and the three seeded notes that pointed into the bundle now name its records by path, since a wikilink cannot cross vaults. Both vaults carry the current plugin builds (LOKF Registrar `release/0.5.0`, LOKF Curator `release/0.3.0`).

## [1.0.0] - 2026-09-13

### Added

- **Rebuilt as a one-vault-many-folders showcase.** `MSc-AI/` is the vault; `MSc-AI/knowledge_bundle/` is a real folder inside it, with `.lokf/knowledge` at the repository root linking onto it - the `lokf-sidecar` skill's *visible layout*. The sidecar moved from `MSc-AI/.lokf/` to the repository root, beside the vault rather than inside it.
- **Seven example notes across the vault's lifecycle folders**, each in a different frontmatter state on purpose - from a raw inbox capture with none at all to a Map of Content - so the README's ten-minute walkthrough has something real to click through.
- **Four bundle records spanning every curation stage**: `self-attention` (draft, unchecked), `gradient-descent` (stable, automation-checked), `transformer` (draft, with open questions), and a stable `Reference` source record.
- `README.md` rewritten short: the layout, how to open it, a walkthrough ending in a real **Confirm**, and both plugins already pointed at this bundle.
- **CI**: `knowledge-registrar.yaml` and `knowledge-librarian.yaml`, scoped to the bundle and validating against `msc-ai.yaml`; both name it under its two paths, since a git pathspec never traverses the `.lokf/knowledge` symlink.
- **Semantic release**: the version is computed from Conventional Commits on `main`, this file's `## [Unreleased]` section is promoted into a dated heading, and a GitHub Release is published from it.
- `llms.txt`, `.markdownlint-cli2.jsonc`, and `.gitignore` files for the new layout.

### Changed

- The plugin folder follows the upstream rename: `lokf-enforcer` is now `lokf-registrar`.

### Removed

- The root-level `.obsidian/` (the repository root was never meant to be a vault) and the doorway link the earlier attempt pointed the wrong way. Both moved to `.retired/` rather than deleted; `git rm -r .retired` when you agree.

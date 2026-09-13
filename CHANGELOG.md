# Changelog

All notable changes to this repository are documented here. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

No version below has been published as a GitHub release yet, so entries describe development history against `main`, not user-facing upgrades. No tags exist yet either, which is why version headings carry no compare links.

## [Unreleased]

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

# Changelog

All notable changes to this repository are documented here. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

No version below has been published as a GitHub release yet, so entries describe development history against `main`, not user-facing upgrades. No tags exist yet either, which is why version headings carry no compare links.

## [Unreleased]

### Added

- **Daily notes, a base, and Templater's folder.** Daily notes is on, writing `02-Daily/<year>/` from a new `Daily note` template (core `{{date}}` syntax, so Templater is not needed for it); `01-Dashboard/Vault.base` gives five views over the vault's properties - Inbox, Lectures by module, Papers, Concepts, Daily - and `Home` embeds the Inbox one.

### Changed

- **README rewritten Obsidian-first.** Part 1 is the workshop: a first hour in seven steps, a table of what Obsidian gives you natively, the plugins and the one browser extension that matter, and five habits. Part 2 is the exhibition, compacted to the graduation rule, the second vault, the four roles, one confirmation to try, and tooling. `Home` and `CONVENTIONS` follow. The repository describes itself as a personal vault, not a showcase.
- **Two vaults, not one.** The bundle moved out of the vault to `.lokf/knowledge/`, with `knowledge_bundle` at the repository root as the doorway opened *itself* as the exhibition vault, its own `.obsidian/` committed; `MSc-AI/` stays the workshop and no longer lists it. A day of the previous layout showed why: Obsidian indexed the bundle folder with the notes. Every concept `id` is unchanged.
- CI, the wrapper and the justfile address the bundle as `.lokf/knowledge` and `knowledge_bundle`, as the sidecar templates do. Notes that pointed into the bundle now name records by path, since a wikilink cannot cross vaults.

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

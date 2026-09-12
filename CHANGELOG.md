# Changelog

All notable changes to this repository are documented here. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

No version below has been published as a GitHub release yet, so entries describe development history against `main`, not user-facing upgrades. No tags exist yet either, which is why version headings carry no compare links.

## [Unreleased]

### Added

- **Rebuilt as a one-vault-many-folders showcase.** `MSc-AI/` is the vault (`Open folder as vault` opens it, not the repository root); `MSc-AI/knowledge_bundle/` is a real folder inside it, with `.lokf/knowledge` at the repository root linking onto it - the `lokf-sidecar` skill's *visible layout*. The sidecar itself (`.lokf/`: toolkit, `msc-ai.yaml` schema extension, justfile, CI scripts) moved from `MSc-AI/.lokf/` to the repository root, alongside it rather than inside the vault.
- **Seven example notes across the vault's lifecycle folders**, each a different frontmatter state on purpose: a raw inbox capture with no frontmatter at all, a lecture note, a lab note, two concept notes at opposite ends of curation (one alias-only, one with `tags`), a literature note, and a Map of Content - so *Your first ten minutes* in the README has something real to click through rather than an empty vault.
- **Four bundle records spanning every curation stage** this project's trust model names: `concepts/self-attention.md` (`status: draft`, unchecked), `concepts/gradient-descent.md` (`status: stable`, `verified: process:lokf-librarian` - automation-checked, not yet a person), `concepts/transformer.md` (draft, with an `## Open questions` section the librarian left), and `sources/vaswani-2017-attention-is-all-you-need.md` (a `Reference`, stable).
- `README.md` rewritten short and concrete: the layout tree, how to open it, a ten-minute walkthrough ending in a real **Confirm**, and the two plugins' settings already pointed at this bundle.
- `.github/workflows/knowledge-registrar.yaml` and `knowledge-librarian.yaml`, scoped to `MSc-AI/knowledge_bundle/**` and validating against `msc-ai.yaml`; both name the bundle under its two paths, since a git pathspec never traverses the `.lokf/knowledge` symlink.
- `llms.txt`, `.markdownlint-cli2.jsonc`, and root/`​.lokf/.gitignore` for the new layout.
- **Semantic-release, hardened.** [`semantic-release.yml`](.github/workflows/semantic-release.yml) computes the next version from Conventional Commits on `main`, refuses to proceed if this file's own `## [Unreleased]` section is empty, promotes it into a dated heading via a new, dependency-free `.github/scripts/changelog-release.mjs`, and publishes a GitHub Release from that text - this repository's first release automation. The write-scoped job sits behind the `release` GitHub Environment - configure required reviewers on it in Settings → Environments.

### Changed

- The two Obsidian plugins moved with the sidecar: `lokf-enforcer` is now `lokf-registrar` (`MSc-AI/.obsidian/plugins/lokf-registrar/`, `community-plugins.json` updated), matching the upstream rename in `obsidian-lokf-registrar`.

### Removed

- The old root-level `.obsidian/` (the repository root was never meant to be opened as a vault) and the root `knowledge_bundle` doorway link the earlier attempt left pointing the wrong way - both moved under `.retired/` pending deletion, not deleted outright, so nothing is lost if something in them turns out to matter.

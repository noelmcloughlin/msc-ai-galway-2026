## Summary

## What changed?

- [ ] The vault (`MSc-AI/`) - notes, templates, `Vault.base`, or `.obsidian/` configuration
- [ ] This repo's own `.lokf/knowledge/` bundle (the exhibition generated from the vault)
- [ ] Conventions or documentation (`README.md`, `01-Dashboard/CONVENTIONS.md`)
- [ ] Repository packaging only (CI, templates, release tooling)

## Checklist

- [ ] If `.lokf/` changed, `cd .lokf && just lokf-validate` passes
- [ ] If bundle relations changed, `cd .lokf && just lokf-check-refs` passes
- [ ] Vault changes still open cleanly in Obsidian, and the bundle has not leaked into
      the workshop's link suggestions, graph, or search
- [ ] Markdown and links pass (`lint-and-docs.yaml`: markdownlint, lychee, codespell)
- [ ] `CHANGELOG.md` updated under `[Unreleased]` if this changes behaviour - only
      `feat:`, `fix:` and `security:` cut a release; see [Releases](../README.md#releases)

## AI Assistance

If you used AI tools while preparing this PR, you are still the author and responsible for understanding, verifying, and defending your submission. Please engage with reviewers personally rather than through your agent during feedback and revisions. Don't dump LLM output into this PR without curation. See the [AI Covenant](../AI_COVENANT.md) for details.

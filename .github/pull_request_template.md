## Summary

## What changed?

- [ ] Workshop vault (`MSc-AI/`) - notes, templates, dashboards
- [ ] The knowledge bundle (`.lokf/knowledge/`) - records, `index.md`, `log.md`
- [ ] Sidecar tooling or schema (`.lokf/justfile`, `pyproject.toml`, `msc-ai.yaml`)
- [ ] Repository packaging only (CI, docs, templates unrelated to vault or bundle content)

## Checklist

- [ ] If `.lokf/` changed, `cd .lokf && just lokf-validate && just lokf-check-refs` both pass
- [ ] Any `human:` confirmation this PR adds is mine, and the commit that adds it is signed (or I have approved this PR from that account) - the registrar's `provenance` job checks this
- [ ] `npx markdownlint-cli2` passes locally (CI also runs lychee and codespell over every `*.md`)
- [ ] If a seeded note or record changed, the README's ten-minute walkthrough still holds
- [ ] `CHANGELOG.md` updated under `[Unreleased]` if this changes content or behaviour, not just wording

## AI Assistance

If you used AI tools while preparing this PR, you are still the author and responsible for understanding, verifying, and defending your submission. Please engage with reviewers personally rather than through your agent during feedback and revisions. Don't dump LLM output into this PR without curation. See the [AI Covenant](../AI_COVENANT.md) for details.

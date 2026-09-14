# Contributing to msc-ai-galway-2026

*This file is a checklist, not a design log. Each rule is a line or two that links to where its reasoning lives - a code comment, a workflow header, or a page in the skills repository - and CI's docs job holds the file to a word budget so it stays that way.*

Thanks for your interest in improving this repository.

## What this repository is

A working example of the LOKF sidecar pattern around one Obsidian vault: the `MSc-AI/` workshop, the `.lokf/knowledge/` exhibition, and the tooling and CI that keep the exhibition honest. It ships no code of its own. Most contributions touch exactly one of these:

| What | Lives in |
| --- | --- |
| Workshop notes, templates, dashboards | `MSc-AI/` |
| The knowledge bundle, one record per file | `.lokf/knowledge/`, also reachable as `knowledge_bundle` |
| Sidecar tooling and the domain schema | `.lokf/` (`justfile`, `pyproject.toml`, `msc-ai.yaml`) |
| CI, release, repository packaging | `.github/`, root files |

The seeded notes and records are tagged `example` and exist to be clicked through. A change to them should keep the README's ten-minute walkthrough true.

## Development setup

[`uv`](https://docs.astral.sh/uv/) is required for the bundle tooling; [`just`](https://just.systems/) is optional, but every command below assumes it. Obsidian is only needed to open the two vaults.

```bash
git clone https://github.com/noelmcloughlin/msc-ai-galway-2026.git
cd msc-ai-galway-2026/.lokf
just lokf-install      # uv sync
just lokf-validate     # every record against msc-ai.yaml
just lokf-check-refs   # every typed relation resolves
```

`.lokf/README.md` explains the sidecar's layout and why `msc-ai.yaml` exists.

### Agent skills (optional)

The bundle is maintained with [lokf-agent-skills](https://github.com/noelmcloughlin/lokf-agent-skills), **installed, never committed**: `.agents/`, `.claude/` and `skills-lock.json` are git-ignored, and CI installs the librarian itself at run time, at the release `LOKF_SKILLS_REF` in [`knowledge-librarian.yaml`](.github/workflows/knowledge-librarian.yaml) names.

```bash
npx skills add noelmcloughlin/lokf-agent-skills \
  --skill lokf-sidecar --skill lokf-librarian --skill lokf-curator --skill lokf-docent --yes
```

`lokf-librarian` derives and refreshes records, `lokf-curator` records a person's verdict, `lokf-docent` answers from the bundle, and `lokf-sidecar` repairs the sidecar's own files. None is needed to edit a workshop note.

## Before opening a pull request

- **Bundle changes**: `cd .lokf && just lokf-validate && just lokf-check-refs` must both pass. The registrar workflow runs the first on every pull request that touches `.lokf/**`; it keeps records well-formed and never judges whether they are true.
- **A `human:` confirmation must be yours to make.** A pull request that adds a `by: human:<id>` event under `verified` passes the registrar's `provenance` job only if that account approved the pull request or signed the commit; a solo maintainer cannot approve their own, so sign - [Signing your commits](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/docs/signing-commits.md) walks through GPG and SSH.
- **Prose changes**: `lint-and-docs.yaml` runs markdownlint against `.markdownlint-cli2.jsonc`, link-checking against `lychee.toml`, and codespell over every `*.md`. Run `npx markdownlint-cli2` locally before pushing. Templater templates and `.retired/` are excluded on purpose.
- A link into a sibling repository must already resolve on that repository's `main` - the link check follows it for real. Land upstream content first; `lychee.toml` is only for links permanently outside our control.
- **Workflow or script changes**: expect ShellCheck and `actionlint` to have an opinion. Every action is pinned to a commit SHA with the version in a trailing comment; CI fails one that is not, and Dependabot (`.github/dependabot.yml`) bumps the pins.
- **If your change alters content or behaviour**, not just wording, add a line or two under `## [Unreleased]` in [CHANGELOG.md](CHANGELOG.md). The release pipeline refuses an empty one.
- Keep changes focused; the PR template's checklist is the short form of this list.

## Code of conduct

Participation here is covered by the [Contributor Covenant](CODE_OF_CONDUCT.md), the same one the sibling LOKF repositories use.

## Using AI tools

AI assistance is welcome; this repository's own bundle is refreshed by an agent on a schedule. What that requires of you is unchanged: you are the author of whatever you submit, you are responsible for understanding and defending it in review, and an agent may not participate in discussion on your behalf. A record's `human:` confirmation may only ever be written by the person it names, in a session where they actually checked the source. The full rules, including how the scheduled librarian is held to them, are in [AI_COVENANT.md](AI_COVENANT.md).

## Releasing (maintainers)

Commits typed with [Conventional Commits](https://www.conventionalcommits.org/) decide the version, and `## [Unreleased]` is the release note. On a push to `main`, [`semantic-release.yml`](.github/workflows/semantic-release.yml) promotes the changelog, creates the `vX.Y.Z` tag and publishes a GitHub Release whose notes are the promoted section; every pull request gets a `--dry-run` preview of the same. [How the LOKF repositories release](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/docs/releasing.md) has the whole pipeline and the `release` Environment that gates it.

### What the repository settings mean for you

The ruleset on `main` blocks deletion and force-pushes and requires linear history, and deliberately stops there: a rule requiring pull requests, passing checks or signed commits would also reject the release job's own push. So the pull-request discipline is a convention held to by the maintainer, and a red check is yours to fix. The reasoning is on the [releasing page](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/docs/releasing.md#what-the-repository-settings-mean-for-you).

## License

By contributing, you agree that your contributions are licensed under the [Apache License 2.0](LICENSE), the license this repository ships under.

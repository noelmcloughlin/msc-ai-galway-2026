# Contributing to msc-ai-galway-2026

Thanks for your interest in improving this showcase.

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

The bundle is maintained with [lokf-agent-skills](https://github.com/noelmcloughlin/lokf-agent-skills), **installed, never committed**: `.agents/`, `.claude/` and `skills-lock.json` are git-ignored, and CI installs the librarian skill itself at run time, pinned to a release.

```bash
npx skills add noelmcloughlin/lokf-agent-skills \
  --skill lokf-sidecar --skill lokf-librarian --skill lokf-curator --skill lokf-docent --yes
```

`lokf-librarian` derives and refreshes records from the vault and the public sources; `lokf-curator` records a person's verdict, the same session the LOKF Curator plugin runs in the editor; `lokf-docent` answers questions from the bundle; `lokf-sidecar` only repairs the sidecar's own files. None is needed to edit a workshop note.

## Before opening a pull request

- **Bundle changes**: `cd .lokf && just lokf-validate && just lokf-check-refs` must both pass. The registrar workflow runs the first on every pull request that touches `.lokf/**`; it keeps records well-formed and never judges whether they are true.
- **A `human:` confirmation must be yours to make.** A pull request that adds a `by: human:<id>` event under `verified` passes the registrar's `provenance` job only if that account approved the pull request or signed the commit that introduced it. A solo maintainer cannot approve their own pull request, so sign: the three `git config` lines are in the comments of [`knowledge-registrar.yaml`](.github/workflows/knowledge-registrar.yaml), and the [skills repository's guide](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/CONTRIBUTING.md#signing-your-commits) walks through GPG and SSH setup.
- **Prose changes**: `lint-and-docs.yaml` runs markdownlint against `.markdownlint-cli2.jsonc`, link-checking against `lychee.toml`, and codespell over every `*.md`. Run `npx markdownlint-cli2` locally before pushing. Templater templates and `.retired/` are excluded on purpose.
- **Workflow or script changes**: expect ShellCheck and `actionlint` to have an opinion. Every action is pinned to a commit SHA with the version in a trailing comment; keep that convention, and let Dependabot (`.github/dependabot.yml`) bump the pins rather than floating one to a tag.
- **If your change alters content or behaviour** (not just wording), add an entry under `## [Unreleased]` in [CHANGELOG.md](CHANGELOG.md). The release pipeline refuses to run on an empty one.
- Keep changes focused; the PR template's checklist is the short version of this section.

## Code of conduct

Participation here is covered by the [Contributor Covenant](CODE_OF_CONDUCT.md), the same one the sibling LOKF repositories use.

## Using AI tools

AI assistance is welcome; this repository's own bundle is refreshed by an agent on a schedule. What that requires of you is unchanged: you are the author of whatever you submit, you are responsible for understanding and defending it in review, and an agent may not participate in discussion on your behalf. A record's `human:` confirmation may only ever be written by the person it names, in a session where they actually checked the source. The full rules, including how the scheduled librarian is held to them, are in [AI_COVENANT.md](AI_COVENANT.md).

## Releasing (maintainers)

The version number is not hand-picked. Write `## [Unreleased]` in [CHANGELOG.md](CHANGELOG.md) as you go, with a [Conventional Commits](https://www.conventionalcommits.org/) type on each commit, and open the pull request as normal. **Only `feat:`, `fix:` and `security:` cut a release.** `docs:`, `chore:`, `refactor:`, `style:` and `test:` merge, release nothing, and leave their changelog entries to ship with the next release that does. If a pull request should release and its commits are typed too quietly, squash-merge it and give the squash commit the right type.

On a push to `main`, [`semantic-release.yml`](.github/workflows/semantic-release.yml):

1. computes the next version from the commits since the last tag, and stops if none warrant one;
2. refuses to proceed if `## [Unreleased]` is empty (`.github/scripts/changelog-release.mjs check`);
3. retitles that section to `## [X.Y.Z] - YYYY-MM-DD` with a fresh empty one above it, commits the changelog, creates the `vX.Y.Z` tag, and publishes a GitHub Release whose notes are the promoted section.

Every pull request into `main` gets a `--dry-run` preview of the same, so a broken commit message or script is caught in review. The `release` job runs behind the `release` GitHub Environment - **configure required reviewers on it once, in Settings → Environments** - or every qualifying merge ships unattended.

### What the repository settings mean for you

- **Changes reach `main` by pull request, but no ruleset enforces it.** The ruleset on `main` blocks deletion and force-pushes and requires linear history, and stops there. A rule requiring pull requests, or passing status checks, would also reject the release job's own push of the promoted changelog, and `github-actions[bot]` cannot be put on a ruleset's bypass list. So the pull-request discipline is a convention held to by the maintainer. CI runs on every pull request and is read before merge, but it is not what blocks one; treat a red check as yours to fix.
- **"Require signed commits" as a branch rule is deliberately off** and must stay off: a commit made inside a runner is unsigned, so the rule would break every release. Signing your own commits locally is a different thing, and the registrar's `provenance` job is where it matters.

## License

By contributing, you agree that your contributions are licensed under the [Apache License 2.0](LICENSE), the license this repository ships under.

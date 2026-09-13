# Security Policy

## This repository

`msc-ai-galway-2026` is a personal Obsidian vault: example notes, a validated LOKF knowledge bundle, and the tooling that keeps both in shape. It ships no plugin, no server, and no runtime code of its own - opening the vault in Obsidian runs nothing beyond whatever community plugins you already have installed there (see the README's "Open it" and "Upstream" sections for which ones).

Because of that, the realistic risk surface here isn't the vault content itself - it's this **GitHub repository's own automation**: the scheduled agent that helps maintain the knowledge bundle, and the release pipeline that publishes this repository's changelog as a GitHub Release. Everything below concerns that.

Any generated or imported bundle content still deserves the same scrutiny you'd give any Markdown from an external source before treating it as source-of-truth knowledge - that's a property of the content, not of this repository's tooling.

## Reporting a vulnerability

Please use GitHub's [private vulnerability reporting](https://github.com/noelmcloughlin/msc-ai-galway-2026/security/advisories/new)
rather than a public issue. Include:

- the affected file (a workflow under `.github/workflows/`, the wrapper script, or vault content);
- how it is exploitable;
- any proof of concept or minimally sufficient reproduction details.

## Supported versions

Only the latest published release line receives security fixes.

- Security fixes are issued as patch releases when needed.
- Older release tags are not maintained.
- If a published release is found to be vulnerable, the fix lands in the latest release branch and is noted in [CHANGELOG.md](CHANGELOG.md).

---

## This repository's own automation

### Scope

The repository's non-Markdown execution surfaces are:

- the lint-and-docs workflow in `.github/workflows/lint-and-docs.yaml` (ShellCheck, `actionlint`, markdownlint, link-checking, codespell) and the registrar workflow in `.github/workflows/knowledge-registrar.yaml`, which validates the knowledge bundle's *form* on every `.lokf/**`/`knowledge_bundle/**` PR - both read-only;
- the scheduled GitHub Action in `.github/workflows/knowledge-librarian.yaml`, which installs a pinned [`lokf-agent-skills`](https://github.com/noelmcloughlin/lokf-agent-skills) skill and runs it against this repo's own knowledge bundle, then opens a review PR. The agent itself runs with **no write permissions** - see "Repository hardening" below;
- the wrapper script `.lokf/scripts/knowledge-librarian.sh`, which that workflow executes;
- `.github/workflows/semantic-release.yml`, which computes the next version from Conventional Commits, promotes `CHANGELOG.md`, and publishes a GitHub Release from that text - no package is built here, and nothing but this workflow creates a tag;
- this repository's own `README.md`, `llms.txt`, and the knowledge bundle content that the librarian workflow reads and writes: these are prompt-injection surfaces whenever an agent is asked to act on repository text, external URLs, or reader feedback.

### Repository hardening

- GitHub Actions are pinned to reviewed commit SHAs instead of floating tags, in every workflow. `.github/dependabot.yml` keeps those pins current (weekly), alongside the `.lokf/` sidecar's Python toolchain.
- Every workflow declares `permissions: {}` at the top level, so each job opts into only the scopes it needs and nothing inherits a broader default.
- All workflows run Step Security's hardened-runner in audit mode to monitor runner egress.
- **The librarian workflow runs in two jobs so the agent and the write token never meet.** `refresh` runs the agent - third-party code - with `contents: read` and `persist-credentials: false`, so no git credential is on disk while it executes, and hands its proposed change to `publish` as a patch artifact. Only `publish`, which runs no agent code, holds `contents: write` / `pull-requests: write`. What each of those jobs checks, and why, is the template's design - see the next section.
- `semantic-release.yml`'s `release` job, which pushes a commit/tag to `main` and publishes the GitHub Release, sits behind the `release` GitHub Environment (configure required reviewers on it in this repository's own Settings > Environments); its `npm install` of the pinned release tooling runs with `--ignore-scripts`.
- **`main` is protected, but not by a merge gate.** Deletions and force-pushes are blocked and linear history is required. A rule requiring pull requests, or requiring status checks to pass, is deliberately **not** enabled: rulesets apply both to direct pushes as well as to merges, and the release workflow's own commit to `main` cannot be exempted from them - a bypass list accepts roles, teams, GitHub Apps and Dependabot, and `github-actions[bot]` is none of those. What gates a change here is access control, not a rule: only the maintainer can write to this repository, CI runs on every pull request, and its result is read before merging. Secret scanning and push protection are enabled. These are GitHub repository *settings* rather than files in the tree - nothing in CI can assert they are still in force, so keeping them enabled is a maintainer responsibility.
- CodeQL is intentionally **not** enabled: this repository has no application code at all - Markdown, YAML workflows, and one shell script are its entire non-prose surface, and the shell script is covered by ShellCheck above. If that ever changes, add it then rather than carrying unused overhead now.

### The librarian workflow: what is inherited, what this repository owns

`.github/workflows/knowledge-librarian.yaml` and `.lokf/scripts/knowledge-librarian.sh` are copies of the `lokf-sidecar` template in [`lokf-agent-skills`](https://github.com/noelmcloughlin/lokf-agent-skills), and their security design is that repository's, documented once there:

- how the two-job split keeps the agent away from any write-scoped token, and why the `publish` job's own checks - path confinement derived from the patch itself, refusal of any added `by: human:` claim - are the backstop rather than the wrapper's: [Prompt-injection guards](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/SECURITY.md#prompt-injection-guards);
- how the `lokf-librarian` skill treats `.lokf/feedback.md`, and everything else it did not author, as content to inspect and never as instructions to follow: the same section;
- why a skill's stated scope is prose, not a permission, when you run it interactively: [Interactive use: scope is advisory, not enforced](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/SECURITY.md#interactive-use-scope-is-advisory-not-enforced);
- why the review PR it opens does not trigger this repository's `pull_request` checks, and what stands in for them: [Repository hardening](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/SECURITY.md#repository-hardening).

Restating that design here would imply this repository controls it. It does not: a change there reaches here on the next `LOKF_SKILLS_REF` bump, and a copy here would go stale with nothing in CI to notice. What this repository does own, and is accountable for:

- **The deployed copy.** Both files match the template except for `--schema msc-ai.yaml` on the validate step of both workflows, this bundle's own schema. Anything further that is edited locally becomes this repository's responsibility rather than the template's; `diff` against `skills/lokf-sidecar/templates/` in a checkout of `lokf-agent-skills` to confirm.
- **The switches.** The agent step runs only while the `KNOWLEDGE_LIBRARIAN_ENABLED` repository variable is `true`; `AGENT_CLI` chooses *which* non-interactive agent runs, never *what command*; the workflow triggers only on `schedule` and `workflow_dispatch`. All three are readable in this repository's own YAML.
- **The surfaces.** What the librarian reads *here* is `README.md`, `llms.txt`, the vault's notes and the knowledge bundle - and `.lokf/feedback.md`, the one input that can originate from someone with no repository access.
- **The consequence if the inherited guards fail.** The agent holds no write-scoped token, so the worst outcome is a proposed patch confined to the knowledge bundle and `.lokf/feedback.md`, applied by a job that runs no agent code, arriving as a pull request that only the maintainer can merge, after reading it. Nothing on that path reaches the vault itself (`MSc-AI/`) or a published release.
- **The attribution gate is installed.** `.github/workflows/knowledge-registrar.yaml` here carries the template's `validate`, `provenance` and `attestation` jobs, unchanged apart from the schema flag, so a newly added `human:` confirmation must be backed by evidence GitHub holds - an approving review, or a verified signature on the introducing commit. [Human attribution](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/SECURITY.md#human-attribution-human-is-a-claim-not-a-credential) has the design and its stated limits.

See also [AI_COVENANT.md](AI_COVENANT.md), which sets the human-accountability rules this automation operates under.

## What this does not cover

This policy does not turn the repository into a formal sandbox. It does not guarantee that a compromised agent, upstream dependency, or compromised runner will be impossible to abuse. It is a practical baseline intended to reduce the most likely failures in this repository's own release and AI-assisted documentation workflow - a set of concerns that does not extend to your own Obsidian setup or the community plugins you choose to install.

If a vulnerability is found in an external dependency, a copied workflow template, or an AI agent harness behavior, it should still be reported here with clear scope and reproduction details.

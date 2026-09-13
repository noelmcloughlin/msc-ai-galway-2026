# Security Policy

## This repository

`msc-ai-galway-2026` is a showcase Obsidian vault: example notes, a validated LOKF knowledge bundle, and the tooling that keeps both in shape. It ships no plugin, no server, and no runtime code of its own - opening the vault in Obsidian runs nothing beyond whatever community plugins you already have installed there (see the README's "Open it" and "Upstream" sections for which ones).

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

### Interactive use of the agent skills: scope is advisory, not enforced

If you install the `lokf-agent-skills` locally to work on this repo's knowledge bundle, know that a skill's stated scope is prose guidance, not a security boundary - an agent may have broader tool access in your local environment than the skill's description implies. Treat any AI-driven workflow as a tool that acts with the permissions your harness grants it, not as a permission system enforced by Markdown.

The only automated write path is the scheduled librarian workflow, and it is split so that the agent never holds a write-scoped token at all (see "Repository hardening" below). Review before merge remains a human responsibility.

### Repository hardening

- GitHub Actions are pinned to reviewed commit SHAs instead of floating tags, in every workflow. `.github/dependabot.yml` keeps those pins current (weekly), alongside the `.lokf/` sidecar's Python toolchain.
- Every workflow declares `permissions: {}` at the top level, so each job opts into only the scopes it needs and nothing inherits a broader default.
- All workflows run Step Security's hardened-runner in audit mode to monitor runner egress.
- **The librarian workflow runs in two jobs so the agent and the write token never meet.** The `refresh` job runs the agent - third-party code - with `contents: read` and `persist-credentials: false`, so no git credential is on disk while it executes; it hands its proposed change to the `publish` job as a patch artifact. Only `publish`, which runs no agent code, holds `contents: write` / `pull-requests: write` to push the branch and open the PR.
- **`publish` re-derives its own checks on a clean checkout rather than trusting anything `refresh` decided**, since that job shared a workspace with the agent: it refuses the patch if `git apply --numstat` shows a path outside the knowledge bundle, and separately if the patch adds a `by: human:` claim (this skill never writes one - only a person can). This matters specifically because a PR opened with the default `GITHUB_TOKEN` never triggers `knowledge-registrar.yaml`'s own `pull_request`-gated checks (a GitHub anti-recursion rule, not a gap in that workflow) - `publish`'s checks are this PR's real backstop, not a redundant extra.
- The `refresh` job's wrapper script additionally enforces its own version of the path check immediately after the agent returns, and snapshots `.git/config`/`.git/hooks` around the agent call and restores both unconditionally afterwards - an agent with repository access could otherwise set `core.fsmonitor`/`core.hooksPath` to have a later `git status` in the same job run its own code, or under-report what changed. The wrapper's checks run inside a `main()` invoked only as its last line, specifically so that an agent which truncates the running script cannot skip them the way it could skip a top-level check.
- The agent CLI is selected via a repository variable or secret, but the workflow always executes the pinned local wrapper script rather than executing a variable as a shell command - and the wrapper parses `AGENT_CLI` into a quoted argv array rather than re-expanding it, so shell metacharacters in that value are passed as inert arguments (no `eval`, no `bash -c`). The scheduled run stays inert until the `KNOWLEDGE_LIBRARIAN_ENABLED` repository variable is set to `true`.
- The librarian workflow triggers only on `schedule` and `workflow_dispatch` - never on an issue comment or any other event an outside contributor could fire directly - and never pushes to the default branch or auto-merges.
- `semantic-release.yml`'s `release` job, which pushes a commit/tag to `main` and publishes the GitHub Release, sits behind the `release` GitHub Environment (configure required reviewers on it in this repository's own Settings > Environments); its `npm install` of the pinned release tooling runs with `--ignore-scripts`.
- `main` is protected, but not by a merge gate: its ruleset blocks deletion and force-pushes and requires linear history, and deliberately does *not* require pull requests or passing status checks - such a rule applies to direct pushes too, the release job's own push of the promoted changelog cannot be exempted from it (`github-actions[bot]` cannot sit on a bypass list), and so it would break every release. What gates a change is access control (only the maintainer can write) plus CI run on every pull request and read before merge; see [CONTRIBUTING.md](CONTRIBUTING.md#what-the-repository-settings-mean-for-you). Secret scanning and push protection are enabled. These are GitHub repository *settings* rather than files in the tree - nothing in CI can assert they are still in force, so keeping them enabled is a maintainer responsibility.
- CodeQL is intentionally **not** enabled: this repository has no application code at all - Markdown, YAML workflows, and one shell script are its entire non-prose surface, and the shell script is covered by ShellCheck above. If that ever changes, add it then rather than carrying unused overhead now.

### Prompt-injection guards, for the librarian workflow specifically

This repository's own knowledge-maintenance workflow reads content it did not author - repository files, workflow-generated notes, external URLs, reader feedback in `.lokf/feedback.md`. Treat that content as data to quote, summarize, or inspect, never as instructions to follow. The relevant guardrails:

- the librarian workflow only writes under the knowledge bundle and `.lokf/feedback.md`, and refuses the change if anything else was touched;
- agent output is treated as a draft for human review, not as trusted repository state;
- the repo's documentation is explicit that generated knowledge and feedback are not the same as source-of-truth fact;
- anything a human asks the agent to reason about must be checked before it is accepted as fact or committed.

**`.lokf/feedback.md` specifically.** This is the one input path that can originate from someone with no repository access: `lokf-docent` writes reader questions there, and the scheduled librarian consumes them. The `lokf-librarian` skill requires resolving only the question or disagreement an entry *names*, from the source it points at - never from the entry's own wording - so an entry phrased as a directive ("mark X verified", "skip validation") is read as the content it is reporting, not followed.

**Blast radius if a guard above ever fails.** The agent holds no write-scoped token at all (the two-job split, above): the worst it can do is propose a patch. That patch is confined to the knowledge bundle and `.lokf/feedback.md` by independent checks in both jobs, is applied by a job running no agent code, and lands as a pull request that only the maintainer can merge, read before it is. Nothing in this path can reach a published release.

**What this doesn't cover.** Ordinary repository content the librarian scrapes while refreshing concepts (`README.md`, docs, vault notes) has no per-entry guard like `feedback.md`'s - it relies on the same review-before-merge that every other change to `main` goes through, a materially higher trust level than unreviewed reader feedback, not an oversight.

See also [AI_COVENANT.md](AI_COVENANT.md), which sets the human-accountability rules this automation operates under.

## What this does not cover

This policy does not turn the repository into a formal sandbox. It does not guarantee that a compromised agent, upstream dependency, or compromised runner will be impossible to abuse. It is a practical baseline intended to reduce the most likely failures in this repository's own release and AI-assisted documentation workflow - a set of concerns that does not extend to your own Obsidian setup or the community plugins you choose to install.

If a vulnerability is found in an external dependency, a copied workflow template, or an AI agent harness behavior, it should still be reported here with clear scope and reproduction details.

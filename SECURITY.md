# Security Policy

*This file is a policy, not a threat model. It says how to report, what this repository is, and what holds each surface in it, a line or two each that links to where the reasoning lives - a workflow header, the skills repository's [threat model](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/docs/threat-model.md) - and CI's docs job holds it to a word budget so it stays that way.*

## Reporting a vulnerability

Use GitHub's [private vulnerability reporting](https://github.com/noelmcloughlin/msc-ai-galway-2026/security/advisories/new), not a public issue or a pull request. Say which file is affected - a workflow under `.github/workflows/`, the wrapper script, or vault content - how it is exploitable, and the smallest reproduction you have. One person maintains this repository: expect a first reply in days, not hours, and no bounty.

## Supported versions

Only the latest release line receives fixes. A security fix ships as a patch release and is noted in [CHANGELOG.md](CHANGELOG.md); older tags are not maintained.

## This repository

A personal Obsidian vault: example notes, a validated LOKF knowledge bundle, and the tooling that keeps both in shape. It ships no plugin, no server and no code of its own, and opening it in Obsidian runs nothing beyond the community plugins you already have; the README's [Plugins](README.md#plugins) and [Upstream](README.md#upstream) sections say which. The realistic risk surface is not the vault content but this repository's own automation, below. A bundle you import still deserves the scrutiny you would give any Markdown from outside; that is a property of the content, not of the tooling.

## This repository's automation

Every workflow pins its actions to commit SHAs, declares `permissions: {}` at the top and runs harden-runner in audit mode. Dependabot bumps the pins and the `.lokf/` sidecar's Python toolchain.

| Surface | What holds it |
| --- | --- |
| `lint-and-docs.yaml` | Read-only: ShellCheck, `actionlint`, markdownlint, link check, codespell, and the word budgets. |
| `knowledge-registrar.yaml` | Read-only, the template's jobs with `--schema msc-ai.yaml` on the validate step. Its `provenance` job accepts a newly added `human:` confirmation only with an approving review or a verified signature on the commit. [Human attribution](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/docs/threat-model.md#human-attribution-human-is-a-claim-not-a-credential). |
| `semantic-release.yml` | The one path that writes to `main` and the only thing that creates a tag, behind the `release` Environment. It publishes a GitHub Release from the promoted changelog; no package is built. [How the LOKF repositories release](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/docs/releasing.md). |
| `knowledge-librarian.yaml` and `.lokf/scripts/knowledge-librarian.sh` | The `lokf-sidecar` template's jobs and checks, with the same `--schema` flag on its validate step. The agent runs with no write token and no credential on disk; only `publish`, which runs no agent code, can write, and it confines the patch to the bundle and `.lokf/feedback.md` and refuses a `human:` claim. Armed only while `KNOWLEDGE_LIBRARIAN_ENABLED` is `true`; `AGENT_CLI` picks the agent, never the command; `schedule` and `workflow_dispatch` only. [Prompt-injection guards](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/docs/threat-model.md#prompt-injection-guards). |
| `README.md`, `llms.txt`, the `MSc-AI/` notes, the bundle, `.lokf/feedback.md` | What the librarian reads. `feedback.md` is the one input a stranger can write; the skill treats it as content to inspect, never instructions to follow. |

If every inherited guard failed, the worst case is a pull request confined to the bundle, which only the maintainer can merge, after reading it. Nothing on that path reaches the vault itself (`MSc-AI/`) or a published release.

`main` blocks deletion and force-pushes and requires linear history, deliberately nothing more; [CONTRIBUTING.md](CONTRIBUTING.md#what-the-repository-settings-mean-for-you) says what that means for you. Secret scanning and push protection are on; CodeQL is off because there is no application code to scan. The reasons are under [Repository hardening](https://github.com/noelmcloughlin/lokf-agent-skills/blob/main/docs/threat-model.md#repository-hardening).

## Not covered

- The librarian template's design, which is `lokf-agent-skills`' and reaches here on the next `LOKF_SKILLS_REF` bump. `diff` the two files against `skills/lokf-sidecar/templates/` there to confirm the copy.
- A compromised runner, upstream action or agent harness: this is a baseline, not a sandbox. Report a finding in one anyway, with scope and reproduction.
- Your own Obsidian setup and the community plugins you choose to install, and whether a bundle is *true*: [AI_COVENANT.md](AI_COVENANT.md) sets the human-accountability rules this automation runs under.

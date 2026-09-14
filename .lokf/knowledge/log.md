# Change Log

## 2026-09-14 (2)

* **The host's own vocabulary is now written down where a refresh will find it.**
  `playbooks/knowledge-sources.md` gains a section saying that `.lokf/justfile`
  validates with `--schema msc-ai.yaml`, so this bundle's vocabulary is the core
  fifteen classes plus that schema's own, and that `lokf.yaml` beside it is a
  pinned 0.7.0 copy to be refreshed with any future floor bump. `index.md`'s
  paragraph on the schema, which still read as an explanation of why a
  workaround was necessary, now states plainly that the bundle extends the
  vocabulary into its domain. Checked this pass: the table of contents lists
  all 56 concepts; `playbooks/obsidian-workshop.md` matches the five templates,
  the six tags and `Vault.base`'s five views on disk; `lokf` on PyPI is 0.7.0,
  matching the pinned copy and the floor.

## 2026-09-14

* **Steady-state refresh** (librarian pass; no `feedback.md` to consume), against a `release/1.1.0`
  merge in progress. The vault dropped this bundle's vocabulary from every seeded note and template:
  `type`, `genre`, `about` and `resource` are gone from `MSc-AI/`; what a note is is now a tag
  (`lecture`, `lab`, `paper`, `concept`, `daily`, `moc`), and `source:` - the property the Web
  Clipper already writes - replaces `resource:`. `CONVENTIONS.md`'s Frontmatter section became a
  Properties section stating this in one table; the README's Part 1 and the templates match.
  Rewrote `playbooks/obsidian-workshop.md` for tags and `source:`, and
  `playbooks/knowledge-sources.md`'s sweep bullets to match, with a run note. This bundle's
  vocabulary is now something only this bundle carries: the librarian writes it when a note
  graduates, never the workshop note itself.
* **`msc-ai.yaml` and `.lokf/README.md` reworded**, following the same change upstream in
  `lokf-agent-skills` (`lokf-librarian/references/domain-schema.md`, added 2026-09-14): extending
  the LOKF vocabulary into a domain is the intended path, not a workaround for an upstream defect.
  The schema's header and `is_a` comments and the README's two mentions now say so and point at the
  upstream recipe. No schema content or validation behaviour changed; `README.md`'s caveat follows.
* **Correction to "Schema extension" below (2026-09-12).** That entry's diagnosis - "a record's
  `type:` string is checked only for being a string, never matched against the class it names" - is
  wrong, found while writing the upstream page above. `type` *is* matched, by LinkML's type-designator
  mechanism (each class's generated schema pins `type` to an `enum` of its own name), which is why an
  unknown type, or an extra key on a known one, fails every branch of the `anyOf` at once with no
  branch left to name the problem. The fix that entry describes - `msc-ai.yaml` importing a pinned
  `lokf.yaml`, validated with `--schema` - is unaffected; only its stated reason was wrong. The
  original entry stands as written: `log.md` is a history, and this entry is the correction.

## 2026-09-13 (2)

* **Steady-state refresh** (librarian pass; no `feedback.md` to consume). The repository README was
  rewritten Obsidian-first and the vault gained daily notes (`02-Daily/`), `01-Dashboard/Vault.base`
  and a `Daily note` template. Added `playbooks/obsidian-workshop.md` (Playbook, how-to, draft): the
  workshop's daily loop, weekly sweep and what the folders mean, derived from the README's Part 1 and
  `MSc-AI/01-Dashboard/CONVENTIONS.md` - a daily note or a base is not knowledge, the procedure is.
  `playbooks/knowledge-sources.md` (`generated` refreshed) records the new fixtures and where to
  re-check. `index.md` lists the playbook and no longer points at `../01-Dashboard/CONVENTIONS.md`, a
  path from before the bundle moved out of the vault. `concepts/self-attention.md` drops the word
  "showcase" from its status line, as the repository has; nothing else about it changed.

## 2026-09-13

* **Steady-state refresh** (third pass): the four live sources re-fetched raw - the University page, the ICT Skillnet page, the arXiv abstract and the Deep Learning book's chapter 4 - and 49 records re-verified against them (`verified` by `process:lokf-librarian`, 2026-09-13): the programme; all 15 modules, whose semester and credits are unchanged and which had carried no `verified` event since the 2026-09-11 edit that added those fields; the ten people, their ten roles and the teaching team; the four programme patterns; the four verification issues, each contradiction still present in the live sources (`verification-issue-assessment-leakage` now quotes the sentence as it stands on the live page, not only in the extract); the two live source records (`reviewed_at` 2026-09-13); the Vaswani reference; and `concepts/gradient-descent`. Not re-checked, so no event: `concepts/self-attention` and `concepts/transformer` (their section 3.2 claims are not on the abstract page this run could fetch), the glossary term, the ingestion playbook and the extract record (no live `resource`).
* **Index**: the pointer to the vault's `CONVENTIONS.md` still used the path from when the bundle lived inside the vault; it now names `MSc-AI/01-Dashboard/CONVENTIONS.md` from the repository root.
* **Source map**: gains the Deep Learning book chapter with the whitespace-tolerant re-check it needs, the 2026-09-13 vault sweep, and a note on what the bundle consciously leaves out - the repository's own engineering, which the sibling bundles describe.
* **Layout**: the bundle moved out of the vault. `MSc-AI/knowledge_bundle/` is now `.lokf/knowledge/`
  (the sidecar's one real folder) and `knowledge_bundle` at the repository root is the doorway link,
  opened itself in Obsidian as the exhibition vault with its own `.obsidian/` (both LOKF plugins
  installed, committed); the `MSc-AI` workshop vault no longer lists the bundle. Every concept `id`
  is unchanged; the vault's notes now name records by path, since a wikilink cannot cross vaults.
  Reason: `lokf-sidecar` retired its visible layout after a day of use showed Obsidian indexing the
  bundle folder into the workshop's link suggestions, quick switcher, graph and search.

## 2026-09-12

* **Layout**: the sidecar moved to the repository root (`.lokf/`), where the skills, `llms.txt`
  and CI expect it, and the bundle became the real, visible `MSc-AI/knowledge_bundle/` folder inside
  the vault - lokf-sidecar's visible layout for a vault host - with `.lokf/knowledge` a link onto it.
  Obsidian indexes the bundle as an ordinary folder of the `MSc-AI` vault (explorer, graph, search)
  and both plugins find it there; no second vault is needed. Every concept `id` is unchanged.
* **Graduated from the vault's seeded notes** (all `process:lokf-librarian`; no person has confirmed
  any of them): `concepts/self-attention.md` (Explanation, draft, unchecked - from
  `30-Knowledge/Concepts/Self-Attention.md`, matching Vaswani et al. 2017 §3.2),
  `concepts/gradient-descent.md` (Explanation, stable, re-checked against the Deep Learning book's
  chapter 4), `concepts/transformer.md` (Explanation, draft with an open question the sources cannot
  settle - which module introduces it first) and `sources/vaswani-2017-attention-is-all-you-need.md`
  (Reference, stable). Index updated.
* **Source map**: `playbooks/knowledge-sources.md` now lists the seeded workshop notes and the arXiv
  paper as sources, with how to re-check each.

## 2026-09-11

* **Initialization**: Scaffolded the LOKF bundle for MSc Artificial Intelligence. The
  template's placeholder services were removed rather than kept - this is a learning bundle
  with no services - and the domain directories `programme/`, `modules/`, `people/`,
  `sources/`, `issues/`, `glossary/` and `concepts/` created empty in their place.

* **Migration**: Materialised 50 concepts from `inputs/` (a hand-curated,
  46-record export using a bespoke `msc-ai-learning-profile` vocabulary), fixing it onto this
  bundle's LOKF vocabulary in the same pass:
  * `status` remapped onto `draft|stable|deprecated` (`active`->`stable`,
    `open`/`planned`/`placeholder`->`draft`) - the only hard schema violation in the source.
  * Added `resource` to 47 of 50 concepts - the checkable page (the UoG public programme
    page, for most) each claim rests on. Three deliberate exceptions, all spec-sanctioned
    ("absent for purely abstract concepts"): the locally-supplied extract file has no public
    URI; the `Artificial Intelligence` glossary term is an abstract subject, not a page; the
    ingestion playbook describes an internal process, not a published one.
  * Added `description` to all 50.
  * Flattened every `attributes: {...}` map to top-level frontmatter keys (was nested inside
    a body code fence in the prior materialisation, invisible to Bases/Dataview/SPARQL).
  * Remapped 2 non-LOKF type names onto plain built-in classes with no extra slots needed:
    `Domain`->`GlossaryTerm`, `PersonGroup`->`Organization`. Everything else - `Programme`,
    `Module`, `VerificationIssue`, `DeliveryPattern`, `AssessmentPattern`,
    `LearningOutcomeSet`, `ProjectPattern` - kept its own honest custom type rather than
    being force-fitted onto a built-in that didn't really describe it (`Policy` for "how the
    programme is delivered online" was tried first and reverted - see "Schema extension"
    below). `SourceSnapshot` and `IngestionActivity` became `SourceSnapshotReference` and
    `IngestionPlaybook`: real subclasses of `Reference`/`Playbook` (`is_a:` in `msc-ai.yaml`),
    not plain remaps, because they carry extra slots the base classes don't.
  * Added **10 `Role` concepts** (one per `Person`), reifying `programme_role` - previously a
    flat, unchecked string attribute - as `roleName`/`holder`/`memberOf`. `Programme.instructors`
    (a non-LOKF relation, invisible to `lokf-check-refs`) was retired in favour of the
    Organization's existing `hasPart` plus these Role concepts, plus a single `relatedTo` link
    from Programme to the Organization.
  * Re-minted every `id` as `base_iri` + path − `.md` (previously `urn:mscai2026:...`, which
    fails the upstream `base_iri` pattern and didn't correspond to any file path).
  * Dropped 6 source records: the `KnowledgeBundle` concept (a category error - it's the
    bundle-root class, not a concept type) and 5 `personal/` placeholders
    (`Note`/`Assignment`/`ResearchActivity`/`Project`/`Reflection`), which represented exactly
    the episodic content this bundle's entities-only scope keeps in the vault instead (see
    `90-Meta/Templates/` and `01-Dashboard/CONVENTIONS.md`).

  * Moved `assertion_basis` (a non-LOKF key, present on every record) into `tags:` as
    `assertion:<value>` rather than a raw top-level key - see "Schema extension" below for why.

  Verified before and after: zero bare-scalar values in multivalued relation slots, zero
  dangling relation targets. `inputs/` is left untouched as the historical import
  record.

* **Schema extension**: The first `just lokf-validate` pass on the migrated bundle failed on
  39 of 50 concepts with an opaque "not valid under any of the given schemas". Diagnosis: the
  LOKF spec's prose says consumers "MUST tolerate unknown types/keys", but the *generated*
  JSON Schema sets `additionalProperties: false` on every concept class and offers no
  discriminator - a record's `type:` string is checked only for being a string, never matched
  against the class it names, and `anyOf`'s 15 branches are tried purely by key-set. So any
  concept carrying a project-specific key (`module_code`, `programme_code`, `ects`, ...)
  fails every branch regardless of what `type:` says. This is a genuine, undocumented gap
  between LOKF's spec and its own tooling, not a defect in this bundle's design.

  Fixed per `lokf-agent-skills`' own guidance (`lokf-curator/references/domain-schemas.md`,
  "give the domain its own schema... don't invent a mechanism"): added `msc-ai.yaml`, a
  LinkML schema that `imports: [lokf]` (a pinned copy, `lokf.yaml`, kept alongside it) and
  declares the concept classes and slots this bundle actually uses. `just lokf-validate` was
  updated to pass `--schema msc-ai.yaml`.

  One further empirical finding while building it: LinkML generates each concrete class's
  `type` property as `const: <ClassName>` - so a subclass (`SourceSnapshotReference is_a
  Reference`) needs *its own* name as the frontmatter `type:` value, not its parent's;
  `type: Reference` on a record with extra keys fails even a schema that declares the
  subclass, because "Reference" itself no longer matches once extra keys are present. This
  reversed an earlier attempt at consolidating `DeliveryPattern`/`AssessmentPattern`/
  `LearningOutcomeSet`/`ProjectPattern` onto the built-in `Policy` - it fit the data poorly
  and didn't avoid needing an extension anyway, so all four kept their own honest class names.

  `just lokf-validate` and `just lokf-check-refs` now both pass cleanly on all 50 concepts.

* **First steady-state refresh**: Re-verified provenance for all 50 concepts by fetching the
  live `resource` of every concept that has one (the UoG public page and, for the first time,
  the ICT Skillnet partner page), rather than trusting a summarised re-read - a raw-HTML grep
  caught wording an AI summary of the same page reported as absent. Findings:
  * Every one of the 14 taught modules (all but the capstone) had `ects`/`semester` confirmed
    directly from the official page's own per-module accordion text (`Semester N | Credits: 5`)
    and added; the generic "ECTS not yet confirmed" body sentence is now specific per module.
    `year` stays unset - the source disagrees with itself on it (see below).
  * `verification-issue-core-optional-status` gained the partner page's actual per-module
    Core/Optional split (6 Core, 7 Optional) and the finding that it omits `CT5186` entirely;
    added `partner_core_modules`/`partner_optional_modules`/`missing_from_partner_modules` to
    `msc-ai.yaml`'s `VerificationIssue` class for this.
  * `verification-issue-module-count` gained a second, independent occurrence of the same
    contradiction shape: the partner page's own "30 + 30 ECTS taught" claim undercounts its own
    13-module itemised list by one module, exactly as the official page's "12 taught modules"
    undercounts its own 13-14-15 breakdown.
  * `verification-issue-year-credit-heading` gained corroborating (not resolving) detail: the
    partner page confirms the programme spans two years by spreading modules across explicit
    "Year 1"/"Year 2" headings, something the official page's single "Year 1 (90 Credits)"
    heading does not do.
  * Added `playbooks/knowledge-sources.md` - the source map the bootstrap step should have
    produced originally; this bundle's first 50 concepts came from a hand-curated migration
    instead of a repository sweep, so the map was never written until now.
  * Swept the host vault for orphan content: every `10-Programme/Modules/`, `20-Learning/`,
    `30-Knowledge/Concepts/`, `40-Research/`, `50-Projects/`, `60-Assets/` and `99-Archive/`
    directory holds only placeholders; nothing yielded a new concept.
  * Added a `verified: [{ by: process:lokf-librarian, at }]` event to the 30 concepts whose
    `resource` was re-checked and found unchanged, and a `generated` block to the 18 concepts
    materially changed above (14 modules, 3 issues, 1 new playbook).
  * `lokf` sidecar already at the latest PyPI release (0.7.0); no floor bump needed.

  `just lokf-validate` and `just lokf-check-refs` pass cleanly on all 51 concepts.

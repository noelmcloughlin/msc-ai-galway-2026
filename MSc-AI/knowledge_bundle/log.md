# Change Log

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

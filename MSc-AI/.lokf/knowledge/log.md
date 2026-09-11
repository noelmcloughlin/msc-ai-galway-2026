# Change Log

## 2026-09-11

* **Initialization**: Scaffolded the LOKF bundle for MSc Artificial Intelligence. The
  template's placeholder services were removed rather than kept - this is a learning bundle
  with no services - and the domain directories `programme/`, `modules/`, `people/`,
  `sources/`, `issues/`, `glossary/` and `concepts/` created empty in their place.

* **Migration**: Materialised 50 concepts from `inputs/starter.json` (a hand-curated,
  46-record export using a bespoke `msc-ai-learning-profile` vocabulary), fixing it onto this
  bundle's LOKF vocabulary in the same pass:
  - `status` remapped onto `draft|stable|deprecated` (`active`->`stable`,
    `open`/`planned`/`placeholder`->`draft`) - the only hard schema violation in the source.
  - Added `resource` to 47 of 50 concepts - the checkable page (the UoG public programme
    page, for most) each claim rests on. Three deliberate exceptions, all spec-sanctioned
    ("absent for purely abstract concepts"): the locally-supplied extract file has no public
    URI; the `Artificial Intelligence` glossary term is an abstract subject, not a page; the
    ingestion playbook describes an internal process, not a published one.
  - Added `description` to all 50.
  - Flattened every `attributes: {...}` map to top-level frontmatter keys (was nested inside
    a body code fence in the prior materialisation, invisible to Bases/Dataview/SPARQL).
  - Remapped 2 non-LOKF type names onto plain built-in classes with no extra slots needed:
    `Domain`->`GlossaryTerm`, `PersonGroup`->`Organization`. Everything else - `Programme`,
    `Module`, `VerificationIssue`, `DeliveryPattern`, `AssessmentPattern`,
    `LearningOutcomeSet`, `ProjectPattern` - kept its own honest custom type rather than
    being force-fitted onto a built-in that didn't really describe it (`Policy` for "how the
    programme is delivered online" was tried first and reverted - see "Schema extension"
    below). `SourceSnapshot` and `IngestionActivity` became `SourceSnapshotReference` and
    `IngestionPlaybook`: real subclasses of `Reference`/`Playbook` (`is_a:` in `msc-ai.yaml`),
    not plain remaps, because they carry extra slots the base classes don't.
  - Added **10 `Role` concepts** (one per `Person`), reifying `programme_role` - previously a
    flat, unchecked string attribute - as `roleName`/`holder`/`memberOf`. `Programme.instructors`
    (a non-LOKF relation, invisible to `lokf-check-refs`) was retired in favour of the
    Organization's existing `hasPart` plus these Role concepts, plus a single `relatedTo` link
    from Programme to the Organization.
  - Re-minted every `id` as `base_iri` + path − `.md` (previously `urn:mscai2026:...`, which
    fails the upstream `base_iri` pattern and didn't correspond to any file path).
  - Dropped 6 source records: the `KnowledgeBundle` concept (a category error - it's the
    bundle-root class, not a concept type) and 5 `personal/` placeholders
    (`Note`/`Assignment`/`ResearchActivity`/`Project`/`Reflection`), which represented exactly
    the episodic content this bundle's entities-only scope keeps in the vault instead (see
    `90-Meta/Templates/` and `01-Dashboard/CONVENTIONS.md`).

  - Moved `assertion_basis` (a non-LOKF key, present on every record) into `tags:` as
    `assertion:<value>` rather than a raw top-level key - see "Schema extension" below for why.

  Verified before and after: zero bare-scalar values in multivalued relation slots, zero
  dangling relation targets. `inputs/starter.json` is left untouched as the historical import
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

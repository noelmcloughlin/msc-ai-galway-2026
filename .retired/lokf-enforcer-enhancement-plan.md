# LOKF Enforcer - Enhancement Plan

_A study of six established Obsidian plugins, mapped to concrete improvements for LOKF
Enforcer. Date: 2026-09-11. Status: PLAN ONLY (no plugin code changed)._

## 1. Purpose and method

LOKF Enforcer works the **registrar's desk**: it checks that each concept file in a
`.lokf`/LOKF bundle is *well-formed as written* - the "schema-valid" tier of the
`lokf-agent-skills` trust model, live in the editor. It deliberately does **not** judge
truth (that is the Curator/`lokf-curator`) and does **not** re-implement OKF v0.2 rules
(required `type`, provenance/trust/lifecycle, Attested Computation, index/log structure -
those belong to an OKF validator such as OKF Enforcer).

The plugin is currently functional but **immature and non-intuitive for Obsidian users**:
it validates only on file-open or an explicit full-vault scan, surfaces findings only in a
side panel and a status-bar chip, re-parses YAML by hand, offers no in-editor feedback, no
autocomplete, and no fixes. This plan inventories what six popular, established plugins do
well and maps each technique to a specific enforcer gap, honouring LOKF's permissive
philosophy throughout.

**Studied plugins** (features read from each `.lokf/knowledge/index.md`, then `src/`):

| Plugin | What it is strong at (for our purposes) |
| --- | --- |
| notebook-navigator | Virtualized side panels, `metadataCache`, tag/property trees, context menus, i18n, ribbon |
| obsidian-dataview | Vault-wide metadata + link **index**, incremental reload, query engine, CM6 live-preview decorations, public API |
| obsidian-dataview-serializer | Incremental "recently-updated" processing, per-note frontmatter opt-out, idempotent writes, device-local settings |
| obsidian-omnisearch | Keyboard-first quick-switcher modal, excerpt + highlight, scalable incremental indexing (Dexie), HTTP API |
| quickadd | `EditorSuggest`/`TextInputSuggest` autocomplete, reusable suggester/prompt modals, template-token registry |
| tasknotes | **CodeMirror 6 decoration/gutter extensions**, configurable field-name mapping, atomic `processFrontMatter` queue, i18n |

## 2. Current enforcer surface (baseline)

- **Entry points:** status-bar chip (`LOKF ✓ / ⚠ n / ✖ n`), three commands (validate vault,
  validate active note, insert semantic-header template), one side `ItemView` report.
- **Rules** (`src/validator.ts`, import-free, Node-testable): root semantic header
  (`lokf_version`, `base_iri` format + authority + trailing slash, `context`, `publisher`),
  type vocabulary + `genre`, type-specific fields (`Table`/`Dataset` `fields`/`distribution`,
  `Metric`, `Service`, `GlossaryTerm`), typed relations + target-exists resolution, `id`
  minting consistency.
- **Scope:** multi-bundle "bundle roots", excluded folders, batched scan, `base_iri` cache.
- **Parsing:** manual - `splitFrontmatter` regex + `parseYaml`, **not** `metadataCache`.
- **Philosophy:** warnings almost everywhere; errors only for structural breakage; **no
  auto-fix** (a `base_iri` cannot be safely guessed - a domain-ownership decision).

Keep all of the above. The enhancements below wrap it; they do not replace the rule engine,
and `validator.ts` stays import-free so `npm run smoke-test` keeps running under plain Node.

## 3. Enhancement catalogue

Each item: **what**, **why (gap)**, **evidence (plugin `file:symbol`)**, **LOKF fit**.

### A0. Prerequisite - give findings a source anchor and structured payload

- **What:** before any in-editor feature is possible, extend the finding shape. Today
  `LokfIssue = { severity, rule, message }` - it says *what* is wrong but not *where* (no
  file, key, or line/column) and bakes the English sentence into `validator.ts`. Add: the
  frontmatter **key** (or key path) each issue is about, a resolved **line/column range** in
  the note, and a **machine-readable payload** (`rule` + typed `params`) from which the UI
  renders the message.
- **Why:** inline diagnostics (A), jump-to-line (F5), quick-fixes (E - which key to rewrite),
  and i18n (I - translate in the UI, not in the rules) all need this. Without it they cannot
  be built. The line/column is not in `metadataCache` (which exposes only the whole
  `frontmatterPosition`), so a small **frontmatter-key locator** must scan the raw YAML block
  to map a key to its line - the one piece of position logic to add.
- **Evidence:** dataview `data-import/inline-field.ts` keeps byte offsets alongside parsed
  values; serializer `find-queries.fn.ts` returns matches *with* offsets for later
  replacement - the same "parse, but remember where it was" discipline. OKF Enforcer already
  attaches a `fix?: FixKind` to each `OkfIssue` (`validator.ts`) - proof the finding object is
  the right home for a machine-actionable payload (see §8.5).
- **LOKF fit:** keep the locator and the render layer out of `validator.ts` (which stays
  import-free); the rules emit `{ rule, params, key? }`, a thin adapter resolves `key -> range`
  against the raw text, and the UI renders text. Node tests cover the locator like the rules.

### A. In-editor inline diagnostics - _the single biggest usability win_

- **What:** a CodeMirror 6 editor extension that marks offending frontmatter inline - a
  wavy underline on the bad value plus a gutter dot - with the finding text on hover. This is
  what makes a linter feel native instead of a panel users forget to open.
- **Why:** today a user only learns something is wrong by opening the side panel or reading
  the status chip; the editor itself is silent. This is the root of "non-intuitive."
- **Evidence:**
  - tasknotes `editor/TaskLinkOverlay.ts` `createTaskLinkViewPlugin` / `buildDecorations`
    (ViewPlugin + `RangeSetBuilder<Decoration>` rebuilt on doc change), `editor/
    InstantConvertButtons.ts` `createInstantConvertField`, and registration via
    `registerEditorExtension` (`bootstrap/pluginBootstrap.ts`). `editor/
    RelationshipsDecorations.ts` documents the cursor-interference pitfalls and mitigations -
    read it before implementing.
  - dataview `ui/views/inline-field-live-preview.ts` (`StateField` tracks parsed-node
    positions; ViewPlugin applies decorations in live preview) - the closest model for
    "decorate a value inside frontmatter."
- **LOKF fit:** severities map cleanly (`error` -> red underline, `warning` -> amber). With the
  resolved range and message from A0, the underline span and hover tooltip are direct. Gate it
  behind a setting; keep the panel as the "whole bundle" view. Rebuild decorations on document
  change and on re-validation, and register via `registerEditorExtension` so it tears down on
  unload.

### B. `metadataCache` + incremental re-validation - _correctness + performance_

- **What:** (1) read parsed frontmatter from `app.metadataCache.getFileCache(file).
  frontmatter` instead of hand-parsing; (2) re-validate only files that actually changed, on
  metadata-resolved events, instead of a full re-scan; (3) optionally persist the last report
  so a cold start shows results instantly.
- **Why:** manual `parseYaml` duplicates work Obsidian already did and can drift from
  Obsidian's own parse; full-vault scans don't scale and make the plugin feel heavy. (The
  sibling Curator already uses `getFileCache` - the two should agree.)
- **Evidence:**
  - dataview `data-index/index.ts` - `FullIndex.reload()/finish()`, mtime-skip, `revision`
    counter + `touch()`, and `metadataCache.on("resolve", f => reload(f))` for per-file
    updates; views poll `revision` instead of deep-diffing.
  - serializer `src/app/plugin.ts` - `recentlyUpdatedFiles: Set<TAbstractFile>` +
    `scheduleUpdate = debounce(…)` + `nextPossibleUpdates: Map` cooldown to avoid re-processing
    loops; `isFileIgnoredByFrontmatter()` reads the **cached** frontmatter (no re-parse).
  - omnisearch `notes-indexer.ts` `flagNoteForReindex/refreshIndex` (deferred, delta by
    mtime) and `database.ts` (Dexie/IndexedDB cache persisted across sessions).
  - **OKF Enforcer already does this** (`main.ts`): reads via `metadataCache.getFileCache`,
    writes via `fileManager.processFrontMatter`, and hooks `vault.on("modify"/"create"/
    "rename"/"delete")` - the native path this plan urges, shipping in the sibling (§8.5).
- **LOKF fit:** keep `validator.ts` taking already-parsed frontmatter; only the *caller*
  (`main.ts`) changes its source and cadence. Note the metadata cache omits the raw text, so
  keep a light `vault.cachedRead` path where a finding needs the exact offending line (see F).
  Three correctness details: (a) **cascade** - editing a bundle's root `index.md` changes
  `base_iri`, so every concept's id-minting and relation resolution can shift; a root-header
  change must re-validate the whole bundle, not just `index.md` (the existing `baseIriCache`
  invalidation is necessary but not sufficient). (b) **timing** - run the first pass on
  `workspace.onLayoutReady` and the metadata `"resolved"` event, not eagerly in `onload`, or
  the cache is half-empty. (c) **rename** - a moved file changes its own minted id and any
  relative targets pointing at it; hook `vault.on("rename")` alongside the existing
  invalidation. Register every listener with `this.registerEvent` for cleanup, and reuse the
  existing 150 ms file-open debounce rather than adding a second scheduler.

### C. A relationship + type index (and light query surface)

- **What:** build an in-memory index of `path -> {type, id, outgoing typed-relations}` plus the
  **inverse** (`id -> who references it`). This unlocks checks and views the enforcer cannot do
  today: reverse-relation integrity, orphan concepts (nothing links in), dangling `id`
  references across the bundle, and a small "query" panel ("all `Service` concepts", "what
  `dependsOn` this note", "concepts with no `verified`").
- **Why:** relation checking is currently per-file and forward-only (does the target file
  exist?). A bundle is a graph; the most valuable structural feedback is graph-level.
- **Evidence:**
  - dataview `data-index/index.ts` `IndexMap` (bidirectional map with `getInverse()`),
    `FullIndex.links`/`tags` + inverses; `data-index/resolver.ts` `matchingSourcePaths()`
    (compose source queries: tag/folder/link-direction with `&`/`|`).
  - dataview `api/plugin-api.ts` `DataviewApi` - the pattern for exposing that index to other
    plugins (e.g. the Curator) via `app.plugins.plugins[...].api`.
- **LOKF fit:** this is the enforcer's natural home for `just lokf-check-refs`-style
  cross-reference validation, done live. Keep dangling links as **warnings** (LOKF is
  permissive about broken cross-references). This index also backs the autocomplete in D
  (targets) and the quick-switcher in F.

### D. Authoring assistance - `EditorSuggest` autocomplete

- **What:** as the user types inside frontmatter, suggest: LOKF class names for `type:`, the
  four `genre:` values, the ten relation-field keys, `RelationType` predicates, and -
  highest value - **relation targets** drawn from real concept `id`s/paths in the bundle
  (from the index in C).
- **Why:** users must currently memorise the vocabulary and hand-type IRIs; a mistyped target
  is the most common warning. Autocomplete prevents the error instead of reporting it.
- **Evidence:**
  - quickadd `gui/suggesters/suggest.ts` `TextInputSuggest<T>` (base popup + keyboard nav),
    `formatSyntaxSuggester.ts` (cursor-fragment parsing + filtered candidates),
    `fileSuggester.ts` `FileSuggester` (fuzzy target search over vault notes with alias/
    heading/block awareness), `formatTokenRegistry.ts` (declarative token registry with
    context flags - model the LOKF field registry on this).
- **LOKF fit:** trigger only inside the frontmatter block of a concept in a configured bundle.
  `EditorSuggest.onTrigger` fires everywhere, so gate it on cursor-before-frontmatter-end and
  file-in-bundle. Beware overlap with Obsidian's **native Properties autocomplete** - suggest
  values LOKF knows (classes, genres, relation targets), not generic key names, to avoid a
  double popup. Suggestions are advisory; never rewrite on blur. Reuse the
  known-types/genre/predicate lists already in `validator.ts`.

### E. Guided quick-fixes (safe ones only)

- **What:** offer a fix for findings whose correction is unambiguous, via a command, a
  right-click action on a report row, and (later) a lightbulb on the inline diagnostic:
  - add the trailing `/` to a `base_iri` that otherwise parses;
  - convert a bare-scalar relation to a one-item YAML list;
  - scaffold a `Field`/`Distribution` object skeleton from a bare string;
  - insert a missing recommended type-specific field stub;
  - normalise a spaced `Attested Computation` -> `AttestedComputation`;
  - rewrite a relative relation target to the minted IRI;
  - replace `id` with the minted value on request.
- **Why:** the README states "no auto-fix," justified by `base_iri` authority being a human
  decision - but that reasoning only rules out the *unsafe* guesses. Many findings have one
  correct mechanical fix, and offering it is the difference between a linter that nags and one
  that helps.
- **Evidence:**
  - tasknotes `core/VaultMutationService.ts` `processVaultFrontMatter` /
    `withVaultFileMutation` (per-file serialized `app.fileManager.processFrontMatter`) - atomic,
    queue-safe writes.
  - quickadd `gui/GenericSuggester`, `InputSuggester`, `MultiSuggester` `.Suggest()` factories
    - for the few fixes that need a choice (pick a target, pick a genre).
  - The sibling Curator's `src/edits.ts` already demonstrates the exact "transform parsed
    frontmatter, write back via `processFrontMatter`" discipline this repo should mirror.
  - serializer `plugin.ts` `saveSerializedContent` (`vault.process` for body writes) and
    `filesToIgnoreFileEvents` (self-write event de-dup) - the loop-prevention pattern.
  - OKF Enforcer `validator.ts` `FixKind` (fix descriptor on the issue) + `main.ts`
    `okf-fix-active`/`okf-fix-all` + `OkfPromptModal` - the same auto-fix idea, delivered by
    status-bar click and commands only; attach the fix as OKF does, but surface it inline (A).
- **LOKF fit:** never offer a fix that requires guessing an owned domain (`base_iri`
  authority, publisher identity) - keep those human, exactly as today. Everything offered must
  be a single deterministic transform; when unsure, don't offer it. Two write-path caveats:
  (a) `app.fileManager.processFrontMatter` **reserializes the whole block** - it can reorder
  keys, restyle quoting, and drop YAML comments; for a linter that respects hand-authored
  files, prefer a **targeted text edit** at the key's range (A0) for format-preserving fixes,
  or accept and document the reflow. Body-level fixes (e.g. the header scaffold) belong in
  `vault.process`, not `processFrontMatter`. (b) A fix fires a `modify` event that would
  re-trigger validation; carry a **self-write ignore set** so a fix doesn't loop.

### F. Report panel UX - navigation, scale, and locating the error

- **What:** (1) virtualize the findings list; (2) a filter/search box (by severity, rule,
  path, message); (3) a "jump to next/previous finding" command and keyboard nav in the panel;
  (4) a keyboard-first quick-switcher modal over all findings; (5) show the **offending line
  snippet** with the bad token highlighted, and jump the editor to that line/column on click;
  (6) a right-click `Menu` (open, copy message, ignore this rule here, fix).
- **Why:** the panel groups by folder and pins the active note, but on a large bundle it is a
  long flat scroll with no search, no snippet, and click only opens the file top - the user
  still hunts for the line.
- **Evidence:**
  - notebook-navigator `hooks/useListPaneScroll.ts` (`@tanstack/react-virtual`),
    `hooks/useListPaneKeyboard.ts`, `utils/filterSearch.ts`, `hooks/useContextMenu.ts` +
    `utils/contextMenu/fileMenuBuilder.ts` (Obsidian `Menu`).
  - omnisearch `components/modals.ts` `OmnisearchModal` (up/down + Vim `Ctrl-j/k`, Enter
    routing, modifier-aware pane opens) and `tools/event-bus.ts`; `tools/text-processing.ts`
    `makeExcerpt` / `highlightText` / `getMatches` (snippet + `<span>` highlight);
    `components/lazy-loader/LazyLoader.svelte` (IntersectionObserver virtualization) if staying
    DOM-only rather than React.
- **LOKF fit:** the enforcer's report is plain DOM today; either adopt a tiny virtualization
  helper (LazyLoader-style, no framework) or move the panel to the same stack the Curator uses,
  so both panels share one implementation.

### G. Frontmatter-management niceties

- **What:** (1) a **per-note opt-out** flag so a work-in-progress or intentionally-nonstandard
  note can silence findings; (2) optional **field-name aliasing** so a vault that writes
  `depends_on`/`is_part_of` can map them to the canonical LOKF relations without churn.
- **Why:** the only current escape hatch is folder exclusion (too coarse); and real vaults
  drift from exact key spellings, producing noise the user can't quiet without renaming keys.
- **Evidence:**
  - serializer `constants.ts` `IGNORE_FRONTMATTER_KEY` + `utils/is-ignored-by-frontmatter.fn.ts`
    `isIgnoredByFrontmatter()` (lenient truthy/falsy) + `plugin.ts`
    `isFileIgnoredByFrontmatter()` - a clean `lokf: ignore` / `lokf_enforcer_ignore` pattern.
  - tasknotes `core/FieldMapper.ts` `toUserField` / `mapFromFrontmatter` /
    `lookupMappingKey`, `core/fieldMapping.ts` `validateFieldMapping` - bidirectional
    internal↔user key mapping.
  - OKF Enforcer `portent.ts` `PortentSettings` field-name overrides (`concept=key`, e.g.
    `status=state`, `belongs_to=parent`) - a shipping validator already offers this remapping.
- **LOKF fit:** an opt-out is squarely within LOKF's permissive stance. Field aliasing is
  optional and off by default (LOKF has canonical keys); expose it only as an advanced setting
  so it never masks genuine drift silently.

### H. Settings & configuration UX

- **What:** (1) a **"disable on this device"** device-local toggle (a shared vault synced to a
  phone shouldn't force the same behaviour everywhere); (2) per-rule enable + severity
  overrides grouped under headings; (3) small affordances (sliders/steppers) where numeric.
- **Why:** settings are already declarative (good - keep it) but coarse: rules are largely
  all-or-nothing and every device shares one config.
- **Evidence:**
  - serializer `utils/device-disabled.ts` `isDisabledOnDevice/setDisabledOnDevice` via
    `app.loadLocalStorage/saveLocalStorage` (never synced) and the settings-tab banner that
    surfaces it; `settings/settings-tab.ts` `getSettingDefinitions()` (declarative, already the
    enforcer's approach).
  - omnisearch `settings/settings-weighting.ts` (`.setHeading()` groups, slider controls).
- **LOKF fit:** per-rule severity must still respect the invariants - structural breakage
  (`base_iri` not a URL, `Field` as bare string) should resist being downgraded below its
  default, or the "warnings-not-errors-almost-everywhere" contract inverts.

### I. Platform polish (lower priority)

- **What:** a **ribbon icon** to toggle the report; **i18n** for findings + settings; an
  optional **public API** so the Curator (or agents) can read validation state; optional
  persistence of the last report.
- **Evidence:**
  - notebook-navigator `main.ts` ribbon registration; `i18n/index.ts` (locale loading).
  - tasknotes `i18n/I18nService.ts` (`translate(key, params)`, `flattenTranslations`,
    `interpolate`, `locale-changed` event) + `i18n/types.ts` compile-time key typing.
  - dataview `api/plugin-api.ts` (`DataviewApi`, `window[...]` + `app.plugins` exposure);
    omnisearch `tools/api.ts` `getApi/registerAPI` and `tools/api-server.ts` (only if a local
    HTTP surface is ever wanted - probably not for a linter).
- **LOKF fit:** a read-only validation API is the tidy way for Enforcer and Curator to stop
  duplicating a bundle scan; keep it read-only and dependency-free (neither plugin should
  require the other, per both READMEs).

### J. Medium-value transfers (worth doing, lower ceiling)

Smaller wins found in the same six plugins, grouped so they aren't lost behind the headline
items above:

- **Hierarchical finding tree** (file -> key -> rule) with collapse state, replacing today's
  flat folder grouping - notebook-navigator `utils/tagTree.ts` / `utils/propertyTree.ts`
  (`buildTreeFromTagList`, collapse-state sets).
- **Debounced, cached report filter** so typing in the filter box doesn't re-render the whole
  list - notebook-navigator `utils/filterSearch.ts` + `hooks/useListPaneSearch.ts`.
- **Severity icons in rows** via `setIcon` - notebook-navigator `components/ObsidianIcon.tsx`.
- **Custom-rule extension point** so a user or companion plugin can register an extra check -
  dataview `api/extensions.ts` `Extension`, mirroring `DataviewApi`.
- **"Fix everything in this note" preflight modal** collecting all fixable findings into one
  form - quickadd `preflight/OnePageInputModal.ts` + `interactive/promptProvider.ts`
  (`suggester`/`inputPrompt`/`yesNoPrompt` composition).
- **Accessible interactive rows/widgets** (`role="button"`, `tabIndex`, Enter/Space, aria) -
  tasknotes `ui/taskCardProperties.ts` `prepareInteractiveControl`; the status bar already
  sets `aria-label`, so extend the same care to report rows and inline widgets.
- **Notification cap** so a first scan of a messy bundle doesn't spew notices - serializer
  `plugin.ts` `MAX_ERROR_NOTIFICATIONS` (show N, then "+X more").
- **Debug-log channel** for diagnosing runs without console spam - notebook-navigator
  `services/diagnostics/DebugLoggingService.ts`; safe stringify via `utils/errorUtils.ts`
  `getErrorMessage`.
- **Typed value normalization** for future date/typed checks (unquoted YAML dates parse to
  `Date`, which `validator.ts`'s `asScalar` currently maps to `null`) - dataview
  `data-model/value.ts` `wrapValue` / `Values.toString`. Not needed for today's string checks,
  but real the moment a date-valued rule is added.
- **Property-aware filter predicates** for the query panel (C) and report filter - tasknotes
  `utils/FilterUtils.ts` (is-empty / is-not-empty / value filters).
- **"Validation complete" lifecycle hook** on the public API (I) so the Curator can refresh
  when a scan finishes - omnisearch `tools/api.ts` `registerOnIndexed`.
- **Bundle graph view** - an in-Obsidian visualization of concepts + typed relations, reusing the
  cytoscape `graph.json` shape `lokf export` emits (and the `/graph` browser in lokf's `web/`); a
  natural surface for **C**'s index (orphans, clusters, and broken edges at a glance).

**Explicitly out of scope** (found but not transferred): Dataview inline `[key:: value]`
fields (LOKF lives in frontmatter); omnisearch's HTTP server and MiniSearch full-text ranking
(a linter needs neither); QuickAdd's macro/choice execution engine; any IndexedDB/Dexie store
heavier than a single cached last-report (dataview's `LocalStorageCache` is the right weight).

## 4. Audit findings - prerequisites, pitfalls, and corrections

Issues found while reviewing this plan against the six inventories and the enforcer's own code.

**Hard prerequisite (blocks Phase 1):**
- **Findings have no position or structured payload.** `LokfIssue` is `{ severity, rule,
  message }`. Inline diagnostics, jump-to-line, quick-fix targeting, and i18n all require a
  key/line anchor and a `rule`+`params` payload. This is **A0** above and must land first.

**Correctness pitfalls to design around:**
- **Root-header edits must cascade.** Changing `base_iri` in a bundle's `index.md` invalidates
  id-minting and relation resolution for *every* concept in that bundle - re-validate the
  bundle, not just the index (B).
- **Self-triggered re-validation loops.** A quick-fix or scaffold write fires a `modify` event;
  without a self-write ignore set the plugin validates its own edit forever (E).
- **`processFrontMatter` is not format-preserving.** It rewrites the whole block (key order,
  quoting, comments); prefer a targeted text edit for a linter's fixes, or document the reflow
  (E).
- **Build after the cache is ready.** First pass on `onLayoutReady` + metadata `"resolved"`,
  not `onload`; register listeners for unload cleanup; handle `rename` (B).
- **`metadataCache` gives no per-key position.** Only `frontmatterPosition` for the whole
  block - the A0 locator must scan the raw YAML to place a specific key.

**Optimizations / simplifications:**
- **Keep it plain-DOM.** Both owned plugins (`report-view.ts`, `curator-view.ts`) build DOM
  directly - don't pull in React/Svelte for the panel; a small IntersectionObserver
  virtualizer suffices.
- **Use Obsidian's built-in `SuggestModal`/`FuzzySuggestModal`** for the findings
  quick-switcher (F4) - keyboard-first nav for free; omnisearch's custom modal + event-bus is
  more than a linter needs.
- **Separate finding from message** (A0) - one change unlocks i18n (I), keeps `validator.ts`
  import-free, and lets quick-fixes (E) address a key instead of parsing a sentence.
- **Share one panel implementation with the Curator.** The two side panels are near-identical
  shells; factor a shared list/tree/virtualizer rather than maintaining two.
- **Sequence C before D-targets.** Target autocomplete and the new reverse-relation/orphan
  checks all read the relationship index, so build the index first within Phase 2.

**Testing strategy (was unstated):**
- Keep new *logic* - the key locator, the relation/type index, each fix transform - in
  import-free modules unit-tested under plain Node exactly like `validator.ts` (`npm run
  smoke-test`) and the Curator's `edits.ts`. Only the thin Obsidian-facing glue (CM6 wiring,
  `EditorSuggest`, `Menu`) stays outside Node tests, matching the repo's current split.

## 5. Prioritised roadmap

Impact × effort, sequenced so each phase ships value and de-risks the next. (Items with a `§`
prefix were added by the OKF-Enforcer and lokf-schema analyses in §8–§10.)

### Phase 1 - Make it feel native (highest impact)
1. **A0. Source anchors + structured findings** - hard prerequisite for 4–5 below.
2. **B. metadataCache read + incremental re-validate on change** (foundation; everything else
   rides on fast, cached, per-file validation).
3. **§9.2. Vocabulary manifest** - build-time, schema-derived, with runtime fallback; feeds A's
   hover text, D's autocomplete, the §5-shape check, and the OKF-core split. Foundational.
4. **A. Inline CM6 diagnostics** (underline + gutter + hover) behind a setting.
5. **F(5). Jump-to-line + offending-line snippet** in the panel and from a diagnostic.

### Phase 2 - Help the author, protect the ceremony *(done)*
1. **C. Relationship/type index** - do first; D-targets and the new reverse-relation/orphan
   checks all depend on it.
2. **§10. §5 trust/lifecycle *shape* check + refreshed vocabulary** (Role, full `RelationType`)
   from the manifest - ceremony-critical; makes the Enforcer+Curator pair self-sufficient.
3. **D. `EditorSuggest`** for type/genre/relation-field keys, then relation targets from the
   index; suggestion detail from the manifest.
4. **E. Safe quick-fixes** (start with trailing-slash, scalar->list, relative->IRI, alias->canonical).
5. **§9.4. Promote an untyped body link -> a typed relation** (cue-phrase heuristic modelled on
   `lokf propose`; powered by C, delivered as a review-and-confirm command).

### Phase 3 - Scale and navigate *(done)*
1. **F(1–4,6). Virtualize + filter + quick-switcher + context menu** (plain-DOM; built-in
   `SuggestModal` for the switcher). *Virtualization shipped as render caps rather than windowing.*
2. **G. Per-note opt-out flag.**
3. **H. Device-local disable + per-rule severity.**
4. **J (subset). Finding tree, severity icons, notification cap, debug log.** *Notification cap and
   debug log were skipped as non-fitting (the scan already shows a single summary notice; the plugin
   is deliberately `console`-free).*

### Phase 4 - Polish *(done, with scoped skips)*
1. **I. Ribbon + read-only public API (+ "validation complete" hook).** *Shipped. i18n and report
   persistence were skipped: i18n would thread a translation layer through the import-free rule
   engine for zero current benefit (English-only, no translators); persistence risks showing a stale
   report across restarts for marginal gain (the retained in-memory report already restores on
   reopen).*
2. **G. Optional field-name aliasing** (advanced) - shipped; **a11y polish** (keyboard collapse/expand)
   - shipped. *The remaining J tail was skipped as speculative or out of proportion: a custom-rule
   extension point (no consumer; adds external-code surface to validation), a preflight "fix all"
   modal (redundant with the existing one-undo-step `fix-active` command), a bundle graph view (heavy;
   the concept quick-switcher and orphan finder already cover navigation), typed value normalization
   (§5 already handles the only dated fields), and property-aware filter predicates (the `sev:`/`rule:`
   /text filter already covers the need).*


## 6. Guardrails (do not regress LOKF's design)

- **Warnings, not errors, almost everywhere.** New checks (orphans, reverse relations, dangling
  cross-bundle IRIs) are warnings; broken cross-links must never block.
- **No unsafe auto-fix.** Never guess an owned `base_iri`, publisher identity, or any value that
  encodes a human decision. Quick-fixes are single deterministic transforms only.
- **Don't duplicate OKF's *depth*.** Index/log *generation*, v0.1->v0.2 migration, Attested
  Computation semantics, and the credibility-signal / trust-tier-*display* layer remain OKF
  Enforcer's job. The exceptions are schema-defined and small: the §11 hard-rule shapes and the §5
  trust/lifecycle field *shapes* are part of the LOKF schema and the curation ceremony, so LOKF
  Enforcer validates them (§8, §10) by *deriving from the shared schema* - never by re-coding OKF.
- **Keep `validator.ts` import-free.** Editor, index, suggest, and fix layers wrap the rule
  engine; the rules stay Node-testable via `npm run smoke-test`.
- **Respect the dot-folder constraint.** `.lokf/knowledge` is invisible to Obsidian's index;
  none of the new indexing/eventing changes that (the "open folder as vault" guidance stands).
- **Independence.** No hard dependency on the Curator, the OKF validator, or the agent skills -
  a read-only API may be *offered*, never *required*.

## 7. Evidence index (quick reference)

| Enhancement | Primary source `file:symbol` |
| --- | --- |
| Inline diagnostics | tasknotes `editor/TaskLinkOverlay.ts:createTaskLinkViewPlugin`; dataview `ui/views/inline-field-live-preview.ts` |
| metadataCache + incremental | dataview `data-index/index.ts:FullIndex.reload/revision`; serializer `plugin.ts:recentlyUpdatedFiles` |
| Relationship index / query | dataview `data-index/index.ts:IndexMap`; `data-index/resolver.ts:matchingSourcePaths` |
| Autocomplete | quickadd `gui/suggesters/suggest.ts:TextInputSuggest`; `fileSuggester.ts:FileSuggester` |
| Quick-fixes | tasknotes `core/VaultMutationService.ts:processVaultFrontMatter`; quickadd `GenericSuggester.Suggest` |
| Report virtualize/nav | notebook-navigator `hooks/useListPaneScroll.ts`; omnisearch `components/modals.ts`, `tools/text-processing.ts` |
| Per-note opt-out | serializer `utils/is-ignored-by-frontmatter.fn.ts:isIgnoredByFrontmatter` |
| Field-name mapping | tasknotes `core/FieldMapper.ts:toUserField` |
| Device-local settings | serializer `utils/device-disabled.ts` |
| i18n | tasknotes `i18n/I18nService.ts`; notebook-navigator `i18n/index.ts` |
| Public API (+ complete hook) | dataview `api/plugin-api.ts:DataviewApi`; omnisearch `tools/api.ts:registerOnIndexed` |
| Finding source anchors (A0) | dataview `data-import/inline-field.ts` (offsets); serializer `find-queries.fn.ts` |
| Custom-rule extension | dataview `api/extensions.ts:Extension` |
| Finding tree / collapse | notebook-navigator `utils/tagTree.ts:buildTreeFromTagList` |
| "Fix all" preflight modal | quickadd `preflight/OnePageInputModal.ts`; `interactive/promptProvider.ts` |
| Accessible controls | tasknotes `ui/taskCardProperties.ts:prepareInteractiveControl` |
| Self-write loop guard | serializer `plugin.ts:filesToIgnoreFileEvents` |
| Notification cap / debug log | serializer `plugin.ts:MAX_ERROR_NOTIFICATIONS`; notebook-navigator `services/diagnostics/DebugLoggingService.ts` |
| OKF boundary / fix-on-issue | okf-enforcer `validator.ts:FixKind`, `OkfSettings`; `portent.ts:PortentSettings` |
| Native read/write (already shipped) | okf-enforcer `main.ts` `metadataCache`/`processFrontMatter` + `vault.on` hooks |
| Schema-derived vocab/ownership | lokf `lokf.yaml` (classes, `RelationType`/`DiataxisMode`/`FieldType` enums, `in_subset`); `lokf.schema.json`; `lokf vocab` |
| Body-link -> typed relation | lokf `src/lokf/propose.py` (`extract_links`, cue-phrase `propose`, `apply`) |

## 8. OKF Enforcer: coexistence, conflict, and ownership

A separate, mature plugin - **OKF Enforcer** (MartinForReal, v0.6.1, Apache-2.0) - validates the
OKF v0.2 layer that LOKF Enforcer deliberately leaves alone. It is built on the *same skeleton*
as this plugin (`main.ts`/`validator.ts`/`report-view.ts` at the root, the same folder-grouped
report, the same `✓/⚠/✖` status bar, the same batched queue, the same `.lokf`/`llms.txt`
scaffolding) - the two are siblings. This section inventories it, judges whether the three
plugins conflict, and settles what LOKF Enforcer should own.

### 8.1 What OKF Enforcer already does (feature inventory)

- **OKF v0.2 conformance** with spec-section rule ids (`§4.1`, `§5.1–§5.5`, `§7`, `§8`, `§9`,
  `§10`, `§11`, `§13`): hard rules as **errors** (parseable frontmatter, non-empty `type`,
  `index.md`/`log.md` structure), recommended fields as toggleable **warnings**.
- **Trust/provenance/lifecycle (§5):** `generated`/`verified` + actor convention, `status`,
  `stale_after` (absolute + passed), `sources` credibility - and the derived **trust tier**
  (unverified / machine-confirmed / human-reviewed) in the status-bar tooltip.
- **Attested Computation (§10)**, **v0.1->v0.2 migration (§13)** command, **`index.md`
  generation (§8)** (additive or rebuild, type-grouped, wikilink-aware, subdir descriptions, or
  a "report gaps" mode), **`log.md` entries (§9)**.
- **Auto-fix** (`FixKind` per issue: `add-frontmatter`/`add-type`/`add-title`/`add-generated`/
  `migrate-*`), **bulk "fix all"**, **prompt-for-required-fields modal**, **on-save/on-create/
  on-rename/on-delete hooks**, **batched non-blocking queue**.
- **Portent layer (opt-in, beta):** a *different* profile (`portent.ts`) with its own type
  vocabulary, lifecycle, `belongs_to`/`related_to`, all **warnings**, and **free-form field-name
  overrides** (`status=state`, `belongs_to=parent`).
- **Native APIs it already uses:** `metadataCache.getFileCache`, `fileManager.processFrontMatter`,
  `vault.on(...)` hooks. **It does *not* use** `registerEditorExtension`, `EditorSuggest`,
  `ViewPlugin`, or `Decoration` - i.e. **no inline editor diagnostics and no autocomplete.**

### 8.2 Who owns what

| Layer | Owner | Concern |
| --- | --- | --- |
| OKF v0.2 conformance (§4–§11), index/log generation, migration, Attested Computation | **OKF Enforcer** (3rd-party) | is the note a valid OKF concept at all |
| Portent profile | **OKF Enforcer** (opt-in) | a separate KB vocabulary |
| LOKF semantic layer (`base_iri` authority, 14-class vocab, `genre`, type-specific fields, typed relations + resolution, `id` minting) | **LOKF Enforcer** (yours) | is the note valid *LOKF* |
| Human confirmation (`verified: human:` writes, review queue) | **LOKF Curator** (yours) | did a person vouch for it |

### 8.3 Do they conflict?

- **Rule level - no.** Disjoint rule namespaces (`§n` vs `lokf/*`) and disjoint concerns; no rule
  double-reports. The one adjacency is `type`: OKF requires it non-empty, LOKF wants it in the
  14-class vocabulary, Portent wants its own 8 - these *layer* (a `Metric` is OKF-valid,
  LOKF-valid, Portent-unknown). The only visible artefact: a vault running OKF Enforcer **with
  Portent on** *and* LOKF Enforcer sees two different "unknown type" warnings from two
  vocabularies - confusing, not incorrect, and avoidable (don't pair Portent with LOKF).
- **Reserved files - minor.** Both touch the root `index.md`, different parts: OKF writes/checks
  the §8 **body listing**; LOKF checks the **semantic-header frontmatter**. OKF's generation is
  additive and preserves frontmatter + prose, so churn is low - but if a user turns on OKF's
  *rebuild*, the two shouldn't both write that file in one session.
- **Curator - no.** OKF Enforcer only *reads/displays* the trust tier; the Curator *writes*
  `verified: human:` events. Complementary (Curator produces what OKF Enforcer shows), and both
  derive the tier deterministically from the same events, so they always agree.
- **Experience level - yes.** Two status-bar items, two near-identical report panels, two vault
  scans, two command sets, two settings tabs. For a user wanting full OKF+LOKF coverage, running
  both is redundant chrome and double cost. **This - not rule collision - is the real conflict.**

### 8.4 Decision: coexist, don't clone - with a scoped opt-in core

**Do not fork OKF Enforcer's engine into LOKF Enforcer.** Cloning its ~1,700-line validator plus
index generation, migration, Attested Computation, and Portent would (a) reintroduce the
two-copies-of-OKF maintenance burden this plugin's README rightly refuses, (b) make you owner of
heavy OKF-domain machinery outside your remit, and (c) carry an Apache-2.0 third-party into your
tree with the attribution and release-coupling that implies. OKF Enforcer's depth *is* its reason
to exist - leave it there.

**Do add the tickbox you proposed - but scoped.** Add an **optional "OKF v0.2
core conformance" check** that runs the three hard §11 requirements - a parseable frontmatter
block is present, `type` is non-empty, and `index.md`/`log.md` structure is valid - **plus the §5
trust/lifecycle field *shapes* (§10)** - so a user who runs **LOKF Enforcer alone** gets a
self-sufficient baseline instead of today's
README telling them to go install another plugin. It must **not** re-implement OKF's *deep*
checks - index/log generation, migration, Attested Computation, Portent, or the
credibility-signal and trust-tier-*display* depth of §5 - those stay OKF Enforcer's value and its
reason to install. It **does**, however, cover the *shape* of the §5 trust/lifecycle fields
(`generated`/`verified`/`status`/`stale_after`), because those are LOKF-schema-defined and the
substrate of LOKF's own curation ceremony - see §10. Label it plainly: *"Enable only if you do not run an
OKF v0.2 validator; leave off to avoid duplicate findings."* Because there is no reliable public
API to detect another plugin (this plugin's own stated position - it never reads `app.plugins`),
the switch stays a **manual user choice**, defaulting off so the recommended beside-OKF-Enforcer
setup is unchanged. The core-vs-LOKF split need not be hand-drawn: the upstream schema's
`in_subset` tags (`okf_core` / `okf_v02` / `lokf_semantic`) already partition every slot by
owner, so this boundary can be *derived* from the schema - see §9. (§11 revisits this default and
scope in light of OKF Enforcer's maintenance risk.)

**Win the experience where neither plugin competes.** OKF Enforcer has *no* inline diagnostics
and *no* autocomplete; LOKF Enforcer's A/A0 (inline CM6 findings), D (EditorSuggest), and C
(relationship index + query) are therefore uncontested ground and the clearest path to the
"immersive Obsidian experience" being sought. Prioritise those over absorbing OKF rules. A
later, optional **unified report / single status-bar mode** (one panel showing both plugins'
findings) would remove the remaining duplicated chrome without either plugin owning the other's
rules - a future item, not a dependency.

### 8.5 Enrichments to fold in from OKF Enforcer (weighed vs the showcase)

- **Attach a `fix` descriptor to each finding** (OKF `FixKind` on `OkfIssue`) - direct
  corroboration of **A0**'s structured payload. Adopt the on-issue fix field, but deliver it
  **inline (A) and via a preflight modal (E)** rather than OKF's status-bar-click-only, which the
  showcase plugins beat for nativeness.
- **Bulk "fix all safe issues" command** (OKF `okf-fix-all`) - add to **E**, constrained to
  LOKF's *safe-fix subset* (never the `base_iri`-authority class).
- **On-create / on-rename / fix-on-save hooks** (OKF `vault.on(...)` + `fixOnSave`) - add to **B**
  as an **opt-in** mode, paired with the serializer's debounce + cooldown + self-write de-dup so
  it can't loop or fight the user's typing.
- **Prompt-for-required-fields modal** (OKF `OkfPromptModal`) - maps to **E**'s preflight;
  quickadd's `OnePageInputModal` is the more reusable, native implementation to model on.
- **Free-form field-name overrides** (OKF Portent `concept=key`) - a *shipping validator* already
  offers exactly the remapping in **G**, strengthening it (still advanced, still off by default,
  since LOKF has canonical keys).
- **Corroboration, not a gap:** OKF Enforcer already reads via `metadataCache` and writes via
  `processFrontMatter` - proof that **B** and **E**'s native path is right, and that LOKF
  Enforcer's manual `parseYaml` is behind its own sibling.

### 8.6 Deliberate non-goals (avoid triplication)

- **Trust-tier *display*** stays with OKF Enforcer (shows it) and the Curator (produces it) - LOKF
  Enforcer adds no third *display* of the tier. It does validate the *shape* of the §5 fields the
  tier derives from (display ≠ shape; see §10) - that shape is ceremony-critical and
  LOKF-schema-defined.
- **`index.md`/`log.md` generation, v0.1->v0.2 migration, Attested Computation, Portent** remain
  OKF Enforcer's - LOKF Enforcer's only index write stays the semantic-header scaffold.
- **OKF §5 *deep* validation** (credibility-signal heuristics, tier display, migration) is *not*
  pulled in; a user who wants it installs OKF Enforcer. The §5 field *shapes*, by contrast, **are**
  checked here - they are the curation ceremony's wiring (§10).

## 9. The `lokf` project (upstream LinkML schema + toolkit): what to leverage

LOKF is defined once in **`lokf.yaml`** (a LinkML schema, v0.7.0) from which the JSON-LD context,
JSON Schema (`lokf.schema.json`), SHACL, OWL, and SQL are generated. The plugin currently
*hard-codes* a copy of the vocabulary (`KNOWN_LOKF_TYPES`, `DEFAULT_KNOWN_PREDICATES`,
`DEFAULT_GENRE_VALUES`) and terse messages. The schema is a far richer source, and reading from
it - with fallbacks - is the highest-leverage, lowest-risk structural change available.

### 9.1 What the schema already carries that the plugin ignores

- **A newer, larger vocabulary than the plugin's copy.** The SPEC's type table lists 14 classes;
  `lokf.yaml` 0.7.0 adds **`Role`** (`schema:OrganizationRole`; slots `roleName`/`startDate`/
  `endDate`/`memberOf`/`holder`) as a concept type the plugin's 14-item `KNOWN_LOKF_TYPES` is
  **missing**. The `RelationType` enum has **15** predicates - the plugin's
  `DEFAULT_KNOWN_PREDICATES` (ten fields + `joinsWith`) **omits `wasAttributedTo`, `measures`,
  `memberOf`, `holder`.** The plugin's vocabulary is simply *stale*.
- **Rich per-element metadata:** every class and slot has a `description`, a `slot_uri`, and
  ontology `*_mappings`; many carry `aliases` (e.g. `Playbook` ↔ `runbook`; `AttestedComputation`
  ↔ the spaced `Attested Computation`), `see_also` (Diátaxis URLs), `comments` ("Pair with
  `genre: how-to`"), and `examples`. `DiataxisMode` values even carry `notes` ("Answers 'How do
  I…?'") and action/cognition quadrant annotations. The plugin surfaces none of it.
- **Deprecations are declared:** `timestamp`, `citations`, and the `Citation` class are marked
  `deprecated` (superseded by `generated.at` / `sources`). The plugin never says so.
- **Ownership is machine-readable:** `in_subset` tags each slot `okf_core`, `okf_v02`, or
  `lokf_semantic` - exactly the OKF-vs-LOKF boundary §8 draws by hand.

### 9.2 Leverage-with-fallback: a build-time vocabulary manifest

The plugin cannot run LinkML/Python in Obsidian, so **derive a small JSON manifest at build time**
from the *pinned* `lokf.yaml` (or the `lokf vocab` command, or the generated `lokf.schema.json`)
and ship it as data:

```
{ schemaVersion, classes[], relationTypes[], genres[]{value,aliases,notes},
  fieldTypes[], conceptStatuses[], subsets{okf_core,okf_v02,lokf_semantic},
  deprecations{field->supersededBy}, descriptions{element->text}, mappings{element->curie} }
```

At runtime the plugin loads the manifest and **falls back to today's hard-coded constants** if it
is missing, malformed, or a value is unknown (LOKF's permissive stance already requires tolerating
unknowns). This keeps `validator.ts` **import-free** (the manifest is data passed in, like
`settings`), keeps the vocabulary in lockstep with a pinned upstream, and powers richer
autocomplete (**D** - show each suggestion's description/aliases/notes) and hover (**A** - the
slot's own description + mapping + `see_also`). It also lets §8.4's core-vs-LOKF split be **derived
from `in_subset`** rather than hand-maintained. Record the manifest's `schemaVersion` so the UI can
say "vocabulary as of LOKF schema 0.7.0."

### 9.3 New checks the schema unlocks (all warnings, all fallback-guarded)

- **Refresh + widen the vocabulary:** add `Role`; widen accepted `predicate`s to the full
  `RelationType`; **accept `aliases`** (stop nagging `runbook` or the spaced `Attested
  Computation`, and offer the canonical form as a fix).
- **Validate `Field.datatype` ∈ `FieldType`** and keep `genre ∈ DiataxisMode` - both now
  schema-backed rather than a hand-typed list.
- **Surface deprecations:** an advisory "`timestamp` is superseded by `generated.at`" straight
  from the schema's `deprecated:` text.
- **Validate §5 trust/lifecycle *shape*** from the schema's `Generation`/`Verification`/`Source`
  classes + `ConceptStatus` enum (a well-formed `verified` list of `{by, at}`, a §7-actor `by`, a
  date `stale_after`, `status ∈ {draft, stable, deprecated}`) - the substrate the curation ceremony
  depends on (§10); the shapes come straight from `lokf.schema.json`.
- **Type↔genre pairing hint** from a class's `comments` ("`Playbook` pairs with `genre: how-to`")
  - a gentle suggestion via **D**/**E**, never a hard rule.

### 9.4 Toolkit capabilities that map onto the plan (and one new item)

- **`lokf vocab`** - the machine-readable vocabulary dump; the natural source for the manifest
  (§9.2).
- **`lokf validate` (JSON Schema) + `lokf-check-refs` (SPARQL target-existence)** - the plugin's
  live equivalent is exactly **C** (the relationship/type index) plus today's relation-resolution
  check; this confirms **C**'s design.
- **`lokf serve` / `query` / `tables` / `export`** - SPARQL endpoint, graph explorer, table
  projection, and static-site artifacts (`graph.json`, `concepts.jsonld`); the plugin's **C**
  query panel is a lightweight in-Obsidian analog, and a future "export graph" could reuse those
  shapes.
- **`lokf propose` - a new plan item.** It extracts markdown body links, classifies each against a
  cue-phrase table, and writes the inferred **typed relation into frontmatter** without disturbing
  formatting (`src/lokf/propose.py`: `extract_links` -> `propose` -> `apply`). This is the single
  most transferable *capability* here: a quick-fix/suggestion **"promote an untyped body link to a
  typed relation"** (`references:`/`dependsOn:`/…), powered by **C**'s index and delivered through
  **E**/**D**. Add it to the roadmap under Phase 2.
- **`registry` (init/add/list/resolve) + `mcp`** - cross-bundle IRI resolution and an agent
  surface; they align with a future *registry-aware relation check* (resolve an external IRI
  instead of flagging it) and the read-only public API (**I**). Future, not now.

### 9.5 What `lokf.yaml` itself could gain (anticipate, then derive)

The schema is descriptively rich but **constraint-poor**: it has **no `pattern`, `rules`,
`recommended`, `values_from`, or cardinality bounds** - only `required` (nine times). The plugin's
most valuable checks are therefore constraints the schema does not yet encode, which means they
can be **upstreamed** and then consumed with fallback:

- a `pattern`/`structured_pattern` on `base_iri` (absolute http(s), trailing `/`) and an
  `id`-minting rule;
- `recommended: true` on `Metric.unit`/`formula`/`measures`, `Service.endpoint`/`http_method`/
  `documentation`, `GlossaryTerm.definition`, and `Table`/`Dataset` `fields`/`distribution` - so
  the plugin's "missing recommended field" warnings become schema-derived;
- a `pattern` for the OKF §7 actor string on `by`/`author`;
- `publisher` sub-field expectations (type ∈ {Person, Organization}, `id`, `name`).

Proposing these upstream (they are the plugin's own rules, already written and tested) benefits the
whole ecosystem; until they land, the plugin keeps them as local rules and treats the schema as
**additive** - leverage what is present, never require what is not.

### 9.6 Guardrail: no runtime dependency on the toolkit

The manifest is generated at build time from a pinned schema and shipped as static data; the
plugin never invokes `uv`, Python, or the `lokf` CLI at runtime and degrades to built-in defaults
without the manifest. This preserves both the import-free `validator.ts` and the plugin's stated
independence - it works on any LOKF bundle, however produced, needing no toolkit, skill, or agent.

## 10. The curation ceremony vs. the OKF-delegation boundary (refines §8)

### 10.1 The concern

§8 delegated the whole OKF layer to OKF Enforcer. But `lokf-agent-skills` ships a **curation
ceremony** - a four-role loop with explicit handoffs:

scaffolding -> **librarian** (derives concepts, marks them `status: draft`, records `generated:
{by: process:lokf-librarian, at}`, leaves `## Open questions`) -> **curator** (a person confirms /
corrects / retires / sends back, writing `verified: {by: human:<id>, at}`, clearing `status:
draft`, sometimes setting `stale_after` or `status: deprecated`) -> **docent** (reads, records
misses and disagreements in `.lokf/feedback.md`) -> back to the librarian, on a schedule.

**Every arrow in that loop carries OKF v0.2 §5 frontmatter** (`generated`, `verified`, `status`,
`stale_after`, `sources`) or a reserved convention (`## Open questions`, `.lokf/feedback.md`). OKF
Enforcer has no model of this ceremony - it treats §5 as generic conformance, not as a handoff
protocol. So delegating §5 *entirely* to it makes the correctness of the user's flagship workflow
depend on a third-party plugin being installed - and even then, that plugin does not understand
the workflow it is guarding. **The concern is correct.**

### 10.2 Why it bites in Obsidian specifically

The two plugins exist because "people edit bundles by hand and there is no CI to catch them." A
human hand-editing `verified`, `status`, or `stale_after` in the Properties panel can malform it:
a `verified` event missing `by`/`at`; a `by` that ignores the §7 actor convention (so the `human:`
prefix the tier keys off is absent); a `status` typo outside `{draft, stable, deprecated}`; a
`stale_after` that isn't a date. If nothing validates §5 live:

- the **Curator plugin** silently miscounts - its `trust.ts` already defensively normalizes (bare
  `verified` mapping, `Date` objects), but defensiveness *hides* malformed data rather than
  reporting it, so the health report and "worth ten minutes" queue quietly drift;
- the **docent's** "how far has this been trusted" answer is computed from the same malformed
  fields and is wrong;
- the **librarian's** scheduled `verified: {by: process:lokf-librarian}` refresh can't be told
  apart from a broken one.

The ceremony degrades from the substrate up, invisibly - the worst failure mode for a trust system.

### 10.3 The reframe that dissolves the "duplication" objection

§8's guardrail was "don't duplicate OKF." But the §5 families are **defined in `lokf.yaml`** - as
the `Generation`, `Verification`, `UsageWindow`, and `Source` classes, the `ConceptStatus` enum,
and the `by`/`at`/`status`/`stale_after` slots (subset `okf_v02`, several with `lokf:`-minted IRIs
like `lokf:verified`). LOKF Enforcer's whole mandate is *"validate the LOKF schema layer."*
Therefore **validating the shape of the §5 fields is enforcing the LOKF schema, not duplicating
OKF Enforcer** - and the generated `lokf.schema.json` already encodes those shapes, so the §9
manifest covers them for free. The current README's "§5 is OKF's job, we deliberately don't check
it" is a *mis-scoping*: the fields originated in OKF v0.2, but they are part of the LOKF schema and
the load-bearing substrate of LOKF's own ceremony, so their well-formedness is squarely LOKF
Enforcer's business.

### 10.4 The refined delegation boundary

Split "OKF" into three and delegate only the outer two:

| OKF slice | Delegate to OKF Enforcer? | Why |
| --- | --- | --- |
| Index/log generation (§8/§9), v0.1->v0.2 migration (§13), Attested Computation (§10), recommended-field warnings (§4.1), §11 index/log *structure* | **Yes** | generic OKF plumbing; heavy; no ceremony role |
| §5 trust/lifecycle field **shapes** (`generated`, `verified` + actors, `status` vocab, `stale_after` date, `sources` shape) | **No - own the shape** | the ceremony's handoff protocol, defined in `lokf.yaml`, read/written by your Curator |
| §5 **depth** (credibility-signal heuristics, trust-tier *tooltip display*) | **Yes (optional)** | interpretation + presentation, not handoff integrity |

### 10.5 Where the §5 shape check lives

- **LOKF Enforcer** validates §5 *shape* - the schema-valid tier the whole ceremony stands on.
  Scope it to what the handoffs consume (a well-formed `verified` list of `{by, at}` with §7
  actors, `generated: {by, at}`, `status ∈ ConceptStatus`, `stale_after` a date), **derived from
  the §9 manifest** so it is not a second hand-maintained copy. This is tens of lines, not OKF
  Enforcer's full §5 + credibility engine.
- **LOKF Curator** keeps *consuming* well-formed §5 and *producing* verdicts - and can now trust
  that the substrate is validated upstream instead of defending against malformed input.
- **OKF Enforcer** remains the *optional* deep layer (credibility signals, tier tooltip,
  migration). The small overlap on "is `verified` well-formed" is acceptable and bounded - the
  price of not coupling your own ceremony to an optional dependency, and far short of the rejected
  full-engine clone.

Teach both plugins to *recognize* (never choke on) the ceremony's reserved markers: `## Open
questions` (librarian->curator), `.lokf/feedback.md` (docent->librarian), `status: draft`
(librarian-created), and `by: process:lokf-librarian` vs `human:` (tier derivation). The Curator
already handles these; the Enforcer must at least not misreport them.

### 10.6 Amendments to §8

- **§8.4 - widen the opt-in core.** The scoped core becomes **§11 hard rules *plus* §5
  trust/lifecycle field shapes** (still not index/log generation, migration, Attested Computation,
  Portent, or the deep credibility/tier layer). Because §5 shapes are LOKF-schema-defined, that
  slice is arguably **on by default whenever the bundle uses §5 fields**, unlike the §11 generic
  rules, which stay off by default (they are the part that overlaps OKF Enforcer).
- **§8.6 - reframe the trust non-goal.** "No third trust-tier *display*" stands (display belongs
  to OKF Enforcer + Curator). But validating the *shape* of the fields the tier derives from is
  not display - it is ceremony-critical and owned here. Display ≠ shape.

### 10.7 Net

Delegating *generic* OKF to OKF Enforcer does not degrade the solution. Delegating the *§5
curation substrate* to it does - that substrate is the ceremony's wiring, it lives in the LOKF
schema, and outsourcing it makes LOKF's own trust loop depend on a third-party plugin that has no
concept of the loop. **Own the §5 *shape* in LOKF Enforcer** (schema-valid tier); leave §5 *depth*
and all other OKF plumbing to OKF Enforcer. This keeps the "don't run two full OKF engines" intent
of §8 while closing the gap the ceremony exposed - and it is why the answer to "delegate OKF
entirely?" is **no: delegate all of OKF except the §5 shapes that are LOKF's own ceremony
speaking.**

## 11. Wrap-up: ownership and responsibility, reconsidered against OKF Enforcer's risk

§8 and §10 optimised for *not re-implementing OKF*. This section adds the factor the user raised -
**OKF Enforcer is a single-maintainer, Apache-2.0, partly-beta project that may go unmaintained** -
and reconsiders where rigour and responsibility should sit. It supersedes §8.4's "off-by-default,
minimal-§11 core" framing on the *conformance* axis; it does **not** revive the rejected "clone the
whole engine."

### 11.1 The risk reframes the boundary along a better axis

§8 drew the line at "OKF vs LOKF." Maintenance risk reveals a sharper axis:
**validation that is derivable from the shared schema** vs **imperative, generative machinery**.

- `lokf.yaml` is the single source of truth for **both** the LOKF layer **and** OKF v0.2 (the
  `okf_core` + `okf_v02` subsets). So *everything that is validation* - LOKF semantics, the §11
  hard rules, the §5 shapes, recommended fields, Attested-Computation *shape*, `type`-required -
  is derivable from the one §9 manifest. Owning it is **rigorous and maintenance-light** (schema-
  derived, Node-testable, permissive) and is **not** "cloning OKF Enforcer's code" - it is
  enforcing the schema the whole ecosystem already shares.
- What is genuinely worth delegating is only the **imperative, non-validation, heavy** machinery:
  `index.md`/`log.md` **generation**, v0.1->v0.2 **migration**, and **Portent**. Losing these if
  OKF Enforcer is abandoned degrades *comfort, not correctness* - and they are recoverable by
  forking (Apache-2.0).

### 11.2 Recommendation: the owned pair becomes a complete, self-sufficient registrar's desk

- **Promote the "OKF-core" tickbox from a §11 stopgap to full OKF v0.2 *conformance validation*,
  derived from the shared-schema manifest** - so LOKF Enforcer + LOKF Curator together validate
  the entire LOKF + OKF v0.2 *conformance* surface with **no third-party dependency**. Make it on
  by default (it is schema-derived and permissive); a user who *also* runs OKF Enforcer sees the
  small, bounded `verified`/`type`/structure overlap, which is the acceptable price of
  independence.
- **Reframe OKF Enforcer as an optional companion for the *generative conveniences*** - index/log
  generation and migration - not a dependency for conformance. This inverts today's README ("install
  OKF Enforcer for full coverage") into: *the owned pair is the rigorous foundation; OKF Enforcer
  optionally adds generation and migration on top.*
- **Rigour belongs at the foundation.** The owned stack is schema-driven (one source of truth),
  ceremony-backed (four skills), and fully owned (both plugins + the shared scaffolding). Basing
  *conformance* on it - rather than on a loose, single-maintainer third party - puts the rigorous
  layer where the foundation should be, and lets the at-risk project sit safely *above* it as a
  removable convenience.

### 11.3 Why this is not the rejected "clone the engine"

- **Derivation, not duplication.** Owned conformance rules come from `lokf.yaml` via the §9
  manifest - one generated artefact, unit-tested under Node - not a hand-copied second engine. The
  "don't run two full OKF engines" intent of §8 holds: there is one schema-derived validator.
- **Generation and migration stay out.** LOKF Enforcer never grows `index.md`/`log.md` writers,
  a migration rewriter, or Portent. Its only write path remains the semantic-header scaffold plus
  the safe quick-fixes (E). Those are the heavy, opinionated, imperative parts genuinely better
  left to OKF Enforcer - and genuinely survivable if it lapses.
- **No coupling, no detection.** Neither plugin detects or calls the other; OKF Enforcer stays a
  recommended-but-optional install, and a fork is the ultimate backstop.

### 11.4 The one honest trade-off

Owning full conformance *validation* is more surface to test than the minimal §11 core - but
schema-derived rules have low marginal cost, are Node-testable like `validator.ts`, and are far
cheaper to keep than OKF Enforcer's imperative index-gen/migration code would be. In exchange it
**removes a single point of failure from the foundation**. Net: worth it.

### 11.5 Curator's place in the reconsidered stack

LOKF Curator (human-confirmed tier) is unaffected by OKF Enforcer's fate: with LOKF Enforcer now
validating §5 shape and full conformance, the Curator's substrate is *guaranteed by the owned
pair*. The two owned plugins become a complete registrar's desk on their own - **Enforcer =
schema-valid (LOKF + OKF v0.2 conformance), Curator = human-confirmed** - with OKF Enforcer
demoted from "recommended companion for coverage" to "optional convenience for generation and
migration." That is the ownership posture the rigour-vs-risk trade-off points to.

### 11.6 Net decision

- **Own (LOKF Enforcer):** the LOKF semantic layer **and** all OKF v0.2 *conformance validation*,
  derived from the shared schema - on by default, rigorous, third-party-independent.
- **Own (LOKF Curator):** the human-confirmed tier, reading/writing the §5 substrate the Enforcer
  now guarantees.
- **Delegate (OKF Enforcer, optional):** index/log **generation**, **migration**, **Portent**, and
  the §5 **depth** (credibility signals, tier display) - conveniences that degrade gracefully and
  are forkable if the project lapses.

This keeps every earlier guardrail (derive-don't-clone, warnings-not-errors, import-free
`validator.ts`, no cross-plugin coupling) while ensuring the solution does **not** degrade if OKF
Enforcer becomes unmaintained - because nothing load-bearing was ever outsourced to it.

## 12. Companion plan: folding the same improvements into LOKF Curator

The two owned plugins are siblings that already share `bundle.ts` (bundle-root plumbing,
`splitFrontmatter`, `mintExpectedId`). To keep them **in step** - architecturally and in native
feel - the Curator should adopt the same showcase enrichments where they apply, share one
implementation of anything both need, and both should render a common **handoff** language so a
reader can see where a concept sits in the librarian -> curator -> docent loop.

### 12.1 What the Curator already leads on (the Enforcer borrows *up*)

The Curator is ahead of the Enforcer on the native path: it reads via `metadataCache.getFileCache`,
writes via `processFrontMatter` (frontmatter) and `vault.process` (body) in `edits.ts`, and ships a
one-concept-at-a-time **review card** (source beside claim), a **health** chip row, and a ranked
**"worth ten minutes" queue**. Those are exactly the patterns the Enforcer's B/E/F borrow - so this
companion plan is mostly the Enforcer catching up, plus a few showcase items flowing the other way.

### 12.2 Showcase enrichments the Curator should also adopt (mirror A–J, curator-flavoured)

- **A / inline markers.** A CodeMirror gutter/decoration on a concept's frontmatter showing its
  **trust label** at a glance in the editor - *confirmed by a person* / *checked by automation
  only* / *nobody has checked* / *draft* / *past review* - the review card's verdict, live where
  the note is edited. (tasknotes `editor/TaskLinkOverlay.ts`; dataview `inline-field-live-preview.ts`.)
- **B / incremental.** Recompute the affected concept's `TrustRecord` on `metadataCache` change
  instead of re-scanning the bundle; poll a `revision` like dataview's `FullIndex`. The Curator
  already parses per file - this just makes the health/queue update live and cheap.
- **D / autocomplete.** `EditorSuggest` for the values a curator hand-types: the actor string
  (`human:<id>` from `curatorId`), `status ∈ ConceptStatus`, and dates - from the shared manifest
  (§9.2). (quickadd `suggest.ts:TextInputSuggest`.)
- **F / navigation + scale.** Virtualize the queue, add a filter box, a keyboard-first
  quick-switcher over queued concepts (`SuggestModal`), and a context menu (open / confirm / send
  back). (notebook-navigator `useListPaneScroll`; omnisearch `modals.ts`.)
- **Commands + ribbon.** "Review next in queue", "Confirm active note", "Send active note back",
  and a ribbon toggle for the Curate panel - the Curator today is panel-only.
- **I / i18n + report persistence + read-only API** - same as the Enforcer, sharing one i18n
  catalogue and the "validation/curation complete" hook so each can refresh when the other acts.

### 12.3 Shared modules (keep the siblings byte-coherent)

Both plugins must agree exactly where their surfaces overlap; factor these into one shared,
Node-tested source rather than two drifting copies:

- **Trust-tier derivation.** The Curator's `trust.ts` (`normalizeVerified`, tier from the `human:`
  prefix, `isStale`, open-questions detection) is the reference; the Enforcer's §5-shape check (§10)
  must reuse the *same* normalize/tier logic so both never disagree about a concept's tier.
- **The §9.2 vocabulary manifest** - one build-time artefact, consumed by both (Curator reads
  `ConceptStatus`, the actor convention, `RelationType`; Enforcer reads the full vocabulary).
- **The panel/list/tree/virtualizer** (§4 optimisation) - one implementation, themed per plugin.
- **`bundle.ts`** - already shared; extend it with the position-anchor/A0 locator so inline markers
  work identically in both.

### 12.4 The handoff-hint feature (both plugins, one visual language)

The ceremony's handoffs are recorded in frontmatter and reserved files; both plugins can surface
**where the last handoff occurred**, computed on every read (never stored), from a single shared
label function:

| Signal in the concept | Handoff shown |
| --- | --- |
| `status: draft` + `generated.by: process:lokf-librarian` | *Drafted by the librarian - awaiting a curator* |
| any `verified[].by: human:<id>` | *Confirmed by `<id>` on `<date>`* |
| `## Open questions` present | *Has open questions - for the curator* |
| note mtime > last `human:` `verified.at` | *Edited since a person last confirmed it* |
| `stale_after` passed | *Past its review date* |
| `.lokf/feedback.md` non-empty | *Reader feedback waiting for the librarian* |

- **Enforcer (schema-valid tier)** shows these **passively** - a non-error informational marker in
  the gutter / status-bar tooltip. They are *not* conformance findings, so they never count as
  warnings or errors (guardrail: warnings-not-errors; a handoff marker is *info*).
- **Curator (human-confirmed tier)** shows them **actively** - "your turn: N awaiting confirmation",
  and click-to-jump into the review card at that handoff. This is the queue it already builds,
  reframed as an explicit handoff trail.
- Both draw from **one shared label vocabulary** (in the shared trust module), so the phrase a
  reader sees is identical whichever plugin surfaces it - matching the agent-skills principle that
  the labels are computed from the files on every read and cannot drift.
- **Opt-in and quiet.** The hint is a setting, off for a plain vault; it never blocks, never writes,
  and the Enforcer must not treat a `process:lokf-librarian` draft or an `## Open questions` section
  as a defect - they are the ceremony working, not a problem.

### 12.5 Roadmap alignment

Land the **shared modules once** (trust logic, manifest, panel/virtualizer, position anchor) and
both plugins consume them: Enforcer Phase 1–2 (§5) and a mirrored Curator Phase 1–2 draw from the
same commits. The handoff-hint feature ships when the shared trust module and inline-marker layer
(A) are in place - Enforcer Phase 2, Curator Phase 2 - so both light up together.

### 12.6 Curator guardrails (unchanged responsibilities)

- The **Curator remains the only writer of `human:` verifications**; the Enforcer never writes
  trust, only validates its shape and surfaces handoff hints.
- Both stay **permissive** and **independent** (no cross-plugin detection or calls); the shared
  code is a library both import, not a runtime dependency of one on the other.
- Shared trust logic stays **pure and Node-tested**, like today's `trust.ts` / `edits.ts`.

## 13. Proposal to the `lokf` project (PR-ready)

_A concise, paste-ready proposal. All changes are **additive** and **advisory** - no bundle that
validates today stops validating._

### 13.1 Title & motivation

**Title:** *Add advisory constraints (patterns + `recommended`) so downstream validators can derive
checks from the schema.*

**Motivation.** `lokf.yaml` is descriptively rich but **constraint-poor**: it declares no `pattern`,
`recommended`, or cross-field rule, so the SHOULD-guidance in the SPEC lives only in prose. Every
downstream consumer - the two Obsidian plugins (LOKF Enforcer, LOKF Curator) and the four agent
skills - currently *re-invents* the same checks (a `base_iri` must be absolute and end in `/`; a
`generated.by`/`verified[].by` actor must follow the §7 convention; a `Metric` SHOULD carry `unit`;
…). Encoding these as **additive, advisory** metadata lets every consumer derive them from the one
source of truth instead. Nothing here adds a required field or narrows an existing valid value.

### 13.2 Proposed changes (all additive, generator-safe)

**1. `pattern` on `base_iri`** - absolute http(s), trailing slash (ids are minted by concatenation):

```yaml
slots:
  base_iri:
    range: uri
    pattern: "^https?://[^\\s]+/$"
```

**2. `pattern` for the §7 actor string on `by`** (covers `generated.by` and every `verified[].by`;
the string-valued `Source.author` too):

```yaml
slots:
  by:
    range: string
    pattern: "^(human:.+|process:.+|[^/\\s]+/[^/\\s]+)$"
```

**3. `recommended: true` on the type-specific SHOULD fields** (via `slot_usage`, so it is per-class
and inherits nothing unintended):

```yaml
classes:
  Metric:
    slot_usage:
      unit: {recommended: true}
      formula: {recommended: true}
      measures: {recommended: true}
  Service:
    slot_usage:
      endpoint: {recommended: true}
      documentation: {recommended: true}   # http_method stays optional
  GlossaryTerm:
    slot_usage:
      definition: {recommended: true}
  Dataset:
    slot_usage:
      fields: {recommended: true}
      distribution: {recommended: true}
```

**4. (optional) A committed machine-readable vocabulary artefact** - e.g. `lokf.vocab.json` beside
the other generated files, or a documented-stable `lokf vocab --format json`, carrying classes,
`RelationType`/`DiataxisMode`/`FieldType`/`ConceptStatus` values with their `aliases`/`meaning`, and
the `okf_core`/`okf_v02`/`lokf_semantic` subsets. This lets no-Python consumers (the plugins) pin
and read the vocabulary without parsing `lokf.yaml` or the full JSON Schema.

### 13.3 Deliberately *not* proposed (and why)

- **`id == base_iri + concept_path` as a LinkML `rule`.** Cross-field string-concatenation equality
  is awkward in LinkML and risks generator issues (e.g. `gen-owl` is fragile around some rule
  constructs); leave id-minting consistency to a **SHACL shape or the consumer**, not a `rule`.
- **New `required` fields.** OKF/LOKF are permissive by design (unknown types, missing optional
  fields, broken cross-links must not reject). `recommended` expresses the SHOULD without breaking
  that; `required` would reject existing bundles.
- **`publisher` sub-field shape** (type ∈ {Person, Organization}, `id`, `name`) - desirable but not
  cleanly additive on the abstract `Agent` range; raise separately if wanted.

### 13.4 Guarantees for reviewers

- **Backward-compatible:** every change is additive; `recommended` is advisory (not `required`), and
  the two patterns match all conformant values in `examples/` - confirm with `just lint` +
  `just gen-project` + `just test`.
- **Generator-safe:** `pattern`, `recommended`, and `slot_usage` are handled by
  `gen-json-schema`/`gen-shacl`/`gen-owl` without the `union_of`/`none_of` pitfalls; no `rules` are
  added.
- **Permissiveness preserved:** consumers still MUST tolerate unknowns; these only let a consumer
  *choose* to warn, never to reject.

### 13.5 What the raised PR ([nicholsn/lokf#69](https://github.com/nicholsn/lokf/pull/69)) actually shipped - and the learnings

The proposal above was raised as PR #69 (proposed, not yet merged). It landed close to §13.2 but
went further, and two details **correct the plugin's current behaviour** - fold these back in:

- **`base_iri` pattern is `^https?://\S+[/#]$`, not `…/$`.** A **`#`-terminated** base_iri (a hash
  namespace) is valid, not only `/`. The plugin was *erroring* on `https://ex.org/ns#`; that is a
  false positive. **Fixed** in `validateRootHeader` (accept `/` or `#`); the scaffold command's
  template still emits a `/` form (fine). *(Applied.)*
- **`by` is strict; `Source.author` is loose.** `by` = `^(human|process):\S+$|^[^\s/]+/[^\s/]+$`
  (only `human:`/`process:`/`<producer>/<version>`), while `author` = `^[^\s:/]+:\S+$|…` admits any
  `<prefix>:<id>` (e.g. `team:analytics`). §10's `by` check had used the *loose* form - too
  permissive. **Fixed:** the `ACTOR_RE` for `verified`/`generated` `by` now matches the strict
  schema pattern. *(Applied.)*
- **`http_method` is deliberately *not* recommended** (its spec says "if applicable"; a GraphQL /
  gRPC / whole-REST-API Service has no single verb) - and it is now a controlled `HttpMethod` enum
  (GET/POST/PUT/PATCH/DELETE/HEAD/OPTIONS, uppercase). The plugin was *warning* on missing
  `http_method`. **Fixed:** dropped from the Service recommended set. *(Future: a small
  `http_method ∈ HttpMethod` value check, manifest-derived.)*
- **Also shipped beyond §13.2:** an `email` pattern (`^[^@\s]+@[^@\s]+\.[^@\s]+$`); the `http_method`
  enum; a `scaffold.py` fix preserving `#`-terminated base IRIs; and - most usefully - an **official
  `lokf.vocab.json`** build artifact plus **`lokf vocab --manifest`**. `recommended`, `deprecated`,
  `pattern`, and the `frontmatter_key`/`concept` flags survive in *no* generated artifact (JSON
  Schema, SHACL, OWL all drop them), so the manifest is their only machine-readable carrier.
- **Manifest-shape learning (supersedes §9.2's hand-rolled shape).** The official manifest is a
  **richer** shape than `scripts/build-vocab.mjs` currently emits: `classes` is an *object* keyed by
  name carrying `concept`, `abstract`, `aliases`, `slots`, `recommended[]`, and `deprecated`; `slots`
  carries `range`, `required`, `pattern`, and `subsets`; `relationTypes[]` carries `frontmatter_key`
  (the ten slot-keys vs the relations-only predicates); `enums` is an object (incl. `HttpMethod`);
  plus `subsets{}` and `deprecations{}`. **When #69 merges,** switch `build-vocab.mjs` to consume the
  pinned `lokf.vocab.json` (or `lokf vocab --manifest`) and adopt this shape, then **derive** the
  `base_iri` pattern, the `by` pattern, the recommended-field sets, and the frontmatter-key/all-
  predicate split from it - retiring the three hand-coded rules above in favour of schema-derived
  ones (the §9.5 "upstream, then derive" endgame, now concrete).

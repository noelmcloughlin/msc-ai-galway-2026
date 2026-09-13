---
lokf_version: "0.2"
okf_version: "0.2"
base_iri: https://msc-ai.example/knowledge/
context: https://w3id.org/lokf/context.jsonld
title: MSc Artificial Intelligence Knowledge Bundle
description: Personal knowledge vault and programme knowledge graph for a two-year part-time online MSc in Computer Science (Artificial Intelligence) at University of Galway.
license: https://creativecommons.org/licenses/by/4.0/
publisher:
  type: Person
  id: https://msc-ai.example/knowledge/person/noel-mcloughlin
  name: Noel McLoughlin
---

# MSc Artificial Intelligence Knowledge Bundle

A [LOKF](https://lokf.nolan-nichols.com) knowledge base for MSc Artificial Intelligence. Every Markdown file under `knowledge/` is one concept; together they form a queryable knowledge graph.

This bundle holds **entities, not episodes**: the programme, its modules, its teaching staff,
the sources they are evidenced by, and the open questions about them - the stable things that
lecture notes, labs and reading notes in the surrounding vault point *at*. Personal notes stay
in the vault and graduate into `concepts/` only once they have settled. See
`../01-Dashboard/CONVENTIONS.md`.

`base_iri` is a placeholder (`msc-ai.example`, an RFC 2606 reserved domain) pending a real,
owned namespace - consistent with the other LOKF bundles in this author's projects. It mints
every concept `@id`, so migrating it later rewrites all of them; cheap now, expensive later.

Scaffolded 2026-09-11 and materialised the same day from a hand-curated source extract
(`inputs/`), migrated onto this bundle's LOKF vocabulary - see `log.md` for the
fix-list applied. This bundle carries a small domain extension, `msc-ai.yaml` (imports
`lokf.yaml`, a pinned copy of the core schema alongside it) - `just lokf-validate` uses it
automatically; see `msc-ai.yaml`'s header comment for why one was needed. On 2026-09-12 the
first three concepts graduated from the vault's seeded notes - one unchecked draft, one checked by
automation, one carrying an open question - so that every trust state the plugins show is on display.
On 2026-09-13 the bundle moved out of the vault to `.lokf/knowledge/`, the sidecar's one real folder,
opened in Obsidian as a vault of its own through the `knowledge_bundle` link at the repository root.
`lokf-librarian` maintains this table of contents from here on.

# Programme

* [MSc Computer Science - Artificial Intelligence (Online)](programme/programme-uog-1mao3.md) - University of Galway's two-year, part-time, online MSc in Computer Science (Artificial Intelligence), programme code 1MAO3.

# Modules

* [Agents, Multi-Agent Systems and Reinforcement Learning - Online](modules/module-ct5130.md) - CT5130, a curriculum entry in programme 1MAO3. Semester 2, 5 ECTS.
* [Artificial Intelligence and Ethics - Online](modules/module-ct5152.md) - CT5152, a curriculum entry in programme 1MAO3. Semester 1, 5 ECTS.
* [Capstone Project and Thesis in Artificial Intelligence - Online](modules/module-ct5131.md) - CT5131, a curriculum entry in programme 1MAO3. Capstone entry (30 ECTS).
* [Data Visualisation - Online](modules/module-ct5136.md) - CT5136, a curriculum entry in programme 1MAO3. Semester 2, 5 ECTS.
* [Deep Learning - Online](modules/module-ct5145.md) - CT5145, a curriculum entry in programme 1MAO3. Semester 2, 5 ECTS.
* [Future of Artificial Intelligence](modules/module-ct5186.md) - CT5186, a curriculum entry in programme 1MAO3. Semester 2, 5 ECTS.
* [High Performance Computing and Parallel Programming](modules/module-ph504.md) - PH504, a curriculum entry in programme 1MAO3. Semester 2, 5 ECTS.
* [Information Retrieval - Online](modules/module-ct5153.md) - CT5153, a curriculum entry in programme 1MAO3. Semester 1, 5 ECTS.
* [Introduction to Natural Language Processing - Online](modules/module-ct5146.md) - CT5146, a curriculum entry in programme 1MAO3. Semester 1, 5 ECTS.
* [Knowledge Representation - Online](modules/module-ct5188.md) - CT5188, a curriculum entry in programme 1MAO3. Semester 2, 5 ECTS.
* [Principles of Machine Learning - Online](modules/module-ct5170.md) - CT5170, a curriculum entry in programme 1MAO3. Semester 1, 5 ECTS.
* [Programming and Tools for Artificial Intelligence - Online](modules/module-ct5148.md) - CT5148, a curriculum entry in programme 1MAO3. Semester 1, 5 ECTS.
* [Research Skills in Artificial Intelligence](modules/module-ct5144.md) - CT5144, a curriculum entry in programme 1MAO3. Semester 2, 5 ECTS.
* [Statistics for Artificial Intelligence](modules/module-st5001.md) - ST5001, a curriculum entry in programme 1MAO3. Semester 1, 5 ECTS.
* [Tools and Techniques for Large Scale Data Analytics - Online](modules/module-ct5150.md) - CT5150, a curriculum entry in programme 1MAO3. Semester 2, 5 ECTS.

# People

* [Dr Colm O’Riordan](people/person-colm-oriordan.md) - Dr Colm O’Riordan, listed teaching team member, per the 2026 programme extract.
* [Dr Conor Hayes](people/person-conor-hayes.md) - Dr Conor Hayes, listed teaching team member, per the 2026 programme extract.
* [Dr Enda Howley](people/person-enda-howley.md) - Dr Enda Howley, listed teaching team member, per the 2026 programme extract.
* [Dr Jamal Nasir](people/person-jamal-nasir.md) - Dr Jamal Nasir, Programme Director, per the 2026 programme extract.
* [Dr James McDermott](people/person-james-mcdermott.md) - Dr James McDermott, listed teaching team member, per the 2026 programme extract.
* [Dr John McCrae](people/person-john-mccrae.md) - Dr John McCrae, listed teaching team member, per the 2026 programme extract.
* [Dr Matthias Nickles](people/person-matthias-nickles.md) - Dr Matthias Nickles, listed teaching team member, per the 2026 programme extract.
* [Dr Patrick Mannion](people/person-patrick-mannion.md) - Dr Patrick Mannion, listed teaching team member, per the 2026 programme extract.
* [Listed programme teaching team](people/organization-teaching-team.md) - The programme's listed teaching team, as named in the 2026 extract.
* [Listed teaching team member - Dr Colm O’Riordan](people/role-colm-oriordan.md) - Listed teaching team member held by Dr Colm O’Riordan.
* [Listed teaching team member - Dr Conor Hayes](people/role-conor-hayes.md) - Listed teaching team member held by Dr Conor Hayes.
* [Listed teaching team member - Dr Enda Howley](people/role-enda-howley.md) - Listed teaching team member held by Dr Enda Howley.
* [Listed teaching team member - Dr James McDermott](people/role-james-mcdermott.md) - Listed teaching team member held by Dr James McDermott.
* [Listed teaching team member - Dr John McCrae](people/role-john-mccrae.md) - Listed teaching team member held by Dr John McCrae.
* [Listed teaching team member - Dr Matthias Nickles](people/role-matthias-nickles.md) - Listed teaching team member held by Dr Matthias Nickles.
* [Listed teaching team member - Dr Patrick Mannion](people/role-patrick-mannion.md) - Listed teaching team member held by Dr Patrick Mannion.
* [Listed teaching team member - Prof Michael Madden](people/role-michael-madden.md) - Listed teaching team member held by Prof Michael Madden.
* [Listed teaching team member - Professor Paul Buitelaar](people/role-paul-buitelaar.md) - Listed teaching team member held by Professor Paul Buitelaar.
* [Prof Michael Madden](people/person-michael-madden.md) - Prof Michael Madden, listed teaching team member, per the 2026 programme extract.
* [Professor Paul Buitelaar](people/person-paul-buitelaar.md) - Professor Paul Buitelaar, listed teaching team member, per the 2026 programme extract.
* [Programme Director - Dr Jamal Nasir](people/role-jamal-nasir.md) - Programme Director held by Dr Jamal Nasir.

# Sources

* [Technology Ireland ICT Skillnet programme page](sources/source-snapshot-reference-ict-skillnet-page.md) - The Technology Ireland ICT Skillnet partner page for the programme, kept as corroborating evidence.
* [University of Galway online MSc AI course extract (2026)](sources/source-snapshot-reference-uog-extract-2026.md) - The supplied 2026 programme-extract text file, the primary source for most claims in this bundle.
* [University of Galway online MSc AI public page](sources/source-snapshot-reference-uog-public-page.md) - The University of Galway's official public page for the programme.
* [Vaswani et al. (2017), Attention Is All You Need](sources/vaswani-2017-attention-is-all-you-need.md) - The NeurIPS 2017 paper that introduced the Transformer; the external source behind the bundle's self-attention and transformer concepts.

# Issues

* [Apparently unrelated assessment wording](issues/verification-issue-assessment-leakage.md) - Grammar/translation-course wording in the source looks unrelated to an AI programme.
* [Core and optional classification conflict](issues/verification-issue-core-optional-status.md) - The official page labels every module Optional; the partner page classifies 13 of the 14 as Core or Optional.
* [Module-count contradiction](issues/verification-issue-module-count.md) - The source claims 12 taught modules but names 13 topics and lists 15 curriculum entries.
* [Year and credit heading inconsistency](issues/verification-issue-year-credit-heading.md) - The source's 'Year 1 (90 Credits)' heading conflicts with the programme's stated 2-year, 90-ECTS structure.

# Glossary

* [Artificial Intelligence](glossary/glossary-term-artificial-intelligence.md) - The top-level subject-area term this programme develops.

# Playbooks

* [Offline LOKF knowledge acquisition](playbooks/ingestion-playbook-offline-ingestion.md) - How this bundle is refreshed from source material without overwriting personal learning.
* [Knowledge sources map](playbooks/knowledge-sources.md) - Where this bundle's concepts come from, and how to re-check each source.

# Programme Patterns

* [Industry-focused capstone project pattern](programme/project-pattern-capstone-pattern.md) - Timing and focus options for the capstone project (CT5131).
* [Online delivery and workload](programme/delivery-pattern-programme-pattern.md) - How the programme is delivered online and the workload it expects.
* [Programme learning and assessment pattern](programme/assessment-pattern-programme-pattern.md) - The programme's learning activities and assessment forms.
* [Transferable skills](programme/learning-outcome-set-transferable-skills.md) - Programme-level transferable skills the MSc aims to develop.

# Concepts

* [Gradient descent](concepts/gradient-descent.md) - Iteratively move the parameters against the gradient of the objective, θ ← θ − η∇J(θ); the learning rate η sets the step, and too large a step oscillates or diverges.
* [Self-attention](concepts/self-attention.md) - Scaled dot-product attention lets every token weight every other token; the √d_k scaling keeps the softmax from saturating; multi-head attention runs several in parallel.
* [Transformer](concepts/transformer.md) - An encoder-decoder sequence model built from stacked self-attention and position-wise feed-forward layers with positional encodings, and no recurrence or convolution.

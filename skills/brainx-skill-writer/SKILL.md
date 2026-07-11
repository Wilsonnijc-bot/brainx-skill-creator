---
name: brainx-skill-writer
description: Write or revise dense, essential-information-rich BrainX package skills from official BrainX, BrainUnit, BrainState, BrainPy-State, BrainCell, BrainMass, BrainEvent, or BrainTrace HTML sources and user-provided draft material. Use when creating BrainX-native skill bodies, extracting official concept phrasing, organizing progressive-disclosure references, or converting rough BrainX skill drafts into deployable SKILL.md files. Do not use for eval benchmarking; use skill-testor for testing.
---

# BrainX Skill Writer

## Purpose

Create dense BrainX skills that are rich in essential execution information. Assume the agent is already smart, but give it the BrainX-specific concepts, APIs, source-grounded phrases, and canonical workflow it cannot safely infer.

## BrainX Skill Objective

A BrainX skill is successful only if it helps the agent use BrainX packages correctly, cleanly, and intelligently. The agent should learn to solve BrainX modeling and simulation tasks through BrainX APIs, concepts, examples, and documentation.

The objective is to make the agent master BrainX packages such as BrainUnit, BrainState, BrainPy-State, BrainCell, BrainMass, BrainEvent, and BrainTrace.


## Core Rules

- Read the raw draft first. Treat the draft as the primary map of intended concepts, workflow shape, scripts, overlays, and reference needs before opening any HTML.
- Study only relevant official HTML. Use `references/brainx_agent_html_reference.md` to choose the package inventory, then select only pages that directly support the draft's P0 concepts, scripts, or overlays. Do not browse broad navigation, click adjacent pages, or collect background that is not needed for the target skill.
- Ground BrainX concepts and API claims in official HTML. If the user gives only a concept title and best source HTML, open or parse the HTML and select the best official sentence or compact phrase from that source. Do not invent explanations, bootstrap from memory, or write textbook paragraphs.
- Preserve the draft's useful scripts and structure by default. The user's raw draft often already follows the target pattern and contains mostly-ready scripts; keep those scripts unless official HTML or local validation shows a concrete reason to change them.
- Pick direct sentences from the official HTML for concept definitions. If a sentence is too long, lightly trim while preserving meaning.
- Keep `SKILL.md` dense and high-signal: purpose, task boundaries, P0 BrainX concepts, canonical workflow scripts, subpatterns/overlays, and routing to references for variants.
- Use progressive disclosure. Put the canonical path and essential scripts in the body; move variants, long explanations, extended examples, and exhaustive API lists into `references/`.

## Required Working Order

1. Read the user's raw draft, concept title, target skill name, and any supplied source hints completely.
2. Inventory the draft: P0 concepts, existing workflow scripts, subpatterns/overlays, API claims, missing citations, and reference candidates.
3. Open `references/brainx_agent_html_reference.md`, route to the matching package inventory, and select only the official HTML pages that directly match the draft inventory.
4. Visit and study only those relevant HTML pages. For each page, extract the page title, target heading, nearest useful code block, and one compact official phrase for each concept or API rule that must be grounded.
5. Open `references/official-source-extraction.md` when selecting or trimming source language, when HTML is noisy, or when multiple candidate sentences compete.
6. Write or revise the skill only after the draft-first inventory and relevant-HTML pass are complete.
7. Preserve draft scripts unless they conflict with official docs, fail local validation, or obscure the canonical BrainX-native path.

## Target Skill Pattern

Mirror the information architecture used by strong scientific/API skills such as the scikit-learn and cuOpt examples: fundamental concepts first, general workflow scripts next, smaller reusable patterns after that, references last. Match the pattern, not their domain content.

Use this body structure unless the user explicitly requests a different one:

1. **Purpose and boundaries** - one compact paragraph on what the skill helps the agent do and when to use it.
2. **P0 concepts** - the most fundamental BrainX concepts the agent must know before writing code. MUST Favor source-grounded phrases and operational distinctions over broad background.
3. **Common general workflow** - the canonical script or scripts for the main task. Prefer complete, runnable snippets from the draft. After each script, add sharp explanations of the code decisions: why this BrainX object/API is used, what state or units it controls, and what the agent must not accidentally change.
4. **Subpatterns and overlays** - short scripts or fragments for variants, extensions, and edge cases. Each pattern needs a clear "use when" cue and the smallest code needed to adapt the canonical workflow.
5. **Common issues or gotchas** - include only high-impact BrainX-specific failure modes grounded in docs, draft evidence, or verified behavior.
6. **References** - list local reference files and official HTML pages used for deeper lookup. Keep exhaustive details out of the main body.

Do not bury the first executable workflow under long concept exposition. Do not convert the body into a documentation summary. The agent should be able to start from the P0 concepts, run or adapt the canonical script, then layer overlays as needed.

## Script Handling

- Keep the draft's scripts when they are already aligned with the target pattern.
- Prefer complete workflow scripts for the main path and short code fragments for overlays.
- Explain scripts in compact, operational notes rather than line-by-line narration.
- Mark uncertain scripts as needing validation instead of silently normalizing them.
- Keep BrainX names exact: package names, classes, functions, units, mechanisms, transforms, states, modules, channels, ions, regions, locsets, morphology terms, and tutorial labels.

## Official Source Extraction

Open `references/official-source-extraction.md` when selecting or trimming concept language from source HTML, when the HTML is noisy, or when multiple candidate sentences compete.

Required output discipline:

- Concept explanation: one official sentence or compact phrase.
- Source note: official page title or URL when available.
- No invented API claims.
- No broad neuroscience, Python, JAX, or NumPy explanations unless the official BrainX source makes them part of the concept.

## References

- `references/brainx_agent_html_reference.md` - compact router for shared BrainX pages and package-specific inventories.
- `references/braincell_html_reference.md` - BrainCell concepts, tutorials, examples, file formats, and API routes.
- `references/brainstate_html_reference.md` - BrainState tutorials, how-to guides, concepts, examples, API modules, and generated class pages.
- `references/brainpy_state_html_reference.md` - BrainPy-State concepts, native and NEST-compatible modeling paths, API categories, and examples.
- `references/brainmass_html_reference.md` - BrainMass tutorials, how-to guides, model gallery, case studies, and central API pages.
- `references/brainevent_html_reference.md` - BrainEvent tutorials, how-to guides, API categories, and custom-kernel references.
- `references/braintrace_html_reference.md` - BrainTrace quickstarts, tutorials, API pages, advanced guides, and runnable examples.
- `references/official-source-extraction.md` - how to parse HTML and choose source-grounded official wording.

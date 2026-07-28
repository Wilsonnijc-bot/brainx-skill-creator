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
- Study only relevant official HTML. Choose the matching package-specific inventory from `references/`, then select only pages that directly support the draft's P0 concepts, scripts, or overlays. Use `references/brainx_install_spec.md` only for installation or release-history tasks. Do not browse broad navigation, click adjacent pages, or collect background that is not needed for the target skill.
- Ground BrainX concepts and API claims in official HTML. If the user gives only a concept title and best source HTML, open or parse the HTML and select the best official sentence or compact phrase from that source. Do not invent explanations, bootstrap from memory, or write textbook paragraphs.
- Preserve the draft's useful scripts and structure by default. The user's raw draft often already follows the target pattern and contains mostly-ready scripts; keep those scripts unless official HTML or local validation shows a concrete reason to change them.
- Keep `SKILL.md` dense and high-signal: purpose, task boundaries, Underlying principle, individual BrainX concepts+ canonical workflow scripts section, and routing to references for variants.
- Use progressive disclosure. Put the canonical path and essential scripts in the body; move variants, long explanations, extended examples, and exhaustive API lists into `references/`.

## Required Working Order

1. Read the user's raw draft, concept title, target skill name, and any supplied source hints completely.
2. Inventory the draft: P0 concepts, existing workflow scripts, subpatterns/overlays, API claims, missing citations, and reference candidates.
3. Open the matching package-specific HTML reference in `references/` and select only the official HTML pages that directly match the draft inventory. For installation or release-history work, open `references/brainx_install_spec.md`.
4. Visit and study only those relevant HTML pages. For each page, extract the page title, target heading, nearest useful code block, and one compact official phrase for each concept or API rule that must be grounded.
5. Open `references/official-source-extraction.md` when selecting or trimming source language, when HTML is noisy, or when multiple candidate sentences compete.
6. Write or revise the skill only after the draft-first inventory and relevant-HTML pass are complete.
7. Preserve draft scripts unless they conflict with official docs, fail local validation, or obscure the canonical BrainX-native path.

## Target Skill Pattern

Use the BrainState skill pattern as the approximate ideal: organize the body around the package's design logic and the reader's implementation decisions, not around a detached catalogue of P0 definitions. Preserve useful variation when another BrainX package needs a different emphasis or grouping.

Use this body structure unless the package or user request clearly requires a variation:

1. **Purpose and boundary** - one compact section defining what the skill owns, its canonical path, and where adjacent BrainX tasks should be routed.
2. **Underlying principle of `<Package>`** - Write this section as a direct mapping from the user’s domain concepts to the core abstractions of the BrainX package.

   The purpose is to help the agent translate a researcher’s natural description of a brain simulation into the concrete abstractions provided by `<Package>`.

   **Use this pattern:**

   > `<Core abstraction 1 of the BrainX package>` represents `<domain concept 1>`. `<Clarification of its specific role>`.
   >
   > `<Core abstraction 2 of the BrainX package>` represents `<domain concept 2>`. `<Clarification of its specific role>`.
   >
   > `<Core abstraction 3 of the BrainX package>` represents `<domain concept 3>`. `<Clarification of its specific role>`.

   The clarification after each mapping is flexible. Use the verb that states the abstraction’s role most precisely, such as:

   - it stores;
   - it computes;
   - it defines;
   - it marks;
   - it maps;
   - it accumulates;
   - it updates;
   - it constrains;
   - it controls;
   - it generates.

   **For example:**

   - BinaryArray represents neuron spikes as boolean or 0/1 data. It marks which presynaptic neurons are active.
   - Connectivity represents synaptic wiring. It defines which presynaptic neurons project to which postsynaptic neurons, while its weights define each synapse’s sign and strength.
   - Plasticity operators represent activity-dependent changes in synaptic strength. They update stored weights from pre- and postsynaptic activity.

   **Writing rules**

   Start from the concepts that the target user naturally uses, such as neuron spikes, synaptic wiring, membrane state, neural dynamics, traces, plasticity, regions, or numerical integration.

   **For each core package abstraction:**

   - state exactly which domain concept it represents;
   - add one direct clarification of what it stores, defines, computes, updates, or controls.

   **Use short declarative sentences:**

   > `<Abstraction>` represents `<domain concept>`. It `<specific role>`.

   Explain domain meaning, not merely the data structure or API category.

   **Weak:**

   > BinaryArray is an efficient event-oriented array type.

   **Strong:**

   > BinaryArray represents neuron spikes as boolean or 0/1 data. It marks which neurons fired.

   **Weak:**

   > Connectivity provides several dense and sparse representations.

   **Strong:**

   > Connectivity represents synaptic wiring. It defines which neurons communicate and the strength of each connection.

   **Do not add:**

   - a general package introduction;
   - a canonical workflow;
   - decision rules between API variants;
   - API signatures;
   - code examples;
   - implementation history;
   - performance claims;
   - broad or promotional descriptions.

   Use only the core concept-to-abstraction mappings needed to explain what the package represents.

   The final section should be a short sequence of direct statements:

   > Package abstraction → domain concept → specific role

3. **API structure overview** - give a compact map of the main namespaces, API families, or architectural layers and what kind of operation each owns. This is an orientation map, not an exhaustive API inventory.
4. **Concept + canonical workflow sections** - cover the P0 concepts one by one through implementation-centered sections. Every section must follow the **API-Centered Section Style** below: one mental-model sentence, a two-column API table, one canonical example, and a route to advanced references. Closely coupled P0 concepts may share a section when separating them would obscure the real workflow.
5. **Reference routing** - route advanced variants, edge cases, detailed lifecycle behavior, complete catalogues, complex compositions, and uncommon failure modes to the smallest relevant reference file.

Do not create a separate P0-concepts catalogue followed by a distant general-workflow section. The underlying-principle section should establish the package philosophy; the later concept sections should make each P0 concept operational.

### API-Centered Section Style for Concept + Canonical Workflow Sections

This style applies to every **Concept + canonical workflow section** in item 4 above. Each such section should function as a compact workflow guide that gives the reader the essential mental model, the relevant APIs, and one canonical implementation path.

1. **Open with one mental-model sentence.**

   Begin with a single sentence explaining what the mechanism represents, how it behaves, and why it matters in practice.

2. **Present operational guidance in a two-column API table.**

   Give each important API its own row. Each description should state, in this order:

   - when to use the API;
   - what it does;
   - any important propagation, mutation, restoration, validation, or lifecycle behavior;
   - what it returns or what error it raises, when relevant.

3. **Use direct, workflow-oriented language.**

   Prefer formulations such as:

   - "Use for..."
   - "Use after..."
   - "Use when..."
   - "It propagates..."
   - "It returns..."
   - "It restores..."
   - "It raises..."

   Include conceptual explanation only when it changes an implementation decision or API choice.

4. **Include one canonical example, usually minimal.**

   Show only the code required to demonstrate the standard workflow. Remove lengthy setup, repeated explanation, secondary outputs, optional variants, and unrelated assertions. Retain assertions only when they verify an important shape, inferred value, state transition, or invariant.

5. **Move advanced material into references.**

   Put implementation details, edge cases, complete configuration maps, uncommon variants, failure modes, and complex compositions in a linked reference file.

6. **Use this default section structure.**

   ````markdown
   ### Section title

   One-sentence mental model.

   | API | Description |
   |---|---|
   | `api()` | When to use it, what it does, and its important result or behavior. |

   ```python
   # Minimal canonical workflow
   ```

   Open `references/...` for advanced variants, edge cases, and detailed behavior.
   ````

7. **Maximize precision without becoming an API catalogue.**

   Include closely related APIs only when they are necessary to complete the workflow or make an immediate implementation choice. Exclude APIs that do not help the reader execute the section's canonical path.

The overall pattern is:

**one mental model -> one API per row -> one canonical example -> advanced details in references**

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

- `references/brainx_install_spec.md` - official BrainX installation and changelog pages.
- `references/braincell_html_reference.md` - BrainCell concepts, tutorials, examples, file formats, and API routes.
- `references/brainstate_html_reference.md` - BrainState tutorials, how-to guides, concepts, examples, API modules, and generated class pages.
- `references/brainpy_state_html_reference.md` - BrainPy-State concepts, native and NEST-compatible modeling paths, API categories, and examples.
- `references/brainmass_html_reference.md` - BrainMass tutorials, how-to guides, model gallery, case studies, and central API pages.
- `references/brainevent_html_reference.md` - BrainEvent tutorials, how-to guides, API categories, and custom-kernel references.
- `references/braintrace_html_reference.md` - BrainTrace quickstarts, tutorials, API pages, advanced guides, and runnable examples.
- `references/official-source-extraction.md` - how to parse HTML and choose source-grounded official wording.

## Required Writing Reference

Before writing or revising any BrainX reference, open and follow
`references/referencewriting.md`.

# BrainX Benchmark Analyzer

Analyze benchmark results to surface patterns and anomalies across BrainX skill-bundle eval runs.

## Role

Review all benchmark run results and generate notes that help the user understand whether the BrainX skills improved API correctness, documentation use, runnable simulation quality, and avoidance of generic NumPy/JAX substitutes.

## Inputs

You receive these parameters in your prompt:

- **benchmark_data_path**: Path to the in-progress `benchmark.json`.
- **skill_path**: Path to the BrainX skill or bundle being benchmarked.
- **output_path**: Where to save notes as a JSON array of strings.

## Process

### Step 1: Read Benchmark Data

1. Read `benchmark.json`.
2. Note configurations such as `with_skill_bundle`, `without_brainx_skills`, or `old_skill_bundle`.
3. Understand the `run_summary` aggregates already calculated.

### Step 2: Analyze Per-Expectation Patterns

For each expectation across all runs:

- Does it always pass in both configurations? It may not differentiate skill value.
- Does it always fail in both configurations? It may be broken, too broad, or beyond the current agent capability.
- Does it pass with the BrainX skill bundle but fail in the baseline? The skill likely adds value.
- Does it fail with the BrainX skill bundle but pass in the baseline? The skill may be hurting.
- Is it highly variable? The expectation or eval may be flaky.

### Step 3: Analyze BrainX-Specific Patterns

Look for recurring BrainX failure modes:

- Generic NumPy, JAX, or standalone neuroscience code replacing the BrainX core.
- Hallucinated BrainX APIs, parameters, modules, channels, ions, mechanisms, or selectors.
- Missing BrainUnit quantities where units matter.
- BrainState state or module registration mistakes.
- BrainCell morphology, region, locset, paint, or place semantics used incorrectly.
- Outputs that mention BrainX but do not follow relevant documentation or examples.

### Step 4: Analyze Metrics Patterns

Look at time, tokens, tool calls, and errors:

- Does the skill significantly increase execution time?
- Is there high variance in resource usage?
- Are there outlier runs that skew the aggregates?
- Did documentation lookup improve correctness enough to justify the extra cost?

### Step 5: Generate Notes

Write observations as a JSON array of strings. Each note should:

- state a specific observation;
- cite the eval, configuration, or expectation involved;
- stay grounded in benchmark data;
- help the user interpret what aggregate metrics hide.

Examples:

- "The BrainState module-registration expectation passed in all with_skill_bundle runs and failed in all without_brainx_skills runs."
- "The baseline repeatedly implemented a standalone NumPy HH model when the prompt asked for a BrainCell HH example."
- "The region/locset eval is high variance: one with_skill_bundle run used paint/place correctly, while another invented selector names."
- "The BrainX skill adds 20s average execution time but improves pass rate by 45 percentage points."

## Output Format

Save notes to `{output_path}`:

```json
[
  "The BrainState module-registration expectation passed in all with_skill_bundle runs and failed in all without_brainx_skills runs.",
  "The baseline repeatedly implemented a standalone NumPy HH model when the prompt asked for a BrainCell HH example."
]
```

## Guidelines

- Report what the data shows.
- Be specific about evals, expectations, configurations, or runs.
- Do not make unsupported causal claims.
- Do not suggest skill edits in this analyzer pass; leave improvement decisions to the main skill-creator loop after user review.

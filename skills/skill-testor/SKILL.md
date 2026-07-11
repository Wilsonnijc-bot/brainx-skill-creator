---
name: skill-testor
description: Test and benchmark existing BrainX package skills or skill bundles. Use when evaluating whether skills correctly guide agents to use BrainCell, BrainState, BrainUnit, BrainMass, or related BrainX APIs for simulations, modeling, debugging, tutorials, evals, and avoidance of generic NumPy/JAX substitutes. Do not use for creating, writing, or revising skills.
---

# Skill Testor

A BrainX-focused skill evaluator for testing whether BrainX skills make agents use BrainX packages correctly.

## BrainX Skill Testing Objective

The purpose of this skill is to test and benchmark existing BrainX skills or skill bundles.

A BrainX skill is successful only if it helps the agent use BrainX packages correctly, cleanly, and intelligently. The agent should learn to solve BrainX modeling and simulation tasks through BrainX APIs, concepts, examples, and documentation.

The objective is not to produce generic Python code. The objective is to make the agent master BrainX packages such as BrainCell, BrainState, BrainUnit, BrainMass, and related BrainX tools.

When a user asks for a BrainX simulation, model, tutorial, debugging help, or code example, the resulting skill should guide the agent toward BrainX-native implementation. NumPy, SciPy, matplotlib, or JAX helper code is acceptable for plotting, data preparation, lightweight checks, or surrounding utilities. They are not acceptable as replacements for the BrainX modeling/simulation core when the task is explicitly about BrainX.

At a high level, use this loop:

- Use an existing user-prepared `evals/evals.json` when present.
- Create a small `evals/evals.json` template only when no eval file exists.
- Run each eval with the BrainX skill bundle and against a baseline.
- Grade outputs with objective BrainX expectations.
- Aggregate benchmark results and open the eval viewer.
- Read user feedback and report findings.
- Stop after reporting results. Do not write, revise, or optimize the tested skill unless the user switches to a separate skill-writing workflow.

## Running and Evaluating Test Cases

This section is one continuous sequence. Do not stop partway through.

`evals/evals.json` is the global test set for the BrainX skill or skill bundle. It may already exist because the user prepared it. If it exists, read it and use it. If it does not exist, create a small starter template and ask the user to review it before running a large batch.

Bundle-compatible `evals/evals.json` template:

```json
{
  "skill_name": "brainx-skill-bundle",
  "skill_names": [
    "brainx-state-management",
    "brainx-module-building"
  ],
  "skill_paths": [
    "skills/brainx-state-management",
    "skills/brainx-module-building"
  ],
  "evals": [
    {
      "id": 1,
      "eval_name": "descriptive-name-here",
      "prompt": "User's task prompt",
      "expected_output": "Description of expected result",
      "expectations": [
        "Expectation 1",
        "Expectation 2"
      ],
      "files": []
    }
  ]
}
```

Use a workspace beside the skill or bundle:

```text
<brainx-skill-name-or-bundle>-workspace/
  iteration-1/
    state-management-module-registration/
      eval_metadata.json
      with_skill_bundle/
        outputs/
        transcript.md
        timing.json
        grading.json
      without_brainx_skills/ OR old_skill_bundle/
        outputs/
        transcript.md
        timing.json
        grading.json
    benchmark.json
    benchmark.md
    review.html
    feedback.json
```

Each eval directory contains one `with_skill_bundle/` run and one baseline run. For a new skill or bundle, the baseline directory is `without_brainx_skills/`. For improving an existing skill or bundle, the baseline directory is `old_skill_bundle/`.

### Step 1: Spawn all runs

For each test case, spawn the BrainX skill run and its baseline in the same turn so results finish around the same time.

BrainX skill bundle run:

```text
Execute this task:
- Skill paths: <path-to-skill-1>, <path-to-skill-2>, ...
- Task: <eval prompt>
- Input files: <eval files if any, or "none">
- Save outputs to: <workspace>/iteration-<N>/<eval-name>/with_skill_bundle/outputs/
- Outputs to save: <what the user cares about, such as runnable script, markdown explanation, notebook, generated plots, fixed code, or exported files>
```

Write an `eval_metadata.json` for each eval directory. `eval_metadata.json` is a local snapshot for one eval in one iteration. It contains the copied prompt, expected output, skill bundle info, files, and expectations for later grading. The grader does not grade `eval_metadata.json` alone. It uses the expectations from it together with the run-specific transcript and output files.

Use this per-eval metadata shape:

```json
{
  "eval_id": 0,
  "eval_name": "descriptive-name-here",
  "prompt": "The user's task prompt",
  "expected_output": "Description of expected result",
  "skill_name": "brainx-skill-bundle",
  "skill_names": [
    "brainx-state-management",
    "brainx-module-building"
  ],
  "skill_paths": [
    "skills/brainx-state-management",
    "skills/brainx-module-building"
  ],
  "expectations": [],
  "files": []
}
```

### BrainX baseline rule

For a new BrainX skill or skill bundle, the baseline run receives the same user prompt but no BrainX skill instructions. The BrainX packages may still be installed in the environment. The baseline should test what the agent does without these custom BrainX skills, not whether the environment has BrainX installed.

When comparing against a previous existing BrainX skill or skill bundle, use the old version as the baseline and save outputs under `old_skill_bundle/outputs/`.

The purpose of the baseline is to test whether the BrainX skill improves BrainX API correctness, documentation use, runnable simulation quality, and avoidance of generic NumPy/JAX substitutes.

### Step 2: While runs are in progress, draft expectations

Do not just wait for runs to finish. If expectations already exist in `evals/evals.json`, review them and explain what they check. If expectations are missing or weak, draft stronger ones and update both `evals/evals.json` and the relevant `eval_metadata.json` files.

Good BrainX expectations are objective and discriminating. They should catch whether the agent actually used BrainX correctly.

Prefer expectations that verify:

- the correct BrainX package is used for the task;
- relevant BrainX concepts are represented correctly;
- the produced code is runnable or close to runnable;
- units, state objects, module registration, mechanisms, morphology selectors, channels, ions, or integration methods are not hallucinated;
- the solution follows BrainX examples or documentation rather than inventing unrelated APIs;
- NumPy/JAX are used only as helpers, not as a replacement for the BrainX core;
- the explanation is technically aligned with the relevant BrainX documentation page.

Weak expectations:

- "Mentions BrainX."
- "Includes code."
- "Explains the concept."
- "Uses Python."

Stronger expectations:

- "The script uses BrainState Module and registered State objects rather than storing learnable parameters as plain NumPy arrays."
- "The simulation uses BrainCell/BrainUnit quantity-aware values for electrophysiology parameters instead of unitless arbitrary floats."
- "The morphology workflow uses BrainCell region/locset or paint/place semantics when assigning mechanisms spatially."
- "The output does not implement a standalone HH model from scratch when the task asks for a BrainCell HH neuron example."

### Step 3: As runs complete, capture timing data

When each subagent task completes, save the notification timing data immediately to `timing.json` in the run directory:

```json
{
  "total_tokens": 84852,
  "duration_ms": 23332,
  "total_duration_seconds": 23.3
}
```

This is the only opportunity to capture this data. Process each notification as it arrives.

### Step 4: Grade, aggregate, and launch the viewer

Once all runs are done:

1. Grade each run. Spawn a grader subagent, or grade inline, using `agents/grader.md`. The grader reads the expectations, transcript, and output files. For checks that can be automated, write and run a script instead of eyeballing them.

Each run should produce `grading.json`:

```json
{
  "expectations": [
    {
      "text": "The output uses BrainState Module registration correctly.",
      "passed": true,
      "evidence": "Specific evidence from transcript or output files."
    }
  ],
  "summary": {
    "passed": 1,
    "failed": 0,
    "total": 1,
    "pass_rate": 1.0
  }
}
```

Keep the required fields `text`, `passed`, and `evidence`. Do not rename them to `name`, `met`, `details`, or anything else.

2. Aggregate into a benchmark:

```bash
python -m scripts.aggregate_benchmark <workspace>/iteration-N --skill-name <name>
```

This produces `benchmark.json` and `benchmark.md` with pass rate, time, and tokens for each configuration, with mean plus standard deviation and the delta. Put each `with_skill_bundle` version before its baseline counterpart.

3. Do an analyst pass. Read the benchmark data and surface patterns the aggregate stats might hide. See `agents/analyzer.md` for signs of weak expectations, high-variance evals, and time/token tradeoffs.

4. Launch the viewer with both qualitative outputs and quantitative data:

```bash
nohup python <skill-creator-path>/eval-viewer/generate_review.py \
  <workspace>/iteration-N \
  --skill-name "my-brainx-skill-bundle" \
  --benchmark <workspace>/iteration-N/benchmark.json \
  > /dev/null 2>&1 &
VIEWER_PID=$!
```

For iteration 2+, also pass `--previous-workspace <workspace>/iteration-<N-1>`.

Use `generate_review.py`; do not write custom HTML.

5. Tell the user that the viewer is ready. The Outputs tab lets them click through each test case and leave feedback. The Benchmark tab shows quantitative comparison. Ask them to return when review is done.

### What the user sees in the viewer

The Outputs tab shows one test case at a time:

- Prompt: the task that was given
- Output: the files the skill produced, rendered inline where possible
- Previous Output: iteration 2+ output, collapsed by default
- Formal Grades: expectation pass/fail details, collapsed by default
- Feedback: a textbox that auto-saves as they type
- Previous Feedback: iteration 2+ comments from the last review

The Benchmark tab shows pass rates, timing, token usage, per-eval breakdowns, and analyst observations.

### Step 5: Read feedback

When the user tells you review is complete, read `feedback.json`:

```json
{
  "reviews": [
    {
      "run_id": "state-management-module-registration-with_skill_bundle",
      "feedback": "the code used plain arrays instead of BrainState State objects",
      "timestamp": "2026-06-29T00:00:00Z"
    }
  ],
  "status": "complete"
}
```

Empty feedback means the user thought that run was fine. Summarize specific complaints and failed expectations. Kill the viewer server when finished:

```bash
kill $VIEWER_PID 2>/dev/null
```

## BrainX Truthbase Discipline

When grading BrainX technical correctness or strengthening eval expectations, first consult:

`references/brainx_agent_html_reference.md`

Use this reference to choose the relevant BrainX documentation page before grading or explaining technical claims.

Do not overclick. Open only the documentation page directly relevant to the concept being tested. Click deeper only when the selected page is insufficient to verify the specific technical claim.

Use the truthbase when:

- checking BrainX API instructions;
- verifying a technical claim;
- grading examples involving BrainCell, BrainState, BrainUnit, BrainMass, morphology, channels, ions, mechanisms, integration, state, modules, transforms, training, or simulations;
- resolving uncertainty from eval transcripts;
- explaining why an expectation failed.

Do not use the truthbase just to decorate the skill with links. Its purpose is to prevent hallucinated BrainX knowledge and keep skills grounded in the current documentation.

## Reporting Findings

After reviewing outputs, feedback, transcripts, and benchmark data, report findings about the tested BrainX skill or skill bundle.

Generalize from failures. If the agent used generic code, hallucinated an API, misunderstood a concept, or skipped the relevant BrainX package, identify the missing BrainX concept or API pattern and cite the failed expectation or transcript evidence.

Do not patch the skill from this workflow. If the user wants changes after testing, treat that as a separate skill-writing task and use the appropriate skill-creation workflow.

When a test fails because the agent used generic code, hallucinated a BrainX API, misunderstood a BrainX concept, or skipped the relevant BrainX package:

1. read the failed transcript and output;
2. identify the missing BrainX concept or API pattern;
3. open `references/brainx_agent_html_reference.md`;
4. choose the relevant BrainX documentation page;
5. verify the correct concept, API, or example from the documentation;
6. report the issue and the evidence;
7. recommend the expectation that would catch the same failure in future evals.

The goal is not to make one eval pass. The goal is to measure whether the tested skill makes the agent more fluent in BrainX.

## BrainX Eval Types

Use a mix of these eval types:

1. Concept explanation evals  
   Test whether the skill explains BrainX concepts precisely.

2. Minimal runnable script evals  
   Test whether the agent can produce a small working BrainX example.

3. Debugging evals  
   Test whether the agent can fix wrong BrainX code without replacing it with generic code.

4. API selection evals  
   Test whether the agent chooses BrainCell vs BrainState vs BrainUnit correctly.

5. Simulation workflow evals  
   Test whether the agent follows the correct modeling to initialization to integration/simulation to output/plot flow.

6. Anti-hallucination evals  
   Test whether the agent verifies BrainX APIs instead of inventing names.

7. Bundle coordination evals  
   Test whether multiple BrainX skills work together cleanly, such as BrainState state management plus BrainState module building.

## Reference Files

The `agents/` directory contains specialized instructions:

- `agents/grader.md` - evaluate BrainX expectations against outputs
- `agents/analyzer.md` - analyze benchmark patterns and failure modes

The `references/` directory contains:

- `references/brainx_agent_html_reference.md` - routing truthbase for selecting relevant BrainX documentation pages
- `references/schemas.md` - JSON structures for evals, metadata, grading, benchmark, review, and feedback files

Core loop:

- Test an existing BrainX skill or bundle.
- Use an existing `evals/evals.json`, or create a small starter template only when none exists.
- Run with-skill-bundle and baseline evals.
- Grade with BrainX expectations.
- Aggregate benchmark results and launch `eval-viewer/generate_review.py`.
- Read user feedback and failed transcripts.
- Consult the BrainX truthbase when grading or explaining technical failures.
- Report findings without writing or revising the tested skill.

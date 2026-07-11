# Skill Testor JSON Schemas

This document defines the JSON shapes used by `skill-testor`.

## evals/evals.json

`evals/evals.json` is the global test set for one BrainX skill or a BrainX skill bundle. Users may prepare this file themselves. Create a small starter template only when it does not already exist.

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

Fields:

- `skill_name`: Bundle or single-skill display name.
- `skill_names`: Names of the BrainX skills under test.
- `skill_paths`: Paths to the BrainX skills under test.
- `evals[].id`: Stable numeric eval identifier.
- `evals[].eval_name`: Human-readable eval name. Use it for the descriptive eval directory.
- `evals[].prompt`: Task prompt to execute.
- `evals[].expected_output`: Human-readable description of success.
- `evals[].expectations`: Objective checks used by the grader.
- `evals[].files`: Optional input files.

## eval_metadata.json

`eval_metadata.json` is a local snapshot for one eval in one iteration. It contains copied data from `evals/evals.json` so grading remains reproducible even if the global eval set changes later.

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

The grader does not grade `eval_metadata.json` alone. It uses the expectations from this file together with the run-specific transcript and output files.

## timing.json

`timing.json` stores subagent timing data for one run.

```json
{
  "total_tokens": 84852,
  "duration_ms": 23332,
  "total_duration_seconds": 23.3
}
```

Save this data immediately when the run completion notification arrives.

## grading.json

`grading.json` is produced for each run directory after grading the transcript and outputs.

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

Required fields:

- `expectations[].text`: Original expectation text.
- `expectations[].passed`: Boolean pass/fail result.
- `expectations[].evidence`: Specific evidence from transcript or output files.
- `summary.passed`: Number of passing expectations.
- `summary.failed`: Number of failing expectations.
- `summary.total`: Total expectations graded.
- `summary.pass_rate`: Fraction passed.

Optional grader fields may include execution metrics, timing, verified claims, user notes, and eval feedback. Keep the required fields unchanged because the benchmark aggregator and viewer depend on them.

## benchmark.json

`benchmark.json` summarizes all runs in an iteration.

```json
{
  "metadata": {
    "skill_name": "brainx-skill-bundle",
    "skill_paths": [
      "skills/brainx-state-management",
      "skills/brainx-module-building"
    ],
    "timestamp": "2026-06-29T00:00:00Z",
    "evals_run": [
      1
    ],
    "runs_per_configuration": 1
  },
  "runs": [
    {
      "eval_id": 1,
      "eval_name": "state-management-module-registration",
      "configuration": "with_skill_bundle",
      "run_number": 1,
      "result": {
        "pass_rate": 1.0,
        "passed": 2,
        "failed": 0,
        "total": 2,
        "time_seconds": 23.3,
        "tokens": 84852,
        "tool_calls": 0,
        "errors": 0
      },
      "expectations": [
        {
          "text": "The script uses BrainState Module and registered State objects.",
          "passed": true,
          "evidence": "The output defines a Module subclass and registers State fields."
        }
      ],
      "notes": []
    }
  ],
  "run_summary": {
    "with_skill_bundle": {
      "pass_rate": {
        "mean": 1.0,
        "stddev": 0.0,
        "min": 1.0,
        "max": 1.0
      },
      "time_seconds": {
        "mean": 23.3,
        "stddev": 0.0,
        "min": 23.3,
        "max": 23.3
      },
      "tokens": {
        "mean": 84852,
        "stddev": 0.0,
        "min": 84852,
        "max": 84852
      }
    },
    "without_brainx_skills": {
      "pass_rate": {
        "mean": 0.0,
        "stddev": 0.0,
        "min": 0.0,
        "max": 0.0
      },
      "time_seconds": {
        "mean": 18.0,
        "stddev": 0.0,
        "min": 18.0,
        "max": 18.0
      },
      "tokens": {
        "mean": 40000,
        "stddev": 0.0,
        "min": 40000,
        "max": 40000
      }
    },
    "delta": {
      "pass_rate": "+1.00",
      "time_seconds": "+5.3",
      "tokens": "+44852"
    }
  },
  "notes": [
    "The baseline run used a standalone NumPy model where the BrainX skill run used BrainState Module registration."
  ]
}
```

The viewer reads field names exactly. Use `configuration`, not `config`. Put pass-rate fields under `result`.

## feedback.json

`feedback.json` stores user review comments from the eval viewer.

```json
{
  "reviews": [
    {
      "run_id": "state-management-module-registration-with_skill_bundle",
      "feedback": "The code still needs a clearer BrainUnit quantity example.",
      "timestamp": "2026-06-29T00:00:00Z"
    }
  ],
  "status": "complete"
}
```

Empty feedback means the user thought the run was fine.

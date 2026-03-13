# Prompt to Generate a Repo-Specific `autoexp_program.md`

You are in a machine learning repository.

Your task is to create a repo-specific `autoexp_program.md` for autonomous training experimentation, based on the instruction below, and a provided template located at https://github.com/wizwand/autoexp/blob/master/program_template.md

Use the provided generic template as the required structure, but adapt every section to this repository after inspecting the codebase.

## What you must do

1. Read the repository context:
   - README
   - training scripts
   - model files
   - config files
   - evaluation code
   - logging / checkpoint code
   - pyproject / requirements / Makefile / shell scripts
2. Infer:
   - the main training command
   - the main validation metric
   - whether higher or lower is better
   - the effective run budget per experiment
   - what files are safe to modify
   - what files must remain fixed
   - how to extract results from logs or artifacts
   - likely crash / timeout conditions
3. Produce a complete `autoexp_program.md` tailored to this repo.

## Requirements for the generated `autoexp_program.md`

- It must be concrete, not generic
- It must name actual files and commands from this repo
- It must define a baseline run first
- It must define a clear keep/discard experiment loop
- It must specify how to log results into `autoexp_results.tsv`
- It must define the primary metric and any secondary constraints
- It must explain what the agent can and cannot modify
- It must preserve evaluation integrity
- It must avoid adding dependencies unless already supported
- It must prefer small, attributable experiments over chaotic rewrites

## Important adaptation rules

- If the repo prints a metrics summary, document that exact format
- If the repo writes metrics to JSON/CSV/TensorBoard/W&B/local files, define the extraction procedure clearly
- If the repo has multiple train paths, choose the default one and explain why
- If the repo uses configs, specify which config keys are tunable
- If the repo has no explicit time budget, define a practical per-run budget based on existing training structure
- If the repo has no single obvious validation metric, choose the metric most aligned with model quality and explain the choice
- If the repo supports distributed training, default to the simplest reliable local mode unless the repo clearly expects otherwise
- If the repo includes benchmark/eval harness files, treat them as read-only unless explicitly intended to be changed
- If some details are ambiguous, document assumptions instead of leaving placeholders

## Input material

You are given:

1. A generic template for an autonomous ML experimentation program.
2. A target repository containing one or more training scripts.

## Objectives

- Infer the main training entrypoint(s)
- Infer the primary validation metric(s) and whether higher or lower is better
- Infer any time budget, step budget, epoch budget, or early stopping logic already present
- Infer what files are safe to modify vs unsafe to modify
- Infer the expected output/log format, or define a robust extraction method
- Infer how to run the baseline experiment
- Infer where results should be logged
- Infer likely crash modes (OOM, NaN, timeout, import errors, bad config, shape mismatch, etc.)
- Produce a clear experimentation loop that a coding agent can follow repeatedly

## Rules

- Prefer using the repo’s existing training/evaluation pipeline instead of inventing a new one
- Do not add new dependencies unless the repo already supports them
- Do not change datasets, labels, or evaluation logic unless the repo clearly allows it
- Do not change infrastructure, deployment, or unrelated product code
- Do not be aggressive about modifying preprocessing, evaluation, or benchmark harnesses unless the repo explicitly treats them as tunable
- Preserve reproducibility and avoid hidden state
- If the repo is ambiguous, document assumptions explicitly in `autoexp_program.md`
- If multiple training scripts exist, choose the most likely primary one and mention alternatives briefly
- If no reliable metric is found, define the best available optimization target and explain how to extract it
- If no explicit time budget exists, define one using the repo’s natural structure, such as one eval cycle, fixed steps, fixed epochs, or a user-configurable max wall-clock budget

## Files to inspect first

Inspect at least:
- README and docs
- train / finetune / pretrain / run scripts
- config files
- evaluation code
- logging code
- shell scripts / Makefile / pyproject / requirements
- checkpointing and CLI arg definitions

## Output requirements

- Write only the final `program.md`
- Do not output commentary
- Do not leave placeholder text like `<fill me>`
- Be specific enough that a coding agent can follow it immediately

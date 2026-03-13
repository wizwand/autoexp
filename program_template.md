# Autonomous Training Optimization Program

This document defines how an autonomous coding agent should iteratively improve a training pipeline in this repository.

## Goal

Improve the training setup to achieve the best possible validation outcome under the repository’s existing constraints.

Primary metric to optimize:
- `<metric_name>`: `<higher|lower>` is better

Secondary metrics / constraints:
- `<metric_name>`: `<higher|lower>` is better
- training stability must be preserved
- run must complete successfully
- memory usage should remain reasonable
- complexity should only increase when justified by meaningful gains

Per-run budget:
- wall clock budget: `<e.g. 10 min / 1 epoch / 5k steps / until first eval>`
- if the code already defines a fixed budget, follow it exactly
- if not, use the smallest budget that still yields a meaningful validation signal

Success criteria:
- improve the primary metric
- do not crash
- do not exceed the run budget
- prefer simpler and cleaner changes when performance is similar

## Repository scope

Read these files first for context:
- `<README / docs / primary train script / config / evaluation script / runner / dataloader / model / utils>`

Primary training entrypoint:
- ``<command to run baseline>``

Primary evaluation path:
- `<how validation is run / where metric comes from>`

Artifacts and logs to inspect:
- `<stdout / log file / tensorboard / wandb disabled or enabled / checkpoints / metrics json / csv>`

## What can be modified

The agent may modify:
- `<training script files>`
- `<model definition files>`
- `<optimizer / scheduler settings>`
- `<hyperparameters>`
- `<batch size / grad accumulation>`
- `<model depth / width / hidden size / heads / dropout / regularization>`
- `<loss formulation if it is clearly part of training and not benchmark tampering>`
- `<augmentation / sampling / curriculum / precision / compilation / checkpointing strategy if safe>`

## What cannot be modified

The agent must NOT:
- modify the benchmark target or validation ground truth
- alter held-out evaluation data in a way that invalidates comparison
- install new packages unless already present in repo support
- rewrite unrelated infrastructure or application code
- silently change the meaning of the task
- bypass evaluation
- make changes that require human secrets or new external services
- leave the repo in a broken state

Do NOT modify:
- dataset contents
- label generation
- evaluation harness
- reporting logic used to determine the official metric

## Baseline run

The first experiment must always be the baseline:
1. run the training pipeline exactly as-is
2. capture the output
3. extract the primary metric and key runtime stats
4. record the result in the experiment log
5. use this baseline as the comparison point for all subsequent changes

## Output format

At the end of each run, extract or derive the following fields when available:

```text
primary_metric:     <value>
secondary_metric:   <value>
training_seconds:   <value>
total_seconds:      <value>
peak_vram_mb:       <value>
num_steps:          <value>
num_epochs:         <value>
throughput:         <value>
num_params_M:       <value>
checkpoint_path:    <value>
status:             <ok|crash|timeout|oom|nan>
```

If the training script already prints a summary block, parse that.
If not, extract the fields from logs, metrics files, or evaluation outputs.
If some fields do not exist, omit them or mark them as `NA`.

## Logging results

Log every experiment to:
- `autoexp_results.tsv`

Use tab-separated format with this header:

```text
commit	primary_metric	status	description	runtime_sec	peak_vram_mb	notes
```

Definitions:
- `commit`: short git commit hash for the experiment
- `primary_metric`: numeric value, or `NA` if unavailable
- `status`: `keep`, `discard`, `crash`, `timeout`, `oom`, or `nan`
- `description`: short description of the idea tested
- `runtime_sec`: total runtime in seconds if available
- `peak_vram_mb`: peak memory if available
- `notes`: brief reason for keep/discard/crash

Do not commit `autoexp_results.tsv` unless the repo explicitly wants experiment logs tracked.

## Search space hints

High-priority knobs:
- `<learning rate>`
- `<batch size / grad accumulation>`
- `<scheduler>`
- `<dropout / regularization>`
- `<model width / depth>`
- `<optimizer>`

Low-priority or risky knobs:
- `<data pipeline changes>`
- `<loss changes>`
- `<architecture rewrites>`

Known repo-specific traps:
- `<OOM risk area>`
- `<config mismatch area>`
- `<evaluation file must remain fixed>`

## Experimentation principles

- Always make one coherent change per experiment, or one tightly-related bundle of changes
- Prefer changes that are easy to reason about
- Prefer improvements that hold under the fixed evaluation protocol
- Prefer simplifications when performance is equal
- Revert failed or non-improving experiments
- Keep good changes and advance from the best known commit
- Avoid large speculative rewrites unless smaller changes are exhausted

Examples of reasonable experiments:
- learning rate / warmup / scheduler adjustments
- optimizer changes already supported by repo
- batch size vs grad accumulation tradeoffs
- model size tradeoffs under same time budget
- dropout / weight decay / label smoothing changes
- precision / compilation / fused kernels already available
- dataloader efficiency improvements that do not alter evaluation validity
- architecture tweaks inside the allowed training/model files

Examples of unreasonable experiments:
- changing the validation set to look better
- skipping evaluation
- hardcoding outputs
- adding hidden test leakage
- major refactors unrelated to training quality
- changing many unrelated knobs at once with no attribution

## The experiment loop

LOOP FOREVER:

1. Inspect current git branch and current best result.
2. Choose one promising experiment.
3. Edit only the allowed files.
4. Commit the experiment with a short message.
5. Run the experiment with output redirected to a log file, e.g.:
   - ``<run command> > run.log 2>&1``
6. Extract the primary metric and key runtime stats.
7. If the run crashed or produced invalid results:
   - inspect the tail of the log
   - determine whether the failure is trivial to fix
   - retry only if the fix is small and the experiment idea still makes sense
8. Record the result in `autoexp_results.tsv`.
9. If the primary metric improved:
   - keep the commit
   - mark status `keep`
   - advance from this commit
10. If the result is worse, equal, invalid, or too costly:
   - mark status `discard` or failure status
   - revert to the prior best commit
11. Repeat until the budget, search space, or improvement potential is exhausted.

## Failure handling

Treat these as failures unless quickly fixable:
- Python exception
- OOM
- divergence / NaN / Inf
- shape mismatch
- missing files
- timeout beyond allowed budget
- metric missing or unparsable

Timeout rule:
- if a run exceeds `<timeout threshold>`, terminate it and mark as `timeout`

Crash handling:
- small typo/import/config mistakes may be fixed and retried
- fundamentally bad ideas should be logged and skipped

## Branching and reproducibility

Recommended branch naming:
- `autoexp/<date-or-tag>`

For each run:
- keep code changes committed
- avoid mixing unrelated experiments
- preserve the best known state
- avoid untracked modifications except logs/artifacts explicitly allowed

## Final instruction to the agent

You are acting as an autonomous training researcher.
Your job is to make disciplined, incremental, well-logged improvements to the training pipeline.
Do not chase novelty for its own sake.
The best result is the one that improves the primary metric reliably, stays within budget, and keeps the codebase clean.

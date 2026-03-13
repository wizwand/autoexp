<p align="center">
<img width="300" height="300" alt="wizwand autoexp" src="https://github.com/user-attachments/assets/7154cfa2-68d9-46d9-a9ee-a8e535e2933e" />
</p>

# AutoExp by Wizwand

Wizwand AutoExp (short for "auto experiments"), inspired by [karpathy/autoresearch](https://github.com/karpathy/autoresearch), is a one-line setup that turns any AI/ML training project into an autoresearch-style workflow, enabling AI coding agents (such as Codex or Claude Code) to run experiments automatically and optimize them iteratively.

## One-time setup

In your project directory, send your coding agent (such as Codex or Claude Code) this message:
```
Read https://github.com/wizwand/autoexp/blob/main/make_autoexp.md and follow the instructions to create `autoexp_program.md` in the current directory.
```
What it does under the hood:
- Your coding agent will scan the project directory and infer the training command, evaluation metric, and other details from the codebase.
- It will then create a `autoexp_program.md` file that defines how to run experiments automatically.

## Run automated experiments

To kick off automated experiments, send your coding agent (such as Codex or Claude Code) this message:
```
Hi, have a look at autoexp_program.md and let's kick off the experiments!
```
What it does under the hood:
- Your coding agent will read `autoexp_program.md` and understand the training process and configuration.
- It will run the baseline experiment and record the results in `autoexp_results.tsv`.
- It will then iteratively run experiments, keeping the best-performing ones and discarding the rest.

## Customization

To customize the experiment setup, edit `autoexp_program.md` freely to adjust the configuration as needed.

## Examples

- Autoresearch but autonomous experiment flow configured by AutoExp: [wizwand/autoresearch-example/program.md](https://github.com/wizwand/autoresearch-example/blob/master/program.md)

## Community

- Website: [wizwand.com](https://www.wizwand.com)
- Follow on X: [@wizwand_team](https://x.com/wizwand_team)
- Discord: [Join our Discord](https://discord.com/invite/TshwQ2UySx)

## License

MIT

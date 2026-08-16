# Rubric

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![CI](https://github.com/byteshiftlabs/rubric/actions/workflows/validate-metadata.yml/badge.svg)

A set of reusable prompt guides for AI-assisted software work.

## What this is

A collection of short guides — one per task (debugging, code review, release checks, and so on) — plus rules that stay the same no matter which task you're doing. You pick only the guides a task needs, instead of loading the whole repo every time.

## Quick start

1. Pick one concrete task. For example: "debug a regression" or "review this PR."
2. Load [core/shared-contract.md](core/shared-contract.md) — the rules that apply to every task.
3. Load one workflow guide that matches the task, for example [development/debugging.md](development/debugging.md) or [development/code-review.md](development/code-review.md).
4. If you're tuning the prompt for a specific model, also load [development/model-adapters.md](development/model-adapters.md).
5. Run the task with just those files. Don't load anything else unless the task changes.

Good starting combinations:

- Debugging: [core/shared-contract.md](core/shared-contract.md) + [development/debugging.md](development/debugging.md)
- Code review: [core/shared-contract.md](core/shared-contract.md) + [development/code-review.md](development/code-review.md)
- Release check: [core/shared-contract.md](core/shared-contract.md) + [development/repo-integrity-audit.md](development/repo-integrity-audit.md)

## Rules for using this repo

- Load a small, task-sized bundle. Don't load every guide by default.
- Keep `core/shared-contract.md` stable across tasks — it's the one thing that shouldn't change.
- Only change prompt wording per model through [development/model-adapters.md](development/model-adapters.md), not by rewriting task guides.
- If you're packaging this repo for a tool, search index, or MCP server, see [meta/CONSUMING.md](meta/CONSUMING.md) — don't bundle everything into one payload.

## What this does and doesn't guarantee

- **This standardizes inputs, not outputs.** Loading the same guides for the same task does not make two runs produce the same result — the model's generation is still stochastic. What this repo controls is what rules, scope, and checklist the model sees before it starts, not what it does with them.
- **There is no measured evidence this improves outcomes.** No controlled before/after evaluation of this repo exists. Its rationale is established prompt-engineering and process-engineering practice (scoped context, explicit non-negotiable rules, checklists with mandatory per-section status) applied consistently, not a benchmarked result specific to this repo.

## The three layers

1. **Shared rules** — [core/shared-contract.md](core/shared-contract.md) (plus [core/memory-contract.md](core/memory-contract.md) if the host has memory). These don't change by task or model. Release gating lives in [development/repo-integrity-audit.md](development/repo-integrity-audit.md) instead, since it's task-shaped, not a standing rule.
2. **Model adapters** — [development/model-adapters.md](development/model-adapters.md) changes prompt structure per model family (Claude, GPT, Gemini, Llama, Mistral, Grok, or reasoning models like o1/o3) without changing the task rules.
3. **Task guides** — one file per kind of work: [debugging](development/debugging.md), [code review](development/code-review.md), [test generation](development/test-generation.md), [git workflow](development/git-workflow.md), and the rest of [development](development) and [setup](setup).

## Running this repo as an MCP server

`rubric_mcp.py` runs this repo as an MCP server, so a host can call it directly instead of you finding and pasting files by hand. It has four tools:

- `get_index` — the full guide list as JSON
- `select_guides` — the smallest set of guides for a task you describe
- `get_guide` — the raw content of one guide file
- `get_shared_contract` — the content of `core/shared-contract.md` directly

To run it:

```bash
pip install .
python3 rubric_mcp.py
```

An example MCP client config is in [.vscode/mcp.json](.vscode/mcp.json).

## Repository layout

- [core](core) — rules that stay the same across tasks and models
- [development](development) — one guide per kind of task: coding, review, tests, debugging, git, and audits
- [setup](setup) — project setup, architecture, documentation, prose style, and reproducibility guidance
- [meta](meta) — machine-readable index of every guide, for tools that package or search this repo
- `rubric_mcp.py`, `pyproject.toml` — the MCP server that exposes this repo as callable tools

## Disclaimer

This repository is provided for reference. Its authors are not responsible for any actions, decisions, or consequences that result from using it, including anything done by an AI agent following this guidance. Use is at your own risk.

See [LICENSE](LICENSE) for the separate software warranty disclaimer.

## License

MIT — see [LICENSE](LICENSE).

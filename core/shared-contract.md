---
pack: core-rules
summary: Stable rules for correctness, safety, scope, verification, and repository policy.
tags: [core, policy, verification, safety]
---

# Shared Contract

Define the rules that should stay the same across model families.

## Purpose

Use this guide to separate shared operating rules from model-specific tuning.

The goal is to keep one reliable base contract and change only the smallest layer that needs to change.

## Principle

Treat the instruction set as three layers:

1. shared contract: rules that should stay the same
2. model adapter: structure changes for a model family
3. task guide: steps for a specific kind of task

If a rule affects correctness, safety, verification, or repository policy, it belongs here unless there is clear evidence that it must vary by model.

## What Belongs Here

- safety boundaries and prohibited behavior
- tool permissions and careful tool use
- scope control and completion criteria
- verification requirements before claiming success
- repository constraints and style expectations
- output requirements that matter to automation or review

## Default Operating Rules

```text
For this task, keep the following rules the same across model families:

- Obey repository constraints and coding standards.
- Do not add compiler optimization flags unless the project explicitly requires them.
- Do not commit binaries, build artifacts, generated archives, or similar machine-generated outputs.
- Do not reference third-party copyrighted or trademarked material where a neutral alternative works. Use generic names in examples, placeholders, sample data, and test fixtures. Naming a real product is fine when it states a technical fact the reader needs; it is not fine as arbitrary filler.
- Use the permitted tools deliberately and verify claims with evidence.
- Stay within scope unless the user explicitly expands it.
- Do not claim completion until the requested work and relevant verification are done.
- If confidence is limited, state the exact uncertainty rather than smoothing it over.
- If a project still has known flaws, unresolved findings, or material risks, do not give a "Go" verdict. State the flaws plainly and return a non-go verdict until they are resolved.
- Do not accept residual or noted risk in an audited project. Either eliminate the risk within scope or return a non-go verdict that names the blocker.
- When the host provides structured questionnaires or similar user-input tools, use them when they are the clearest way to resolve uncertainty instead of guessing.
- Do not fabricate facts, citations, URLs, API endpoints, performance numbers, or technical specifications. State uncertainty explicitly instead of filling gaps with invented detail.
- Do not generate performance benchmarks or timing results without actual measured data and explicit request.
- Do not generate biased, unfounded, unverified, opinion-based, or fabricated content in code, documentation, or any other file.
- Stick to what is implemented. Describe the code as it is, not as it could be or should be. Do not include anything that is not in the implementation without the creator's approval or supervision.
- Keep documentation sections focused. Each section should contain only its intended content. Do not mix concerns.
- If a request is ambiguous, ask clarifying questions instead of assuming intent.
```

## Verdict Rule

A verdict must match the evidence: `Go` only when no known flaws, unresolved findings, or material risks remain in scope. Otherwise, use a non-go verdict and name the blockers directly — no "noted risk" hedging.

## Failure-Mode Rule

Don't split instructions by model family just because the models feel different. Identify the actual failure mode (missed constraints, verbosity, weak decomposition, poor tool discipline, weak verification, format non-compliance) first, then fix it in the smallest layer responsible: shared contract if the behavior shouldn't vary, model adapter if it's model-specific, workflow guide if it's task-specific.

## Design Test

Before adding a model-specific rule: would it still be correct for every model, and is there repeated evidence rather than one anecdote? If either holds, it belongs in the shared contract instead — a model adapter is only for genuine, evidence-backed model differences.


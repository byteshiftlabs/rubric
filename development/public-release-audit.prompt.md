---
agent: "agent"
description: "Use when performing a thorough public-release audit in one structured pass. Creates or updates a single markdown findings file with coverage tracking, severity-ranked issues, unchecked areas, and an ordered fix plan."
tools:
  - read
  - search
  - edit
  - execute
  - todo
---

# Public Release Audit

Run a thorough, coverage-driven audit of a project before public release.

## Inputs

- `PROJECT_NAME`: project or repository name
- `PROJECT_CONTEXT`: short description of the project, stack, and release goals
- `OUTPUT_MD_FILE`: path to the audit findings markdown file to create or update

## Prompt

```text
Perform an exhaustive public-release audit of PROJECT_NAME in one structured pass.

Context:
PROJECT_CONTEXT

Use development/repo-integrity-audit.md as the canonical audit protocol and
the public-release gate. Follow its workflow, output structure, evidence
rules, and section 11 (Release preparation) exactly.

Use these supporting guides where relevant:
- development/code-review.md
- development/test-generation.md
- development/error-handling.md

Create or update OUTPUT_MD_FILE immediately and keep it as the authoritative audit file.

Additional rules for this release audit:
- Do not claim complete certainty without evidence.
- Do not silently skip docs, tests, packaging, or release assets.
- Do not treat checklist completion as proof; verify with code, tests, docs, or commands.
- If runtime verification is needed, say exactly what should be run.
- If the repository is too large for full confidence, state the exact limit and what remains unchecked.
- Keep the audit findings file local-only. Do not commit or push it.
- End with a clear release recommendation: release now / do not release, plus preconditions.
```

## Example Invocation

```text
PROJECT_NAME: premise
PROJECT_CONTEXT: Python research search tool with CLI, GUI, multiple external APIs, cache layer, packaging metadata, and unit tests. Goal is public release with outstanding code, docs, and tests.
OUTPUT_MD_FILE: docs/audit-findings.md
```

## Expected Output Characteristics

- One markdown findings file, updated during the audit
- Explicit coverage accounting
- Explicit reuse of repo-integrity-audit.md as the protocol source of truth
- A release decision grounded in repo-integrity-audit.md section 11
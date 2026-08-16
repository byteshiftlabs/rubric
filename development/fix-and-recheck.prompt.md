---
agent: "agent"
description: "Use after an audit findings file exists. Fix issues in severity order, update the findings file in place, add missing tests, and re-run the audit or targeted verification before closing items."
tools:
   - read
   - search
   - edit
   - execute
   - todo
---

# Fix And Recheck

Consume an existing audit findings file, resolve issues in priority order, and update the same file with current status.

## Inputs

- `PROJECT_NAME`: project or repository name
- `FINDINGS_MD_FILE`: path to the existing audit findings markdown file
- `PROJECT_CONTEXT`: short description of the project, stack, and constraints

## Prompt

```text
Fix and recheck PROJECT_NAME using FINDINGS_MD_FILE as the source of truth.

Context:
PROJECT_CONTEXT

Use these guides together:
- development/repo-integrity-audit.md
- development/test-generation.md
- development/git-workflow.md
- development/error-handling.md

Requirements:
1. Read FINDINGS_MD_FILE first and treat it as authoritative.
2. Work strictly in severity order:
   - all Blockers first
   - then all Serious issues
   - then all Minor issues
3. Within each severity, work in numeric order unless a dependency forces a different sequence.
4. After each fix:
   - update FINDINGS_MD_FILE in place
   - mark the item as fixed / still open / partially fixed / regressed
   - add a short verification note
   - add or update tests when the issue should be test-covered
5. If a finding cannot be fixed safely, do not hand-wave it away.
   Record the blocker, why it remains open, and the exact follow-up needed.
6. After the fix pass, perform a recheck:
   - re-run the relevant tests or checks
   - re-audit changed areas
   - update the merge/release recommendation in FINDINGS_MD_FILE

Rules:
- Do not skip to minor cleanup while blocker or serious issues remain open.
- Do not silently change issue numbering unless you also update the findings file consistently.
- Do not mark an item fixed without evidence from code, tests, docs, or commands.
- Preserve unresolved items in the findings file; do not delete them to make the file look clean.
- If new issues are discovered during fixes, append them with the next available severity ID.
- Keep the findings file local-only and do not commit or push it.
- If the workflow includes opening a PR, assign the PR creator and apply the correct existing label(s). If no suitable label exists, suggest creating one and wait for user approval before proceeding.
```

## Example Invocation

```text
PROJECT_NAME: premise
FINDINGS_MD_FILE: docs/audit-findings.md
PROJECT_CONTEXT: Python research search tool with CLI, GUI, external APIs, cache layer, packaging metadata, and unit tests. Goal is public release with outstanding code, docs, and tests.
```

## Expected Output Characteristics

- The findings file remains the single source of truth
- Issues are closed in severity order, not convenience order
- Every closed item has evidence
- Recheck results are written back into the same file
- The final recommendation reflects current reality, not the original audit
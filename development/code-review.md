---
pack: task-workflows
summary: Review process for normal quality, correctness, maintainability, and targeted cleanup.
tags: [workflow, review, quality]
---

# Code Review

Review code quality with emphasis on readability and maintainability.

## Prompt

```
Review [PROJECT_NAME] for code quality.

Check for clear, descriptive naming; no magic numbers; no uninitialized variables;
readable structure; and proper separation of concerns.

For each issue: file and location, problem description, applied fix.

If the task includes committing, follow [git-workflow.md](git-workflow.md).
```

## Language References

Treat these as suggestions to check against, not a checklist to enforce line-by-line — apply judgment for what the project actually needs:

- **Python**: [PEP 8](https://peps.python.org/pep-0008/)
- **C**: [GNU Coding Standards](https://www.gnu.org/prep/standards/)
- **C++**: [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)

---


## Severity Classification

Tag findings so fixes can be prioritized: **B***n* (Blocker — incorrect behavior, data loss, crash, or misleading docs), **S***n* (Serious — degrades quality without crashing: uninitialized state, dead code, missing error propagation, portability hazards), **M***n* (Minor — style, naming, stale comments; fix when touching the area). Number sequentially within each severity, and fix blockers, then serious, then minor.

```
| ID | File | Issue | Severity |
|----|------|-------|----------|
| B1 | src/core/memory.cpp | Off-by-one in bank switch | Blocker |
| S1 | src/audio/apu.h | Uninitialized channel members | Serious |
| M1 | docs/api/index.rst | Stale method name in example | Minor |
```

## Static Analysis

Run the project's static analyzer as part of every review, not just at release time. Watch for the categories that matter most: variable shadowing, raw loops that a standard-library algorithm would express more clearly, redundant or always-true/false conditions, missing const-correctness, dead code, and unnecessary copies where an in-place operation exists. When a finding can't be cleanly fixed, suppress it inline with a comment explaining why.

---
pack: task-workflows
summary: Review process for normal quality, correctness, maintainability, and targeted cleanup.
tags: [workflow, review, quality]
---

# Code Review

Review code quality with emphasis on readability and maintainability.

## Python

```
Review [PROJECT_NAME] for code quality.

Check for:
1. PEP8 compliance (except line length)
2. Imports at the top of each file, properly organized (stdlib → third-party → local)
3. Clear, descriptive naming for classes, functions, and variables
4. No monolithic modules — proper separation of concerns
5. No magic numbers — use named constants
6. Prefer string methods over regular expressions where possible
7. Readable code structure — logical flow, consistent formatting

For each issue:
- File and location
- Problem description
- Applied fix

If the task includes committing, follow [git-workflow.md](git-workflow.md).
```

---

## C

```
Review [PROJECT_NAME] for code quality.

Check for:
1. GNU coding standards compliance (https://www.gnu.org/prep/standards/)
2. Clear, descriptive naming for structs, functions, and variables
3. No magic numbers — use #define or const
4. No uninitialized variables — initialize at declaration
5. Declare variables at point of first use — not at the top of the function
6. Forward declarations only for mutual recursion or top-down file organization of static helpers — never to duplicate extern signatures from headers
7. Prefer string functions over regex where possible
8. Readable code structure — consistent indentation, logical grouping

For each issue:
- File and location
- Problem description
- Applied fix

If the task includes committing, follow [git-workflow.md](git-workflow.md).
```

---

## C++

```
Review [PROJECT_NAME] for code quality.

Check for:
1. C++ Core Guidelines compliance (https://isocpp.github.io/CppCoreGuidelines/)
2. Clear, descriptive naming for classes, methods, and variables
3. No magic numbers — use constexpr or const (fix immediately, never defer)
4. No uninitialized variables — initialize at declaration
5. Declare variables at point of first use — not at the top of the function
6. Forward declarations only for mutual recursion or top-down file organization of static helpers — never to duplicate extern signatures from headers
7. Prefer string methods over regex where possible
8. Readable code structure — consistent style, logical organization
9. No shadow variables — local variables must not shadow member functions, outer variables, or parameters
10. Prefer STL algorithms (std::copy, std::find_if, std::fill) over raw loops where the intent is clearer
11. Const correctness — references and pointers to read-only data must be const-qualified
12. Good OOP boundaries — classes must have clear ownership and single responsibility; public methods must preserve class invariants; if copy/move would invalidate back-references, resource ownership, or object graph consistency, explicitly delete those operations
13. Copyright years must be current — update ranges when working in a new calendar year
14. Prefer `resize()` over `substr(0, n)` for in-place string truncation — avoids unnecessary copy
15. No abbreviations in identifiers — use full descriptive names (`cartridge` not `cart`, `frequency` not `freq`) unless the abbreviation is universally understood (e.g., `std`, `ptr`, `num`)

For each issue:
- File and location
- Problem description
- Applied fix

If the task includes committing, follow [git-workflow.md](git-workflow.md).
```

---

## Placeholders

- `[PROJECT_NAME]`: Target project or directory

## Severity Classification

Tag every finding with a severity prefix so fixes can be prioritized:

| Prefix | Severity | Meaning |
|--------|----------|---------|
| **B***n* | **Blocker** | Incorrect behavior, data loss, crash, or documentation that actively misleads users. Must be fixed before release. |
| **S***n* | **Serious** | Won't crash but degrades quality: uninitialized state, dead code paths, missing error propagation, portability hazards. Should be fixed before release. |
| **M***n* | **Minor** | Style, naming, stale comments, missing docs. Fix when touching the area; acceptable to defer if on a deadline. |

Number findings sequentially within each severity (B1, B2, ..., S1, S2, ..., M1, M2, ...).

**Fix order**: All blockers first (B1 → B*n*), then serious (S1 → S*n*), then minor (M1 → M*n*).

When presenting review results, group findings by severity with a summary table:

```
| ID | File | Issue | Severity |
|----|------|-------|----------|
| B1 | src/core/memory.cpp | Off-by-one in bank switch | Blocker |
| S1 | src/audio/apu.h | Uninitialized channel members | Serious |
| M1 | docs/api/index.rst | Stale method name in example | Minor |
```

This convention helps reviewers and authors prioritize work with a shared vocabulary.

## Quick Variations

| Use case | Prompt |
|----------|--------|
| Quick check | `Check [FILE] for PEP8 and magic numbers only.` |
| Naming audit | `Review all identifiers in [PROJECT] for clarity. Rename ambiguous names.` |
| Imports only | `Organize imports in [FILE]: stdlib → third-party → local, at file top.` |

## Static Analysis (C/C++)

Run static analysis tools as part of every code review, not just at release time:
```
cppcheck --inline-suppr --enable=all --suppress=missingIncludeSystem --suppress=unusedFunction -I src/ src/
```

Common findings to watch for:
- **shadowVariable / shadowFunction**: Local variable shadows a member function, parameter, or outer variable. Rename the local.
- **useStlAlgorithm**: Raw loop can be replaced with std::copy, std::find_if, std::fill, etc. Convert when the STL version is clearer.
- **knownConditionTrueFalse**: Redundant condition (e.g., `x >= 0x10` after a prior block that returns for `x <= 0x0F`). Simplify the condition.
- **constVariableReference**: Reference to non-modified data should be const.
- **unusedPrivateFunction**: Dead code — remove it.
- **performance (substr)**: `str.substr(0, n)` creates a copy — use `str.resize(n)` for in-place truncation.

When a cppcheck finding cannot be cleanly fixed (e.g., loop with side-effect index), use inline suppression with justification:
```cpp
// cppcheck-suppress useStlAlgorithm  // offset++ side effect not expressible in std::copy
```

## Tips

- Run this after every change, major or minor
- Readable code reduces bugs and shortens onboarding time
- When in doubt, choose the more explicit option
- **Fix magic numbers immediately.** Do not leave bare numeric literals for later cleanup.

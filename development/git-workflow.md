---
pack: task-workflows
summary: Guidance for branches, commits, PR flow, and release hygiene.
tags: [workflow, git, release]
---

# Git Workflow

Conventions for commits, branches, pull requests, and releases.

## Setup (Required)

```
Before making any commits, configure your identity in each local repo:

git config user.name "Your Full Name"
git config user.email "your.email@example.com"

This ensures every branch, commit, and PR is traceable to its author.
Verify with: git config --list
```

## Commit Messages

```
Use past participle for all commit messages. Keep them brief, clear, and specific.
List grouped changes with hyphen bullets when a multi-line message is needed.

Examples:
- Added user authentication module
  - Added login and logout functions
  - Added session token management
  - Added password hashing

- Fixed null pointer exception in parser
  - Added null check before dereferencing
  - Added unit test for edge case

- Renamed ambiguous variables, removed magic numbers
  - Renamed `ns` to `premise_module`
  - Replaced hardcoded 10 with MIN_RESULTS_PER_TERM
  - Replaced hardcoded 2026 with CURRENT_YEAR
```

## Branch Naming

```
Branch names must be self-explanatory and follow the pattern:

[label]/[brief-description]

The [label] prefix must match an existing GitHub label in the repository.
If no suitable label exists, suggest creating one before creating the branch.

Examples:
- feature/user-authentication
- fix/parser-null-pointer
- code-refactor/remove-magic-numbers
- docs-refactor/api-reference
- tests/validation-coverage
```

## Pull Requests

```
PR titles follow the same format as commit messages (past participle).

When creating a PR:
- Assign the PR creator to the PR
- Apply the proper existing GitHub label(s)
- If no suitable label exists, suggest creating a new label and wait for user approval before proceeding

Description must include:
- Summary: What changed, in a sentence or two
- Changes: One line per change, grouped by theme or file
- Testing: What was actually run

State what changed and nothing else. Leave out the reasoning behind the
change, how the problem was found, why an alternative was rejected, and what a
tool does. Explaining why is the developer's job, not yours.

Facts a reviewer needs belong in: a corrected value, a removed file.
Sentences beginning "because", "this means", or "the cause was", and any
recounting of what you checked and in what order, stay out. A before/after
table beats a paragraph explaining a discrepancy.

Leave out numbers that only measure the size of the work. How many lines a
file went from and to, how many symbols, directives, call sites or files were
touched, how many warnings a tool printed: these will probably never be of any
interest to anyone. Write "generated the API reference from the sources", not
"132 hand-written symbols to 1,085, 1,491 lines to 216".

Write a number when the number is the fact: a value the documentation stated
wrongly and now states correctly, a version, a size a reader has to match. A
corrected-figures table is exactly this.

Keep all sections brief and factual.

Do not hard-wrap the text. Write each paragraph and each list item as one long
line and let the browser reflow it. GitHub renders issue, pull request and
discussion bodies with soft line breaks enabled, so a paragraph wrapped at 76
columns becomes a stack of 76-column lines and cannot use the full width of the
page. Tables, code fences and list markers stay on their own lines as normal.
```

## Branch Cleanup

```
Delete a branch once its PR is merged:

- Delete the remote branch immediately after merge (GitHub's "Delete branch"
  button on the PR, or `git push origin --delete [branch]`).
- Delete the local branch too: `git branch -d [branch]`.
- Do this for every merge, not just occasionally — a stale branch list makes
  it harder to tell active work from finished work.
- Exception: shared long-lived branches the repository documents as
  permanent (for example a maintenance or release branch), which stay.

If the repository allows it, enable "Automatically delete head branches" in
GitHub repository settings so this happens without a manual step.
```

## Releases

```
Use semantic versioning for all tags and GitHub Releases:

- vX.Y.Z for final releases
- vX.Y.Z-alpha.N, vX.Y.Z-beta.N, or vX.Y.Z-rc.N for pre-releases when needed

Bump rules:
- Patch (Z): backward-compatible bug fixes, release hardening, CI/tooling fixes, test-only coverage, and documentation corrections
- Minor (Y): backward-compatible feature additions or meaningful supported-scope expansion
- Major (X): breaking changes or compatibility resets

Release names and annotated git tags must match exactly.
Create releases from the merged default branch unless the repository documents a maintenance-branch workflow.

Release notes must include:
- Summary: what changed and why this version exists
- Changes: bullet list of user-visible fixes/features
- Verification: how the release was tested or validated
```

## Tips

- Keep one logical change per commit
- Reference issue numbers when applicable (for example, `Fixed #123`)
- Apply this format consistently across a project's repositories unless a repository documents different rules
- Audit findings files must stay local and must not be pushed to any repository
- Delete a branch as soon as its PR merges — see Branch Cleanup above
- Before committing, check whether README.md or ROADMAP.md need updating to reflect the change. See [documentation.md](../setup/documentation.md#keeping-readmemd-and-roadmapmd-in-sync).

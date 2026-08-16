---
pack: task-workflows
summary: Helps design APIs with clear contracts, compatibility, and maintainable boundaries.
tags: [workflow, design, api]
---

# API Design

Design stable, user-facing interfaces that minimize breaking changes.

## Core Principle

Once you ship an API, it becomes a contract with your users. Breaking that contract should be rare and well justified. Design carefully from the start because repairing a weak API later is expensive for both maintainers and users.

## Prompt

```
Design a public API for [FUNCTIONALITY] in [PROJECT_NAME].

Requirements:
- Define the minimal interface that solves the problem
- Design for extensibility without breaking existing code
- Follow the principle of least surprise
- Document behavior, edge cases, and error conditions
- Consider versioning strategy from the start

Questions to answer:
- What operations must users perform?
- What flexibility do users need?
- What can change in the future without breaking existing code?
- What invariants and contracts must the API maintain?
- How will errors be handled and communicated?

Guiding principle: "Make the simple case simple and the complex case possible."
```

## Placeholders

- `[FUNCTIONALITY]`: What the API enables (e.g., "file I/O", "network requests")
- `[PROJECT_NAME]`: Project context

## API Design Principles

**Backward compatibility is sacred.** Don't break existing code without a strong reason. Adding functions, optional parameters, or error codes is fine; removing or renaming things, changing signatures or return types, or tightening constraints on previously valid input is not. If you must break compatibility, bump the major version, provide a migration guide and deprecation period, and justify why compatibility couldn't be preserved.

**Keep the interface minimal.** Expose only what users need — every public detail becomes a maintenance burden and locks you into a design. If you're unsure whether to expose something, don't; it's easier to add later than to remove.

**Follow the principle of least surprise.** A function should do what its name suggests and nothing more. If the name doesn't capture the behavior, rename it or split it.

**Fail fast and clearly.** Check inputs at the API boundary and return specific, actionable errors — don't accept invalid input silently and fail later, and don't return generic "something went wrong" errors.

**Be consistent.** Similar operations should use similar names, parameter order, and patterns throughout the API.

## API Design Checklist

Based on the characteristics of good APIs in Jasmin Blanchette's ["The Little Manual of API Design"](https://www.cs.vu.nl/~jbe248/api-design.pdf) (Trolltech, 2008):

- Is it easy to learn and memorize?
- Does it lead to readable code at the call site?
- Is it hard to misuse?
- Is it easy to extend later without breaking existing users?
- Is it complete enough to cover the reasonable use cases, without over-reaching?

## Versioning

Don't assume a versioning scheme. Ask the user (or check the project's existing convention) what versioning strategy applies before proposing one — semantic versioning is a common default, but the project may already follow something else, or have no formal scheme at all.

## Tips

- Design for real users and real use cases, not hypothetical future abstractions.
- Write usage examples early. If the API is awkward in examples, redesign it before implementing.
- Seek feedback before implementation when the API will be public or long-lived.
- Document behavior, not just signatures. Users need to know what the function does, not only what it accepts.
- Prefer stable, predictable APIs over clever but fragile ones.

## Related Guides

- [architecture.md](../setup/architecture.md) — system-level organization
- [documentation.md](../setup/documentation.md) — documenting APIs
- [git-workflow.md](git-workflow.md) — versioning and releases
- [data-structure-design.md](data-structure-design.md) — internal structure design

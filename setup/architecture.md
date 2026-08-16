---
pack: task-workflows
summary: Guidance for project architecture review and structural design work.
tags: [setup, architecture, design]
---

# Architecture

Use lightweight architecture principles to keep projects maintainable and scalable.

## Development Process

A lightweight requirements → design → implementation flow to prevent "code first, think later" problems.

### Phase 1: Requirements Analysis

Before writing any code, answer:

- **What does success look like?** Define acceptance criteria.
- **What are the inputs and outputs?** Data formats, sources, destinations.
- **What are the constraints?** Performance, compatibility, dependencies.
- **What are the edge cases?** Empty inputs, invalid data, error scenarios.
- **How will we verify it works?** Test strategy.

**Output**: A clear, testable description of what needs to be built.

### Phase 2: Design

Before implementation, sketch:

- **Component structure**: What modules/classes are needed?
- **Interfaces**: How do components communicate? Define contracts.
- **Data flow**: How does data move through the system?
- **Error handling**: Where can things fail? How do we recover?
- **Testability**: Can each component be tested in isolation?

**Output**: A rough diagram or written description of the structure. It does not need to be formal. A text outline or sketch is enough.

### Phase 3: Implementation

With requirements and design in place:

- Follow the design, adapt when reality differs (document why)
- Build incrementally (see [incremental-development.md](../development/incremental-development.md))
- Validate against requirements continuously
- Refactor design if implementation reveals flaws

**Output**: Working code that meets the requirements.

---

## Key Principles

### 1. Layered Separation

Organize code into distinct layers with clear responsibilities:
- **Interface layer**: UI, CLI, API endpoints — handles user interaction
- **Business logic layer**: Core functionality — independent of how it's accessed
- **Data layer**: Storage, external services — isolated behind abstractions

**Rule**: Never skip layers. Interface should not directly access data.

### 2. Dependency Direction

Outer layers depend on inner layers, never the reverse:
- Interface layer → depends on → Business logic → depends on → Core domain
- Core logic should not know about specific databases, APIs, or UI frameworks
- Use interfaces/abstractions to invert dependencies when needed

**Rule**: A change in the UI should never require changing business logic. A change in the database should never break the core.

### 3. Single Entry Point

One clear starting point per executable:
- `main.py`, `main()`, or equivalent
- All initialization happens here
- Makes the codebase navigable — "start here"

### 4. Configuration Externalization

No hardcoded environment-specific values in code:
- Paths, URLs, credentials, feature flags → config files or environment variables
- Provide sensible defaults
- Document all configuration options

### 5. Explicit Module Boundaries

Each module/package should have a single, clear responsibility:
- If you can't describe what it does in one sentence, split it
- Minimize public API surface — expose only what's necessary
- Avoid circular dependencies between modules

### 6. Separation of Concerns

Each component does one thing:
- Don't mix I/O with computation
- Don't mix validation with business logic
- Don't mix formatting with data processing

### 7. Fail Fast, Fail Clearly

Validate early, at system boundaries:
- Check inputs at entry points
- Fail immediately with clear error messages
- Don't propagate invalid state through the system

### 8. Stateless When Possible

Prefer pure functions and immutable data:
- Easier to test, debug, and parallelize
- When state is necessary, isolate it
- Make state changes explicit and traceable

### 9. Consistent Naming Across Layers

Use the same terminology everywhere:
- If the domain calls it "paper", don't call it "document" in one layer and "article" in another
- Reduces cognitive load and translation errors

### 10. Test Boundaries, Not Internals

Design for testability:
- Each layer should be testable in isolation
- Mock at layer boundaries, not inside them
- If something is hard to test, the architecture may need rethinking

---

## Prompt

```
Review the architecture of [PROJECT_NAME].

Check for:
1. Clear layer separation (interface → logic → data)
2. Correct dependency direction (no low-level details in high-level modules)
3. Single, obvious entry point
4. Externalized configuration
5. Explicit module boundaries with single responsibilities
6. No mixed concerns within modules

For each violation:
- Identify the location
- Explain why it's problematic
- Propose a fix (do not implement without approval)
```

## Tips

- Architecture is about trade-offs, not perfection
- Start simple and refactor when pain points appear
- Document architectural decisions and their rationale
- When in doubt, favor clarity over cleverness

---
pack: core-rules
summary: Defines when memory is real, what should be stored, and how to separate memory scopes.
tags: [core, memory, context]
---

# Memory Contract

Define how an agent should use memory when the host really supports it. This file does not create memory — it governs behavior when the host already provides memory tools or built-in storage, so behavior stays consistent across tasks and model families.

## What Counts As Memory Support

Treat a host as memory-capable only if it can store information outside the current prompt window, retrieve it later, and give clear rules or tools for writing, updating, and deleting it. A pasted note, a single-session chat, or a summarizer that can't retrieve past sessions does not count. If the host can't save and later retrieve memory, treat it as unsupported.

## Memory Scopes

Keep three scopes separate, never mixed:

1. **Session context** — active task state, temporary plans, working assumptions that may change.
2. **User memory** — stable user preferences, repeated workflow instructions, stable approval boundaries or tool constraints.
3. **Repository or workspace memory** — project-specific facts, conventions, commands, and structure.

## What To Remember

Only information that is stable and likely to help later: explicit requests to remember something, repeated corrections that reveal a stable preference, stable tool restrictions or communication preferences, recurring workflow requirements, clear approval boundaries.

## What Not To Remember

Secrets, credentials, tokens, keys, or personal sensitive data; current task state that will go stale; speculative guesses about the user; repo-specific implementation details in user memory; one-off exceptions or unresolved assumptions.

## Before Writing Memory

Write to user memory only when the information is explicit or confirmed by repeated evidence, likely to matter in a future task, belongs in user (not session or repository) memory, and is short enough to stay maintainable. Otherwise, keep it in session context.

## First-Use Initialization

Before substantial task execution, check what memory scopes the host provides and review existing user memory before adding entries. Apply any memory rules the user or repository already defines. Don't write guesses into user memory, treat session summaries as user memory, store repository facts in user memory, or duplicate existing entries.

## Updating And Removing Memory

Update stored memory when the user corrects or replaces it; remove it when it's contradicted, outdated, or causing friction. Prefer editing an existing entry over creating a duplicate. If unsure whether a preference still holds, ask, or leave it out of user memory.

## Default Operating Rules

```text
When the host supports memory:

- Use memory deliberately, not automatically.
- Store only stable user preferences and stable constraints.
- Keep task state in session context, not in user memory.
- Keep repository facts in repository-scoped memory when available.
- Do not store secrets, guesses, or one-off exceptions.
- Update or remove stored memory when the user changes direction.
```

## When The Host Does Not Support Memory

Don't pretend memory exists. Keep important instructions in the current prompt or session summary, tell the user that persistence depends on the host environment, and use context-management rules to separate current-task context from future-use preferences even though they can't be persisted yet.

---
pack: task-workflows
summary: Describes how prompt structure should vary by model family without changing task rules.
tags: [routing, adapter, model-family]
---

# Model Adapters

Adjust prompt structure by model family without rewriting the whole system.

## Purpose

Use this guide when the same workflow behaves differently across model families.

Keep the shared contract stable. Change only the prompt structure, instruction order, and repetition level needed to improve reliability for the active model.

## Adapter Strategy

Apply model-specific tuning only in a thin adapter layer.

Do not rewrite task guides unless evaluation shows that the guide itself is the problem.

## Shared Baseline

These rules should remain shared unless evidence proves otherwise:

- safety and policy boundaries
- repository constraints
- tool permissions
- required verification
- stop conditions and completion criteria

## GPT-Family Adapter

GPT-family models usually respond well to explicit structure.

Prefer:

- clear priority ordering
- direct success criteria
- numbered steps for multi-stage tasks
- explicit separation between planning, execution, and verification
- concrete format requirements near the top of the prompt

Watch for:

- following wording too literally
- excessive compliance text in the final answer
- rigid behavior when too many overlapping rules are present
- defaulting to abstract, jargon-heavy language instead of plain explanations
- framing simple concepts in academic or consulting register when direct wording is clearer

Recommended adjustments:

```text
Use the shared contract as written.

For this model family:
- Put the most important constraints first.
- Use numbered execution steps for complex tasks.
- State the completion check explicitly.
- Keep formatting rules concrete and close to the requested output.
- Write in plain, direct language. Prefer concrete words over abstract nouns.
  Say "check that X matches Y" instead of "enforce a compatibility invariant".
  Say "the build fails if versions differ" instead of "a machine-enforced
  compatibility marker beyond documented release discipline".
  If a sentence would confuse a junior developer, rewrite it.
```

## Claude-Family Adapter

Claude-family models usually benefit from cleaner grouping and less repeated instruction.

Prefer:

- related rules grouped together
- fewer repeated constraints
- concise but explicit scope boundaries
- higher-level framing followed by a short execution pattern

Watch for:

- following the general idea while missing a specific mechanical requirement
- giving a polished answer without enough concrete verification

Recommended adjustments:

```text
Use the shared contract as written.

For this model family:
- Group related constraints together instead of repeating them.
- Remove redundant wording before adding stronger wording.
- Keep the workflow conceptually clear and avoid instruction collisions.
- Restate exact verification or output requirements once, plainly.
```

### XML Tags for Claude

Claude parses structured sections more reliably when content is wrapped in XML tags. Use tags to separate instructions, context, examples, and output format.

```text
<instructions>
[task rules and steps]
</instructions>

<context>
[background the model needs to understand the task]
</context>

<example>
Input: [example input]
Output: [example output]
</example>

<output_format>
[what the response must look like]
</output_format>
```

Do not mix XML tag structure with markdown header structure at the same level. Use one or the other per block.

### Assistant Prefilling for Claude

Claude treats the start of its own turn as a strong formatting signal. Prefill the assistant turn with the exact token you want the response to begin with. This locks the output format before the model begins generating.

Example: to force a JSON response, end your human turn and open the assistant turn like this:

```text
[your prompt here]
```

The assistant will begin its response from that token, preventing preamble and enforcing format from the first character. Useful for locking JSON, YAML, code blocks, or specific section headers.

## Gemini-Family Adapter

Source: Google's official Gemini prompting guide (ai.google.dev/gemini-api/docs/prompting-strategies).

Prefer:

- direct, precise wording — state the goal plainly, skip persuasive language
- clear delimiters between prompt parts (XML tags such as `<context>`/`<task>`, or markdown headings)
- critical instructions, role definition, and output format placed first — in the system instruction or the very start of the prompt
- few-shot examples in most prompts — Google's own guidance is that prompts without them tend to be weaker; 3-5 consistent examples is a reasonable range
- one task per prompt — split multi-part requests into separate sequential prompts instead of one large one
- structured output features (not prompt-only instructions) for JSON or other strict formats

Watch for:

- leaving out context the model needs — Gemini does not fill gaps well from assumed shared knowledge
- inconsistent formatting across few-shot examples, which weakens the pattern instead of reinforcing it

Recommended adjustments:

```text
Use the shared contract as written.

For this model family:
- Put role, constraints, and output format at the very start of the prompt.
- Separate instructions, context, and examples with clear delimiters (XML tags or headings).
- Include a few consistent examples rather than none.
- Ask for one task per prompt; split multi-part requests into separate prompts.
- Keep temperature at its default unless there is a specific, tested reason to change it.
```

## Llama-Family Adapter

Source: Meta's official Llama best-practices documentation and AWS's Meta-partnered guide for Llama 3 on Amazon SageMaker.

Prefer:

- an explicit role/persona at the start of the prompt for context
- task decomposition — break a complex request into smaller sub-tasks rather than one large instruction
- an explicit nudge toward step-by-step reasoning when the task needs it — Llama does not reliably reason in stages unless asked
- concrete, literal constraints — Llama follows explicit formatting and scope restrictions closely (for example, "respond only in bullet points" or "do not use sources older than 2020")
- at least two examples when using few-shot prompting

Watch for:

- vague instructions — Llama tends to follow the letter of a prompt, so ambiguity shows up directly in the output
- if calling a raw/self-hosted Llama model directly (not through a chat-style API), the prompt needs the model's special tokens (`<|begin_of_text|>`, `<|start_header_id|>`/`<|end_header_id|>` for roles, `<|eot_id|>` to end a turn) — most hosted APIs (Bedrock, Together, Groq, etc.) add these automatically

Recommended adjustments:

```text
Use the shared contract as written.

For this model family:
- Open with a clear role or persona statement.
- Break multi-part work into explicit sub-tasks.
- State formatting and scope constraints literally and concretely.
- Ask directly for step-by-step reasoning when the task needs it.
- Use two or more examples for few-shot prompting.
```

## Mistral-Family Adapter

Source: Mistral's official documentation (docs.mistral.ai/models/best-practices/prompt-engineering).

Prefer:

- a short role-and-task opening: "You are a [role], your task is to [task]"
- hierarchical structure — clear sections and subsections rather than one flat block
- markdown or XML-style tags to mark those sections; Mistral's own docs recommend this because it is readable, parsable, and familiar from training data
- explicit output-format instructions, and structured-output/JSON mode when the format must be strictly consistent
- objective, measurable language instead of vague qualifiers ("too long," "better") — state a concrete limit or criterion instead
- worded scales ("Very Low, Low, Neutral, Good, Very Good") instead of numeric 1-5 ratings, per Mistral's own guidance

Watch for:

- contradictory instructions — Mistral's docs recommend a decision tree when two rules could conflict, rather than leaving the conflict implicit
- asking the model to count things itself (word counts, item counts) — provide counts as input instead
- requesting more output than the task actually needs

Recommended adjustments:

```text
Use the shared contract as written.

For this model family:
- Open with "You are a [role], your task is to [task]."
- Organize the prompt into clearly labeled sections.
- State output format explicitly; use structured output mode for strict formats.
- Replace vague qualifiers with objective, measurable criteria.
- Resolve any conflicting instructions explicitly instead of leaving them implicit.
```

## Grok-Family Adapter

Source: xAI's official documentation (docs.x.ai) and xAI's published prompting guidance for grok-code-fast-1.

Prefer:

- XML tags or markdown headers to separate sections of a prompt, especially for agentic/coding tasks
- a Goal → Constraints → Available tools → Deliverables structure for task-oriented or agentic prompts
- deliberate markdown inside responses: bullet lists for parallel items, bold for emphasis, inline code for identifiers/paths/commands, tables for short enumerable facts
- an iterative prompting loop — send a reasonable first attempt and refine based on the result, rather than trying to perfect one long prompt upfront

Watch for:

- editing, removing, or reordering earlier messages in a multi-turn session — if the integration uses xAI's prompt caching, this invalidates the cached prefix and slows every following call
- when calling the xAI API directly, forgetting to keep a stable conversation/session identifier, which also affects cache hits

Recommended adjustments:

```text
Use the shared contract as written.

For this model family:
- Separate sections with XML tags or markdown headers.
- For agentic or tool-using tasks, structure the prompt as Goal, Constraints, Available tools, Deliverables.
- Use markdown deliberately in the response: bullets, bold, inline code, and tables where they fit.
- Treat the first prompt as a draft; refine through iteration rather than one long upfront prompt.
```

## Reasoning Model Adapter

Use this adapter for models that run extended internal reasoning before responding: Claude with extended thinking enabled, and OpenAI o1 / o3 family models.

These models internally generate a chain of reasoning that is not visible in the response. Because of this, the prompting patterns that work for standard models are counterproductive here.

**Do not use with reasoning models:**

- Explicit chain-of-thought instructions ("think step by step", "reason through this in order") or stepped reasoning templates — the model already does this internally
- Over-specified decomposition prompts that prescribe how to reason

**Do use with reasoning models:**

- Open-ended problem framing that gives the model room to explore
- Explicit constraints and success criteria (state what the answer must satisfy, not how to find it)
- Minimal instruction count — fewer instructions produce more thorough internal reasoning
- Direct questions rather than step-by-step procedures

Recommended adjustments:

```text
Use the shared contract as written.

For reasoning models (o1, o3, Claude extended thinking):
- State the problem and the success criteria. Do not prescribe reasoning steps.
- List hard constraints the answer must satisfy.
- Do not add chain-of-thought instructions — the model reasons internally.
- Keep the prompt shorter than you would for a standard model.
- Trust a longer, slower response. It reflects genuine internal exploration.
```

**Claude extended thinking:** Enable via the API `thinking` parameter. Do not instruct the model to think step by step in the prompt — set the budget tokens parameter instead and let the model allocate reasoning effort.

**o1 / o3:** These models perform best with direct task statements and explicit output format requirements. Do not add few-shot reasoning examples; they can anchor the model to a shallow pattern instead of letting it reason fully.

## When To Split A Workflow Prompt

Split a workflow prompt by model family only if all of the following are true:

1. The shared contract is already stable
2. The adapter layer has been tried first
3. The failure recurs in the same workflow across multiple tasks
4. Evaluation shows a real improvement from a workflow-specific split

If those conditions are not met, keep one shared workflow.

## Minimal Adapter Template

```text
Shared contract:
- [insert invariant rules]

Model adapter for [MODEL_FAMILY]:
- [instruction ordering rule]
- [verbosity or decomposition rule]
- [verification emphasis rule]

Task workflow:
- [task-specific procedure]
```

## Common Mistakes

- Forking the whole instruction set after one bad run
- Encoding style preferences as if they were safety rules
- Adding more reminders instead of removing conflicting ones
- Treating model folklore as evidence
- Changing both the adapter and workflow at the same time, which makes results hard to interpret

## Recommended Pairing

When tuning prompts by model family, use this guide together with:

- `core/shared-contract.md`
- `development/context-management.md`
- `development/task-decomposition.md`
---
pack: task-workflows
summary: Stylistic patterns that mark text as machine-written, and what to write instead.
tags: [setup, writing, docs, style, review]
---

# Prose Style: Tropes to Avoid

Applies to every piece of prose a project ships: READMEs, module documentation, course
notes, commit bodies, PR descriptions, issue text and design docs.

Large language models converge on a small set of stylistic habits. Readers notice them,
and once noticed they cost the text its authority, regardless of whether the content is
correct. Treat them as defects in the same way a broken code example is a defect.

The catalogue below is reproduced from **[tropes.fyi](https://tropes.fyi) by
[ossama.is](https://ossama.is)**, retrieved 16 August 2026 from
<https://gist.github.com/ossa-ma/f3baa9d25154c33095e22272c631f5a1>. It is included here
because it is the most complete public list of these patterns; the wording of the trope
descriptions is theirs.

## How to apply this

1. Write first, edit after. Do not try to satisfy this list while drafting.
2. On the editing pass, search for the mechanical ones. Em dashes, `->`, "it's worth
   noting", "delve", "let's unpack" and bold-first bullets are all greppable.
3. Judge the rest by reading. A single instance of any pattern is usually fine. The
   defect is repetition, or several patterns stacked in one passage.
4. Do not remove a construction that carries meaning purely to satisfy the list. "Days
   instead of years" is a real comparison, not a trope. Losing accuracy to gain style
   is a bad trade, and reviewers should reject it.

## Quick grep set

```bash
grep -nE -- "---|—" FILE                       # em dashes
grep -nEi "worth noting|worth mentioning" FILE
grep -nEi "delve|leverage|utilize|robust|streamline|harness|tapestry|landscape" FILE
grep -nEi "let'?s (unpack|explore|dive|break)" FILE
grep -nEi "here'?s (the|where|what)" FILE
grep -nEi "in conclusion|to sum up|in summary" FILE
grep -nEi "serves as|stands as|represents a" FILE
grep -nEi "imagine a|think of it as" FILE
grep -nE "^\s*[-*] \*\*" FILE                  # bold-first bullets
grep -nP "[\x{2192}\x{201C}\x{201D}\x{2018}\x{2019}]" FILE   # unicode arrows and smart quotes
```

---

## Word Choice

**Magic adverbs.** "Quietly", "deeply", "fundamentally", "remarkably", "arguably" used to
make a mundane statement feel significant.

**"Delve" and friends.** Also "certainly", "utilize", "leverage" as a verb, "robust",
"streamline", "harness".

**"Tapestry" and "landscape".** Ornate nouns where a plain one would do. Also "paradigm",
"synergy", "ecosystem", "framework".

**The "serves as" dodge.** Replacing "is" with "serves as", "stands as", "marks",
"represents". Write "the building is a reminder", not "serves as a reminder".

## Sentence Structure

**Negative parallelism.** "It's not X, it's Y", usually with an em dash. The single most
commonly identified tell. Variants: "not because X, but because Y"; "X, not Y"; and the
cross-sentence form "The question isn't X. The question is Y."

**"Not X. Not Y. Just Z."** Negating two things to build tension before the point.

**"The X? A Y."** A rhetorical question nobody asked, answered immediately.

**Anaphora abuse.** The same sentence opening repeated in quick succession.

**Tricolon abuse.** Rule-of-three lists stacked back to back.

**"It's worth noting."** Filler transitions that connect nothing. Also "importantly",
"interestingly", "notably", "it bears mentioning".

**Superficial analyses.** An "-ing" phrase tacked on to inject significance:
"highlighting its importance", "reflecting broader trends", "underscoring its role as".

**False ranges.** "From X to Y" where there is no scale and nothing in between.

## Paragraph Structure

**Short punchy fragments.** One thought per sentence, each its own paragraph, for
manufactured emphasis.

**Listicle in a trench coat.** A list disguised as prose by opening paragraphs with "The
first... The second... The third..."

## Tone

**"Here's the kicker."** False suspense before an unremarkable point. Also "here's the
thing", "here's where it gets interesting", "here's what most people miss".

**"Think of it as..."** The patronising analogy, often less clear than the thing it
explains.

**"Imagine a world where..."** Futurism as persuasion.

**False vulnerability.** Polished, risk-free admissions that simulate candour.

**"The truth is simple."** Asserting clarity instead of demonstrating it. Includes the
reveal variant "but none of them is the real story. The real story is..."

**Grandiose stakes inflation.** Treating every argument as world-historically important.

**"Let's break this down."** Pedagogical hand-holding. Also "let's unpack this", "let's
explore", "let's dive in".

**Vague attributions.** "Experts argue", "industry reports suggest", "observers have
cited". If you cannot name the source, you do not have one. See
[documentation.md](documentation.md) for the citation requirement.

**Invented concept labels.** Compound abstractions such as "supervision paradox" or
"acceleration trap", used as though they were established terms.

## Formatting

**Em-dash addiction.** A human writer uses a handful per piece; models use twenty or
more. Prefer a comma, colon, parenthesis or full stop, chosen by the job the dash was
doing.

**Bold-first bullets.** Every list item opening with a bolded phrase. Very common in
generated READMEs, especially alongside emojis.

**Unicode decoration.** Arrows and smart quotes that no one types by hand. Use `->` and
straight quotes.

## Composition

**Fractal summaries.** A summary at every level: subsection, section, document.

**The dead metaphor.** One metaphor repeated five to ten times.

**Historical analogy stacking.** Rapid-fire lists of famous companies to borrow
authority. Especially common in technical writing.

**One-point dilution.** A single argument restated many ways to feel comprehensive.

**Content duplication.** The same passage appearing twice in one document.

**The signposted conclusion.** "In conclusion", "to sum up", "in summary". Competent
writing does not announce that it is concluding.

**"Despite its challenges..."** Raising problems only to dismiss them in the same breath.

---

Any of these used once may be fine. The defect is several appearing together, or one
appearing repeatedly. Write varied, specific, imperfect prose.

## Use With

- [documentation.md](documentation.md) for what documentation must contain
- [code-review.md](../development/code-review.md) when reviewing someone else's prose
- [shared-contract.md](../core/shared-contract.md) for the default operating rules

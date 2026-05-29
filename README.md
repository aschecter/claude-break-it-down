# break-it-down

A Claude skill for demystifying AI-generated work products — code, math, papers, chains of reasoning — so the human user can understand and audit what was produced.

## What it does

When invoked, the skill translates a technical artifact into language a smart colleague outside the subfield can follow, while preserving enough precision that the user can defend, cite, and build on the work. It is structured around three goals: demystify what the artifact actually does, surface the design choices that were made, and make the artifact auditable for someone who needs to take ownership of it.

The default response structure has five parts: a one-paragraph plain-English summary, a piece-by-piece walkthrough that names every symbol and defines every nontrivial variable, a selective treatment of the design choices that could have gone differently, an explicit "what it's *not* doing" section to prevent later misreadings, and a section flagging where AI judgment came in versus where the user's instructions were strictly followed. Length scales with the complexity of the artifact.

## When to use it

The skill is invocation-only. It triggers when the user explicitly says **"break this down"** or **"in plain English"** (or a close variant like "break it down for me," "explain this in plain English," "plain-English version"). It does *not* trigger on generic requests like "explain X," "walk me through," or "what does this do" — those are answered normally.

The skill also asks the user what to break down if no artifact is explicitly pointed at, rather than guessing.

## What it isn't

This is not a sanity-check or an adversarial review. If a real problem with the artifact surfaces during explanation, the skill names it once, briefly, in the relevant section — but it does not append a "concerns" section, stack disclaimers, or pivot into auditing the artifact. The job is demystification, not critique.

## Design principles

A few principles run through the skill and explain most of its rules:

- **Don't let naming substitute for showing.** When code implements a formula or a named method, the formula is rendered explicitly rather than referenced by name. When math uses notation, every symbol is annotated the first time it appears. "Classical reliability attenuation," "InfoNCE-style loss," "the c-prime path" — all of these are names that may or may not be shared, so the skill shows the underlying thing.
- **Selective on choices, comprehensive on notation.** The choices and AI-judgment sections highlight the few decisions that actually matter. The walkthrough section, by contrast, owes the reader an annotation for every symbol and a definition for every nontrivial variable.
- **Equations alongside prose.** Math is rendered in LaTeX with prose that pairs each symbol to its meaning, so the reader can link expression to interpretation without leaving the explanation.
- **Match length to complexity.** The structure scales: short artifacts get short explanations, dense ones get longer ones. The failure mode to avoid is "ritualistically thorough" rather than "too terse."

## Register and probing

The default register is "smart-friend": plain English, technical when it has to be, no jargon-as-fog. The skill explicitly assumes the reader can handle a one-sentence definition of an unfamiliar term but shouldn't have to decode acronyms or look up named methods.

On a technical follow-up, the skill answers the technical question at the level it was asked, using whatever vocabulary precision requires — but keeps the explanatory glue (why this matters, what it implies) in accessible prose. For deeper still ("just the math," "skip the explanation"), the user says so explicitly.

If the user says "ELI5" or "even simpler," the skill drops further: analogies, no acronyms, no field-specific vocabulary unless defined inline.

## Installation

Upload `SKILL.md` (or the zipped skill folder) in Claude's skill settings. The skill is then available in new conversations and triggers on the phrases listed above.

## Files

- `SKILL.md` — the skill definition with metadata frontmatter, structure guidance, register rules, and a worked example.

## Author

Aaron Schecter (University of Georgia, Terry College of Business). Built collaboratively with Claude in May 2026.

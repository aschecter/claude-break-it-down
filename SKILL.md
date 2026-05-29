---
name: break-it-down
description: Explain an artifact (code, math, a paper, a chain of reasoning, a model output) in plain language so the user can understand and audit what was produced. Use this skill ONLY when the user explicitly says "break this down," "in plain English," or a very close variant ("break it down for me," "explain this in plain English," "plain-English version"). Do NOT trigger on generic "explain X," "what does this do," "walk me through," or casual requests for clarification — those should be answered normally. The user is an academic researcher who needs to understand and take ownership of work products an AI helped produce; the goal is demystification and auditability, not sanity-checking.
---

# Break It Down

## When this skill runs

The user has just said "break this down" or "in plain English" (or a close variant). They are pointing at an artifact — code, a mathematical derivation, a model output, a section of a paper, a chain of reasoning — and they want to understand it well enough to take ownership of it.

This is a different mode from sanity-checking. The user is not asking "is this right?" They're asking "what is this, actually?" The goal is demystification. You are translating a piece of technical work into language a smart colleague who isn't in the weeds could follow.

## What the user wants

The user is an academic researcher (Associate Professor, MIS). They are comfortable with social science theory, statistics, regression, and the general shape of ML/AI methods. They are not necessarily fluent in every framework, library, or piece of math that shows up in their work — especially when an AI produced it for them. They want to understand their own work product well enough to defend it, cite it, build on it, and notice later if something is off.

So the explanation needs to do three things:

1. **Demystify**: say what the thing actually does, in plain language, without jargon-as-disguise. If a piece of code does a weighted average, say it does a weighted average — don't hide it behind the function name.
2. **Surface the choices**: every nontrivial artifact involves design choices. Which loss function? Which prior? Which sample restriction? Which baseline? Which way to handle missing data? Name these choices and say why each one was made (or, if you don't know, say so).
3. **Make it auditable**: tell the user what they should remember, what they might forget, and what someone reviewing this work would push on.

## How to ask before you start

If the user said "break this down" or "in plain English" without pointing at anything specific, ask them what to break down. Don't guess. The conversation may have several candidate artifacts (a function, a paragraph, an equation, an output), and picking the wrong one wastes the explanation.

A good clarifying question is short: "Which piece — the [X], the [Y], or something else?"

If they pointed at something clearly (a specific function, equation, file, paragraph), proceed.

## The default structure

Use this structure unless the artifact is small enough that it would feel bloated. Adapt the headings to the artifact — for code, "what each part does" might be a function-by-function walkthrough; for a paper, it might be section-by-section.

**1. The one-paragraph plain-English summary.**
What does this thing do, in one paragraph, in language a smart colleague outside the subfield could follow? Lead with this. No setup. No "Let me walk you through it." Just say what it is.

**2. How it works, in pieces.**
Break the artifact into its meaningful parts and explain each one. For code, this is usually function-by-function or block-by-block. For math, term-by-term or step-by-step. For a paper, section-by-section. Use the actual names from the artifact, but translate them. "The `compute_weights` function does X" is better than "There is a function that does X" — but "computes weights using inverse propensity scoring, which means it gives more weight to observations that were unlikely to receive the treatment they got" is much better than "computes weights using IPW."

For mathematical artifacts, this section needs to be comprehensive about *notation* even though section (3) is selective about *choices*. Every equation in the original artifact that does substantive work should appear in the explanation, and every symbol in those equations should be named. It is fine to be selective about what to highlight in the choices and judgment sections — most artifacts have a few high-stakes decisions and many trivial ones — but it is not fine to skip equations or leave indices unexplained on the grounds of "picking what matters." If the original artifact bothered to write the symbol, the explanation owes the reader an annotation.

For code artifacts, two parallel rules apply.

**Define variables when in doubt.** Variable names in code carry varying amounts of self-documentation. `n_per_cell` is mostly self-explanatory; `var_struct_M`, `cp`, `mv_trial`, `sM_lat`, `Pm`, `XtXinv_m` are not. When a variable's meaning is not obvious from its name alone, define it explicitly the first time you reference it in prose — what it represents substantively, not just what the code line computes. The bar is low: a clause like "`cp` (the direct effect of $X$ on $Y$, also called $c'$ in mediation notation)" costs nothing and prevents the reader from carrying an opaque token through the rest of the explanation. When in doubt, define.

**Don't assume the underlying math is shared.** When code implements a statistical concept, a formula, or a method by name, *show* the math the code is implementing, don't just name it. "This uses the classical reliability attenuation formula" is naming; "this divides the observed effect by reliability because observed = latent × reliability, so latent = observed / reliability, which inflates the latent value to compensate for the attenuation that measurement noise will introduce" is showing. The same goes for "ICC," "joint significance test," "intraclass correlation," "Fritz-MacKinnon," "the c-prime path," "noise-contrastive estimation" — these are all named knowledge that may or may not be shared. When the code is operationalizing a formula, render the formula in LaTeX, then point at the line that implements it. The reader should be able to follow the code without having to look up the math elsewhere.

The unifying principle for both rules: don't let *naming* substitute for *showing*. A name is a pointer; the explanation owes the reader the thing the name points to.

**3. The choices that were made.**
This is the section that matters most for auditability. Pull out the design decisions — the ones that another reasonable person could have made differently. Examples: choice of loss function, choice of regularization, choice of cluster structure, choice of test statistic, choice of how to handle ties or zeros or missing data, choice of how to define the outcome, choice of baseline, choice of sample restriction. For each, say what was chosen and why (or "I'm not sure why this was chosen — worth flagging"). Don't be exhaustive; pick the ones that actually matter.

**4. What it's NOT doing.**
This is the section that prevents the user from later realizing they thought the code did something it doesn't. If the model is logistic regression, say it's not doing causal inference. If the script computes correlations, say it's not adjusting for confounders. If the function returns predictions, say it's not returning uncertainty. Pick the misreadings most likely to occur given the artifact.

**5. Where AI judgment came in.**
If the artifact was AI-generated (which it usually will be when this skill is invoked), flag the places where the AI made a judgment call that wasn't strictly dictated by the user's instructions. "You said cluster the standard errors; the model clustered by firm-year rather than firm, because [reason]." This is the auditability layer — the user needs to know what's theirs and what's the model's.

If sections (3), (4), or (5) would be empty or trivially short for the artifact in question, skip them rather than padding. A two-line function does not need a "what it's not doing" section.

## Length should track complexity

Match the length of the explanation to the complexity of the artifact, not to the structure above. A short regex, a one-line formula, or a five-line function gets a short explanation — often just the summary paragraph plus one or two sentences on choices. A 300-line training loop, a multi-stage estimator, or a section of a paper with several interacting moves earns a longer treatment. The structure above scales: for simple artifacts, collapse sections together; for complex ones, expand each.

The failure mode to avoid is the opposite of usual: not "too terse," but "ritualistically thorough." Don't produce a five-section explanation of a two-line artifact because the structure says to.

## Math: equations alongside prose, with every symbol named

For mathematical artifacts, render the equations (in LaTeX) and pair each meaningful expression with prose that says what it means. The user wants to link expressions to meaning — that requires both visible. Don't replace equations with prose ("a weighted sum of the residuals" without showing the weighted sum), and don't drop equations in without translation.

**Critical: name every symbol the first time it appears.** Indices, subscripts, superscripts, set memberships — all of them. It's tempting to translate the *operation* an equation performs while leaving the notation unexplained, but that's exactly what makes math feel opaque: the reader can follow "this is a weighted average" but can't tell what $w_{ij}^{(t)}$ actually ranges over. Treat unexplained notation as a failure even when the surrounding prose is clear.

The pattern is:

> $e_{ci} = (t_{ci}, m_{ci}, p_{ci}, \chi_{ci})$
>
> This is the $i$-th event in context $c$. The subscript $c$ indexes the behavioral context (a team, a classroom, an organization — whatever unit groups events together). The subscript $i$ indexes events within that context, running from 1 to $N_c$, the total number of events observed in $c$. Inside the tuple: $t_{ci}$ is the timestamp, $m_{ci}$ is the action type (the "mark"), $p_{ci}$ is who participated and in what role, and $\chi_{ci}$ is any other event-level information (covariates).

Note that this annotates the indices, the running ranges, and the set memberships — not just the high-level meaning of the expression. For a longer artifact, do this for every new piece of notation introduced; once a symbol has been named, you can use it freely.

For step-by-step derivations, do the same per step: show the step, say what just happened and why, and name any new notation introduced.

When in doubt about whether to annotate a symbol, annotate it. The cost of over-annotating is small (a few extra sentences); the cost of leaving a symbol unexplained is that the reader has to either guess or stop and reverse-engineer.

## Register

Default to smart-friend register: plain English, technical when it has to be, but never jargon-as-fog. Assume the user is a researcher who can handle a one-sentence definition of an unfamiliar term but shouldn't have to decode acronyms.

**The trap is staying inside the formal vocabulary the artifact uses.** For math: "InfoNCE-style loss" or "noise-contrastive estimation" or "softmax over a sampled comparison set" all *describe* what is happening accurately, but in the artifact's own terms. The smart-friend version translates one level out: "this is asking the model to pick the event that actually happened out of a small list of plausible alternatives." For code: the equivalent trap is naming the statistical concept a piece of code implements without showing what that concept is — "this uses the classical reliability attenuation formula" names a thing without telling the reader what it is, while "observed effects are smaller than latent ones by a factor of reliability, so the latent value is inflated by dividing by reliability" shows it. Both formal names and plain versions can appear — formal terms for precision, plain version for meaning — but if only the formal version appears, the artifact has been *named* rather than *explained*.

A useful internal test: read your own explanation and ask "would a colleague who hadn't seen this method before understand what the code or equation is doing, or have they just learned what to call it?" If only the latter, push harder.

If the user says "ELI5" or "even simpler" or similar, drop further: analogies, no acronyms, no field-specific vocabulary unless defined inline.

If the user probes with a technical follow-up ("why that loss function specifically?", "what's the gradient look like?"), answer the technical question at the level it was asked. Use whatever vocabulary is needed for precision — that's the point of the follow-up. But keep the explanatory glue (the *why* this matters, what it implies, how it connects to the broader artifact) in accessible prose. Jargon for precision, prose for meaning.

If the user wants to go further still — pure technical, no intuition — they'll say so ("just the math," "skip the explanation," "more technical"). Don't preemptively drop the explanatory layer; it's the part that's hardest to add back without losing the thread.

## Things to avoid

**Don't audit the artifact under the guise of explaining it.** This skill is not sanity-check. If you spot a real problem (a bug, a wrong claim, a citation that doesn't support what it's cited for), name it once, briefly, in the appropriate section — usually "the choices that were made" or "where AI judgment came in." Don't add a "concerns" section. Don't append warnings. Don't stack disclaimers.

**Don't perform humility.** "I might be wrong but..." padding adds nothing. If you're actually uncertain about what a piece does, say so concretely: "I'm not sure whether this uses the bias-corrected or uncorrected estimator — worth checking."

**Don't pad with structure that doesn't earn its place.** A short artifact gets a short explanation. The default structure above is a default, not a template to fill out regardless of fit.

**Don't restate the artifact.** The user has the artifact. The explanation should add something — translation, context, the *why* behind choices. If a paragraph of your explanation could be replaced by the artifact itself, cut it.

**Don't lead with caveats.** Lead with the answer (the one-paragraph summary), then structure.

## A short example

User: "break this down" pointing at a block of R code that fits a multilevel logistic regression with crossed random effects for participant and item, predicting a binary choice outcome.

A good response would start with something like:

> This code fits a model that predicts a yes/no choice from your experimental data, while accounting for the fact that the same people made multiple choices and the same items were seen by multiple people. Both of those create non-independence, and the model handles it by giving each person and each item their own little adjustment that the model estimates from the data.
>
> **The pieces.** `glmer(choice ~ condition + (1|participant) + (1|item), family = binomial, data = df)` is the main call. `choice ~ condition` is the part you care about — the effect of your experimental manipulation. The two `(1|...)` terms are the random intercepts for participant and item; they let each person and each item have their own baseline rate of choosing "yes" without estimating a separate parameter for every single one. `family = binomial` tells the model the outcome is binary and to use logistic regression.
>
> **The choices.** It's using random *intercepts* only, not random slopes — so it's assuming the effect of `condition` is the same across all participants and items. That's a real assumption; if you think people respond to the manipulation differently, you'd want `(1 + condition | participant)`. It's also using the default optimizer; with crossed random effects on a sample your size, convergence warnings are common, and the fix is usually to switch optimizer.
>
> **What it's not doing.** It's not testing whether the random effects are necessary — it's assuming they are. It's not giving you marginal (population-averaged) effects, only conditional ones. If you want odds ratios, you'll need `exp(fixef(model))`.

Notice that this example does the things the skill is for: plain-language summary first, then pieces with the actual names, then choices the AI made (random intercepts vs. slopes, default optimizer), then what someone might wrongly assume the code does. No padding, no audit framing.

## When to offer more

End the explanation cleanly. If there are obvious follow-up directions (a deeper dive on the math, the specific paper a method comes from, a comparison to alternatives), it's fine to offer one — briefly, as a single line. Don't list four options.

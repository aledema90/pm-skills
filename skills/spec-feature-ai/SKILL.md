---

## name: spec-feature-ai

description: Turns a one-line AI feature idea into a short spec that pins down what "good" means — a quality metric with a target, the failure cases that matter, and the human fallback — before anyone builds it. Use when someone brings you an AI feature idea and you need to spec it.
disable-model-invocation: true

# Spec an AI Feature

| Field                    | Value                                                                                                                                                                                                                                                                          |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| name                     | spec-feature-ai                                                                                                                                                                                                                                                                |
| description              | Turns a one-line AI feature idea into a short spec that pins down what "good" means — a quality metric with a target, the failure cases that matter, and the human fallback — before anyone builds it. Use when someone brings you an AI feature idea and you need to spec it. |
| disable-model-invocation | true                                                                                                                                                                                                                                                                           |

An AI feature is never simply "done": it's right some percentage of the time, and the spec has to say which percentage is acceptable and what happens the rest of the time. Most AI specs skip this and describe only the happy path.

## Before writing

Four things you need to know:

1. Who uses it, and what are they doing right before and right after?
2. What's the input, and where does it come from? (user text, a document, product
   data — and how messy it is)
3. What does a wrong output cost? (an annoyed click, a wrong decision, money,
   compliance)
4. How do they do this today, and how good is that baseline, if anyone has
   measured it?

Skip any the conversation already answers. Ask the rest in one message, not one at a time. If the idea is too vague to answer question 1 or 2 (e.g. "add AI to search": no named user, no named input), say so and ask for the specific job the user is trying to get done instead of guessing.

## The spec

Write it in this order. Keep each section as short as its content allows (the failure table is the one part that runs longer); a spec nobody finishes reading protects nobody.

**Problem and user.** One or two sentences: who, doing what, what hurts today. No mention of AI here. If the problem doesn't hold up without the word "AI", say so.

**Behaviour.** What goes in, what comes out, in one concrete example, plus one awkward example (messy input, edge case). Then what the feature explicitly does _not_ do. If the input varies by language, region or customer type, say so here: quality gets measured per group in the next section.

**Quality metric.** One primary metric a person can actually measure (e.g. "% of suggested replies sent with no or only minor edits", "% of extracted fields matching a human reviewer"), with how it's measured: who labels, on how many examples (a few hundred real cases is a sensible start), and how often.  
Say where the eval set comes from: real past cases, not invented ones.  
Define the label scale in a line, since "edited" is noisy: for example send as is / minor edit (wording, tone) / major rewrite or unsafe. If the input varies by group (language, region, customer type), sample and report per group.

- **Target.** Anchor it to a baseline of the primary metric (question 4), not to a round number. A baseline of something else (say, handle time) doesn't count: it can anchor a guardrail, not the target.
- **No baseline?** Don't invent a target. Make measuring the baseline the first step (for example, run the feature silently next to the current process and have people label both), and leave the target open until it's done. Any threshold that depends on it (guardrails, rollback) is written "set from the baseline", with no placeholder number.
- **Guardrails.** Metrics that must not get worse: pick the ones that apply from latency, cost per call, complaint rate, and any hard rule for this feature (e.g. "never promises a refund").

If the metric can't be measured before launch, name the proxy to use instead.

**Failure cases and fallback.** One table, three to five rows, only the failures that matter for _this_ feature; if more apply, keep the top three to five by severity.  
Columns: what goes wrong, how bad it is (and how likely, only if you have data or a labelled guess), what the user sees and can do (edit, retry, skip, reach a person), and what triggers that fallback.  
Pick from: wrong but confident output, refusal or empty output, unsafe or off-brand content, bad or missing input, slow or unavailable model, drift over time. The feature is not shippable without a fallback for every serious row.

When a person reviews every draft, add automation bias as an extra row outside the cap: they approve without reading. The fallback is a design choice (show the facts the draft used, require a check for risky content, audit a sample of sent drafts).

Triggers must be things the system can actually detect: a validation rule, a banned-phrase or policy check, missing input, a timeout, a user report. Don't rely on a model "confidence score" unless the model really provides one.

**Launch and learning.** How it rolls out (internal, small %, opt-in), what feedback signal you collect from users, and the threshold at which you pause or roll back.

**Open questions.** Anything that needs an answer from engineering, legal, or data before build, with an owner if known. Put assumptions you made (numbers, scope) in a short "Assumptions" list under it.

## How it should sound

- Plain, specific, numbers over adjectives. "Wrong in about 1 of 20 cases" beats "occasionally inaccurate".
- No invented figures. If a number is a guess, label it as an assumption. If a wrong guess would change the design, add it to Open questions too; otherwise leave the threshold "to set from the baseline".
- Headings and a table are fine here: this is a document, not a message to paste.
- Write in the language of the conversation unless told otherwise.

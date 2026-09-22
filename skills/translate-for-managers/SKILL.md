---
name: translate-for-managers
description: Turns a technical feature or idea into a plain-English ASD-STE100 pitch for non-operational stakeholders — one sentence, one analogy, the impact, and the decision you need. Use when someone asks "translate it for my manager", "explain it like I'm 5" or for an analogy.
disable-model-invocation: true
---

Before writing anything, ask two questions if they're not already clear from context:

1. Who is this for? (their role, how technical they are)
2. What decision or reaction do you need from them?

Then produce exactly four parts, in this order:

**1. The sentence** — one sentence on what changes for the customer or the business.
No feature names, no jargon. If you can't say it in one sentence, you don't
understand it well enough yet — ask a follow-up instead of guessing.

**2. The analogy** — Pick something the audience already knows from
daily life, not from tech. State explicitly where the analogy breaks down, in one
short line — an analogy that isn't caveated will be taken too literally.

**3. The impact** — one number or order of magnitude. "Cuts X by roughly half" beats
"significantly improves X." If there's no number available, say what you'd need to
measure it instead of inventing one. If not possible, skip the impact stating that this part needs to be evaluated.

**4. The ask** — the exact decision, approval, or action you need from them, stated
as a question. Not "for your awareness" — a specific ask.

Keep the whole thing to 4-6 lines total. If the draft runs longer, cut detail, not
the four parts.

## Example

Input: "We're adding model versioning to the platform."

Output:

- **What changes:** Teams stop losing hours to overwritten or wrong files.
- **Analogy:** It's like Google Docs version history, instead of emailing around
  `final_v7_ACTUAL.docx`. Where it breaks down: our files are much larger, so
  restoring an old version takes longer than in Docs.
- **Impact:** Cuts file-related rework incidents by roughly a third, based on the
  last quarter's incident log.
- **Ask:** Can I get sign-off to spend the next 2 sprints on this?

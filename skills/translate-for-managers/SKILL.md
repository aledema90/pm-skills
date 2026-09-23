---
name: translate-for-managers
description: Turns a technical feature or idea into a short, plain-English message for a non-technical stakeholder, written the way a person would say it — what changes, an analogy with its limit, the impact, and the decision you need. Use when someone asks to "translate it for my manager", to "explain it like I'm 5", or for an analogy for a technical feature.
disable-model-invocation: false
---

# Translate for Managers

Before writing anything, ask two questions if they're not already clear from context:

1. Who is this for? (their role, how technical they are)
2. What decision or reaction do you need from them?

Then write a short message that moves through four beats, in this order. These are
the beats of the message, not its headings: the reader should never see them named.

**What changes.** Open with one sentence on what changes for the customer or the
business. No feature names, no jargon. If you can't say it in one sentence, you
don't understand it well enough yet — ask a follow-up instead of guessing.

**The analogy.** Pick something the audience already knows from daily life, not
from tech. Say where it stops holding, in a few words — an analogy that isn't
caveated will be taken too literally.

**The impact.** One number or order of magnitude. "Cuts X by roughly half" beats
"significantly improves X." If there's no number, say what you'd need to measure
instead of inventing one. If even that isn't possible, say plainly that the impact
still needs to be worked out, rather than dropping it.

**The ask.** End on the exact decision, approval, or action you need, as a real
question. Not "for your awareness" — a specific ask.

## How it should sound

The output is a message the user will paste and send as themselves, so it has to
read like they typed it.

- **No labels, bold, headers, or bullets in the output.** The order of the beats is
  the structure; ordinary transitions do the work the labels used to do ("It's a
  bit like…", "The catch is…", "Can I…?").
- One short paragraph, 3–5 sentences. Two paragraphs only if the audience really
  needs the extra context.
- First person, in the register of the channel: Slack is shorter and looser than
  email. Contractions are fine. Plain words over formal ones.
- Skip the usual AI tells: no opening pleasantries, no "Here's a summary", no
  closing recap, no stacked dashes, no lists of three adjectives.
- Write in the language of the conversation unless told otherwise.
- Deliver only the message, ready to paste. No preface explaining what it is.

## Example

Input: "We're adding model versioning to the platform."

Output:

> Teams will stop losing hours to overwritten or wrong files. It works like version
> history in Google Docs, instead of emailing around `final_v7_ACTUAL.docx`. The
> difference is that our files are much bigger, so going back to an old version
> takes longer than it does in Docs. Based on last quarter's incident log, it should
> cut file-related rework by about a third. Can I get your sign-off to spend the next
> two sprints on it?

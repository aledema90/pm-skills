# pm-skills

Claude Agent Skills for Product Owners and Product Managers.

Every skill here comes from a real, recurring problem in day-to-day PO work — not
a hypothetical use case. Install what's useful, ignore the rest.

## Skills

### 🗣️ explain-it-to-me
**Problem:** "Explain it to me like I'm 5" — upper management asks for the plain-English
or analogy version of a technical feature, and you improvise it on the spot, badly.
**Fix:** One sentence on what changes, one analogy (with its limit stated), the impact,
and the decision you're asking for.

### 📋 spec-feature-ai
**Problem:** "I want an AI assistant that does X" turns into a spec with no success
metric and no failure plan.
**Fix:** Turns a one-line AI feature idea into a spec with a quality metric, failure
cases, and a human fallback.

### 🔍 refine-user-stories
**Problem:** A vague ticket gets built the wrong way because nobody asked the obvious
questions first.
**Fix:** Asks who uses it, what data, what happens on failure — before writing
acceptance criteria.

### ✋ reply-to-stakeholders
**Problem:** Saying "no" or "not now" to a stakeholder without sounding dismissive
or vague.
**Fix:** A 3-line answer: what's possible, what isn't and why, what you propose instead.

## Install

**Claude Code**
\`\`\`bash
git clone https://github.com/<tuo-handle>/pm-skills.git ~/.claude/skills/pm-skills
\`\`\`

**claude.ai / Desktop**
Download the skill's zip from [Releases](../../releases) and upload it under
Settings → Capabilities → Skills.

## Why

Built by a Senior Product Owner working across telco, fintech, and B2B SaaS,
using Claude daily for actual product work — not demos.

## License

MIT — see [LICENSE](LICENSE).

# pm-skills

My take on Claude Agent Skills for Product Owners and Product Managers.

Every skill here comes from a real, recurring problem in day-to-day product work — not a
hypothetical use case. Install what's useful, ignore the rest.

`the-diplomatic-no`, `meeting-to-obsidian` and `spec-feature-ai` are **invoked explicitly**
(`disable-model-invocation: true`): Claude won't fire them on its own, you ask for
them by name. `translate-for-managers` can also trigger by itself when you ask for
the plain-English version of something technical.

## Skills

### Stakeholder communication

#### 🗣️ translate-for-managers

**Problem:** "Explain it to me like I'm 5" — upper management asks for the
plain-English version of a technical feature, and you improvise it on the spot,
badly.

**Fix:** A short message that reads like you wrote it: what changes, an analogy
with its limits stated, the impact, and the decision you're asking for.

#### ✋ the-diplomatic-no

**Problem:** Saying "no" or "not now" to a stakeholder without sounding dismissive
or vague.

**Fix:** A short reply that reads like you wrote it: what's possible, what isn't
and the real constraint behind it, and what you propose instead.

### Meetings & decisions

#### 🗂️ meeting-to-obsidian

**Problem:** Meeting transcripts pile up somewhere nobody reads, and the action
items you personally own get lost in them.

**Fix:** Turns each meeting into a compact Obsidian note (overview + key points,
filed by project) and appends the items you own to a to-do list, with priority
markers that stay current on re-runs.

> **Needs configuration before first use.** Open its `SKILL.md` and fill in the
> Configuration block: where transcripts come from, your vault paths, and your name
> as it appears in action items. It's source-agnostic — a notetaker bot that emails
> summaries, a folder of transcript files, an MCP connector, or text you paste in.

### Product work

#### 🧪 spec-feature-ai

**Problem:** AI feature ideas arrive as one line, and the spec only covers the happy
path — nobody defines what "good enough" means or what happens when it's wrong.

**Fix:** A short spec with a measurable quality metric and target, the failure cases
that matter, and the human fallback for each, plus rollout and rollback thresholds.

## Coming soon

Planned, not written yet — don't expect to find them in the repo or the releases.

**Stakeholder communication**

- **bad-news-early** — announces a delay or a problem before it becomes a surprise:
  what happened, the impact, what you're already doing, and what you need, without
  minimising or dramatising.

**Meetings & decisions**

- **meeting-prep-one-pager** — a five-line brief before a meeting: what you want out
  of it, who decides, and the objections to expect.
- **follow-up-mail** — the message after a meeting: what was decided, who does what,
  and by when.

**Product work**

- **refine-user-stories** — asks who uses it, what data, what happens on failure,
  before writing acceptance criteria.

**Personal workflow**

- **weekly-note-to-self** — turns the week's chaos into a few lines: what you got
  done, what's blocked, and what you want from Monday. Useful raw material for 1:1s
  and reviews.

## Install

### Claude Code

Skills live one level deep in `~/.claude/skills/`, so copy the skill folders rather than cloning the repo into place:

```bash
git clone https://github.com/aledema90/pm-skills.git
cp -r pm-skills/skills/* ~/.claude/skills/
```

To pull updates with `git pull` instead of re-copying, symlink them:

```bash
git clone https://github.com/aledema90/pm-skills.git ~/src/pm-skills
ln -s ~/src/pm-skills/skills/* ~/.claude/skills/
```

For a single project instead of your whole account, use `.claude/skills/` inside the
project.

### Claude.ai / Desktop

Download a skill's `.zip` from
[Releases](https://github.com/aledema90/pm-skills/releases) and upload it under
**Settings → Capabilities → Skills**. One zip per skill — take only what you need.

## Why

Built by a Senior Product Owner working across telco, fintech, and B2B SaaS, using
Claude daily for actual product work — not demos.

## Contributing

Issues and PRs welcome, especially "this failed on my setup" reports for
`meeting-to-obsidian` — it's the one skill with real environment variance.

## License

MIT — see [LICENSE](LICENSE).

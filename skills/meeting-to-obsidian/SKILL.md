---
name: meeting-to-obsidian
description: Turns recorded-meeting transcripts from a given period into Obsidian notes — one compact note per meeting, filed into a project subfolder — and appends the action items you own to a to-do list, with priority markers kept up to date on re-runs. Requires the Configuration block below to be filled in before first use.
disable-model-invocation: true
---

# Meeting to Obsidian

Turns meeting transcripts into permanent Obsidian notes and keeps a personal to-do list in sync with the action items you own.

The notes are **not** verbatim transcripts. Each one is a short Overview paragraph plus a Key Points list — the substance someone would want to recall six months later, not the raw conversation. The transcript stays wherever it already lives.

Designed to be **re-run on a schedule** (weekly is the common case) over an overlapping window: it skips meetings that already have a note, so running it twice costs nothing and misses nothing.

---

## Configuration

**Read this section first, every run.** Everything below it refers back to these values. They live here, in this file, so the rest of the skill stays portable.

If a required value is still a `<placeholder>`, stop and ask the user for it rather than guessing — then offer to write the answer into this file so the next run doesn't ask again.

### Required

| Setting             | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| `TRANSCRIPT_SOURCE` | `<how meetings reach you — see "Source patterns" below>`    |
| `NOTES_FOLDER`      | `<absolute path to the meeting-notes folder in the vault>`  |
| `TODO_FILE`         | `<absolute path to the to-do list markdown file>`           |
| `USER_IDENTITY`     | `<full name, plus the email or handle used in transcripts>` |

### Optional — sensible defaults if left empty

| Setting             | Default                 | Notes                                                     |
| ------------------- | ----------------------- | --------------------------------------------------------- |
| `DEFAULT_PERIOD`    | last 7 days rolling     | Overridden whenever the user names a period.              |
| `PROJECT_FOLDERS`   | discover from the vault | See "Project routing".                                    |
| `EXCLUDED_MEETINGS` | none                    | Case-insensitive substring match on the meeting name.     |
| `NOTE_LANGUAGE`     | match the transcript    | Set explicitly if notes should always be in one language. |
| `TODO_LANGUAGE`     | match `NOTE_LANGUAGE`   | The to-do file often stays in the user's own language.    |
| `OPEN_HEADING`      | `## Not done yet`       | The heading open items live under in `TODO_FILE`.         |
| `DONE_HEADING`      | `## Done`               | Archive section. Never written to.                        |

### Source patterns

`TRANSCRIPT_SOURCE` describes where meetings come from. Four shapes cover almost
everything; record which one applies and the details it needs.

1. **Notetaker bot that emails a summary** (Otter, Fireflies, Read.ai, an internal
   agent). Record: the sender address, and how to reach the transcript from the
   email — usually a link whose last path segment is a filename to resolve in the
   linked storage. The email body typically already carries an executive summary
   and an action-items list; that is usually enough to write the note without
   opening the transcript at all.
2. **A folder of transcript files** (Granola exports, Zoom/Teams downloads, a synced
   folder). Record: the folder path and the filename convention that encodes the
   meeting name and date.
3. **An MCP connector or API** that lists meetings directly. Record: the tool names
   for listing and for reading a transcript.
4. **Pasted by the user** at run time. No configuration needed — skip straight to
   Step 3 with whatever they provide.

Whatever the shape, Step 2 needs the same four things per meeting: **name, date,
participants, and content** (summary + action items with owners, or a transcript to
derive them from). If a source can't supply owners for action items, say so once in
the final report rather than guessing who owns what.

---

## Step 1 — Determine the period

Use `DEFAULT_PERIOD` unless the user named one. State the period used in the final
report.

For a long backfill (several months), prefer one broad query over the whole span to
one query per month, paginating if the source requires it. Cut the page size and
retry if a call fails for returning too much at once.

## Step 2 — Fetch the meetings

Pull the list for the period from `TRANSCRIPT_SOURCE`.

**Apply `EXCLUDED_MEETINGS` immediately**, on the names alone, before resolving or
reading any transcript — there is no point paying for content that won't be written.
Excluded meetings also produce no to-do items. If everything in the period is
excluded, say so plainly instead of padding the report.

Then, for each remaining meeting, get its content. Read the full transcript only
when the summary is thin or the source has no summary at all; extract what the note
needs and discard the rest rather than carrying transcript text forward. If a
transcript is too large to read, the summary alone is fine.

**Same-named meetings on the same day.** A recurring series can genuinely meet twice
in a day with different content — that is two meetings, and the filenames need a
short parenthetical to tell them apart. Only treat them as one if the content is
essentially identical, i.e. a duplicate send.

**Parallelising a large run.** If there are many meetings, subagents can read and
draft independent ones in parallel — but do the exclusion filter and the Step 3
skip-check yourself first, so no subagent works on a meeting that won't be written.
Tell each subagent explicitly which subfolder its meetings belong in; a fresh
subagent doesn't know the routing rules.

## Step 3 — Skip meetings that already have a note

Glob `NOTES_FOLDER` **recursively** (`**/*.md`) — notes live in subfolders, so a
flat listing will miss them and you will write duplicates.

A meeting already has a note if some file, in any subfolder, starts with that
meeting's date and contains a recognisable fragment of its name. Exact matching is
wrong here: past runs may have chosen different disambiguating suffixes. Skip those
meetings entirely — never overwrite, never duplicate.

## Step 4 — Write one note per new meeting

**Filename:** `YYYY-MM-DD - <Meeting Name>.md`, using the meeting's own local date.
Don't convert timezones for the filename.

**Project routing.** Each note goes in a subfolder of `NOTES_FOLDER`, not loose at
the top level. If `PROJECT_FOLDERS` is configured, match the meeting against it. If
it isn't, list the existing subfolders and infer the convention — one subfolder per
recurring project or series, plus a catch-all (`Other/`) for one-offs. A brand-new
recurring project justifies a new subfolder; a single unclassifiable meeting does
not — that goes in the catch-all. Report any subfolder created for the first time.

**Format.** Read one or two existing notes first and match whatever convention is
already there — an established vault outranks the template below. Absent a
convention, use this:

```
---
When: YYYY-MM-DD
Participants:
  - FirstName1
  - FirstName2
tags:
  - "#tag-one"
  - "#tag-two"
---
# **Overview**

<2-5 sentences of prose: what was discussed, what was decided, what happens next.
Third person, past tense.>

**Key Points**

- <a decision, a notable number or fact, or a piece of context worth recalling>
- <an action item — owner, what it is, timing if known>
```

Field rules:

- **Participants** — first names only, one per line. Drop room and equipment
  mailboxes. Strip status prefixes like `Leave - ` but keep the person. For a normal
  meeting (roughly ≤15 people) list everyone; for a broadcast (20+ recipients) the
  full invite list is noise — list the host, the presenters, and anyone who
  contributed substantively, always including the user if they were invited.
- **tags** — 1–3 short lowercase hyphenated tags in `"#tag"` form. Grep the target
  subfolder for existing tags and reuse them; near-duplicate tags (`#ai` and
  `#ai-integration` for the same thing) make the vault worse. The primary tag is
  usually the subfolder name lowercased.
- **Overview** — rewrite the source summary as flowing prose. Don't paste its
  bullets.
- **Key Points** — 5–10 bullets. This section replaces the transcript, so it has to
  carry the substance on its own.

Never paste raw speaker-turn text into a note.

## Step 5 — Extract the user's action items

For each note just written, take the action items from the source and decide which
belong on the user's list.

### 5a — Which items qualify

Keep an item if `USER_IDENTITY` is the **sole owner** or a **co-owner**.

- **Sole owner** — the owner field names the user alone.
- **Co-owner** — the owner field names the user alongside others ("Alessandro and
  Marco", "PO team — Alessandro, Giulia"), or the item text assigns the work
  jointly. Record the other owners' first names; 5b renders them.

**Match on the full name, not the first name.** Any organisation large enough to
need this skill has repeated first names, and attributing someone else's work to the
user is the failure mode that makes the list untrustworthy. Match a bare first name
only when the surrounding context makes the surname unambiguous.

Leave out items owned entirely by others, even in the user's area. And when the user
is merely a dependency — "Marco to check with Alessandro before Friday" — that is
Marco's item, not the user's. A list honest about what the user can actually close
is worth more than a padded one.

### 5b — Line format

```
- [ ] <priority> <task text, standalone> (<co-owners>[[YYYY-MM-DD - Meeting Name]], due: <due>)
```

Examples:

```markdown
- [ ] !! Rivedere la stima per la 0.29 ([[2026-07-27 - Sprint Planning]], due: 2026-08-01)
- [ ] ! Allineare il team sui contenuti ([[2026-07-27 - Sprint Planning]], con Marco, due: prima del prossimo meeting)
- [ ] Aggiornare la documentazione ([[2026-07-25 - Export Sync]])
```

- **Priority** — `!!` high, `!` medium, **nothing** for normal. No placeholder for
  normal. Goes immediately after `- [ ] `. Derived in 5c.
- **Co-owners** — `con <FirstName>, ` (`con <A> e <B>, ` for two). Omit when the user
  owns the item alone. Translate the connector to `TODO_LANGUAGE`.
- **Wikilink** — `[[note filename without .md]]`. Obsidian resolves wikilinks by
  filename regardless of subfolder, so no path. The date is already inside the
  wikilink — don't repeat it as a separate clause.
- **Due** — ISO date when the source gave a calendar date; keep relative phrases
  ("before the next meeting") as prose rather than inventing a date. No due field in
  the source means no `due:` clause at all.

### 5c — Deriving the priority marker

Take the **higher** of two signals:

1. **What the source said** — an explicit high/urgent/blocker flag → `!!`, medium →
   `!`, low or nothing → no marker.
2. **Due-date proximity**, measured from _today at run time_, not the meeting date —
   within 3 days → `!!`, within 7 days → `!`, further out, already past, or no due
   date → no marker.

Signal 2 needs a real calendar date. Items with prose due dates take their priority
from signal 1 alone.

### 5d — Where the line goes

Open items are grouped by project under `OPEN_HEADING`, one `###` heading per
project:

```markdown
## Not done yet

### POPCONS

- [ ] !! Rivedere la stima per la 0.29 ([[2026-07-27 - Sprint Planning]], due: 2026-08-01)

### OS

- [ ] ! Allineare il team sull'export ([[2026-07-25 - Export Sync]], con Marco)

### Other

- [ ] Rispondere a HR sul budget corsi ([[2026-07-22 - Catch-up]])

## Done
```

- **The project is already decided** — it's the subfolder chosen in Step 4. Reuse
  that decision verbatim; don't re-derive it. Headings match subfolder names exactly,
  so the notes folder and the to-do list share one vocabulary.
- **Reuse an existing heading** over creating a near-duplicate (`### Popcons`,
  `### Altro`). Only create a heading when no equivalent exists.
- **New headings go at the end** of the open section, just before `DONE_HEADING`.
  Never reorder existing headings — that ordering is the user's.
- **Append to the end of the project's section**, before the next `###`.
- **`DONE_HEADING` stays flat** and is never written to.
- **Loose items are left alone.** An unchecked line under `OPEN_HEADING` with no
  project heading above it is something the user typed themselves. Never relocate it.

**First run against a flat file.** If there are no `###` headings yet, do **not**
silently restructure. Show the user what the regrouped file would look like and ask.
If they agree, move only skill-written lines (the ones carrying a meeting wikilink)
and leave hand-written items untouched at the top. If they decline, start adding
headings from now on and leave the old flat items alone — a mixed file is fine and
self-corrects as items get ticked off.

### 5e — Avoid duplicates

Before appending, search the **whole** file — every project section _and_
`DONE_HEADING` — for an item with the same wikilink and similar wording. Compare on
task text plus wikilink, not the full line: an existing item may carry a different
priority marker than when it was written. Checking the archive matters most — an
item already ticked off must never reappear as a fresh open line.

## Step 6 — Refresh priorities on existing items

Due-date priority goes stale the moment it's written: an item marked `!` at six days
out should be `!!` at three, and the line won't update itself. Since the skill
re-runs anyway, the run is the moment to refresh it.

Scope this narrowly. Touch a line only if **all** of these hold:

- it sits under `OPEN_HEADING`, in any project section (**never** `DONE_HEADING`),
- it is unchecked (`- [ ]`),
- it carries a `[[YYYY-MM-DD - ...]]` wikilink — i.e. this skill wrote it, and
- its `due:` clause is an ISO calendar date.

Recompute with the 5c rules and edit **only the marker**. Never rewrite the task
text, co-owners, wikilink or due date. Never move a line between sections —
recalculating priority must not reshuffle the file. **Only ever raise a marker,
never lower it**: once a source flagged something urgent it stays urgent.

## Step 7 — Report back

Concise. The user can open the files themselves.

- Period used.
- Counts: found, excluded, already had notes, newly written.
- New notes, with filename and subfolder. Flag any subfolder created for the first
  time.
- To-do items added, grouped by section, showing priority markers and flagging
  co-owned ones.
- Priority refresh as a separate line: how many were re-checked, which changed. One
  line saying nothing changed is enough.
- Anything the source couldn't supply (missing owners, unreadable transcripts).

---

## Design notes

Why the rules are the way they are — useful when adapting this skill.

- **Exclusions filtered before reading** — reading a transcript is the expensive
  step; filtering on names first makes exclusion free.
- **Skip-if-exists before writing** — the skill is built to re-run over overlapping
  windows. Without this check, every re-run duplicates.
- **No verbatim transcript in the note** — a compact, scannable record is what gets
  re-read. A raw conversation dump doesn't, and it bloats the vault.
- **Full-name matching on owners** — attributing someone else's action item to the
  user is the one error that makes the whole list untrustworthy.
- **Co-owned items in, dependencies out** — shared work still needs tracking, and the
  co-owner clause says whom to chase. Items where the user is someone else's
  dependency aren't theirs to close, and capturing them inflates the list.
- **Plain `!!`/`!` markers** — readable and editable without depending on a plugin's
  emoji conventions.
- **Grouped by project, not by deadline** — project membership is permanent, so an
  item is filed once and never moves. Deadline buckets would force the skill to
  shuffle lines on every run, which is a real risk on a synced vault; urgency is
  already carried by the marker. A deadline-ordered view is a Dataview or Tasks
  query over this file, not a change to how it's written.
- **Upgrade-only priority** — restricting edits to a single character, in one
  direction, keeps a re-run from silently undoing the user's own edits.
- **Broadcast participant handling** — a 100-person invite list is noise; the people
  who spoke are the meaningful participants for a personal knowledge base.

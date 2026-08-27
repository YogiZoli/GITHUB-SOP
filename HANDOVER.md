# Handover pattern (STATUS.md + DECISIONS.md) — CANONICAL

Part of the GitHub SOP. Canonical source: https://github.com/YogiZoli/GITHUB-SOP
Last updated: 2026-08-27

## Why

Each project used to keep one giant "Handover" Google Doc that grew with every
session. New chats re-read the whole thing, which burned tokens and still lost
the thread. This pattern replaces that.

## The two files

Every project gets two Markdown files, kept in git, in the repo where the
project's work already lives:

**STATUS.md** — short, read first in every session. Sections, in order:

- A one-line "Canonical" note pointing at the repo and the Drive mirror.
- What the project is (one short paragraph).
- Now: where we are, one or two paragraphs.
- Session start checklist.
- Hard rules (never break).
- Architecture (what talks to what), for code projects.
- Next: the concrete next steps, each with enough context to act.
- Waiting on Zoltan: what is blocked on him, or "nothing blocking right now".
- Recently done: the last ten or so items, one or two sentences each.
- Pointers: repo, local clone, production URLs, key doc links, credentials
  location (never the secrets themselves).

Target length is 150 to 250 lines. If it grows past that, move detail into
DECISIONS.md or drop it (git history keeps it).

**DECISIONS.md** — what blocked us, what we tried, what we settled on. Newest
first, one entry per decision, a short heading with the outcome and date
(ADOPTED, REJECTED, FIXED, CLOSED, SUPERSEDED) and one or two paragraphs. This
is where "we already tried that, here is why not" lives so nobody re-solves it.

## Where each project's files live

| Project type | Location |
|---|---|
| Has a code repo | STATUS.md and DECISIONS.md in that repo, at the root |
| A workspace, no code repo | its own repo, or a channels/<name>/ folder in a parent repo |

Current placements:

- C2C: `commentclient/STATUS.md`, `commentclient/DECISIONS.md`
- PDT: `publishing-dreamteam/STATUS.md`, `publishing-dreamteam/DECISIONS.md`
- freedomOS system: `freedomOS/STATUS.md`, `freedomOS/DECISIONS.md`
- YT Evergreen (Zoltan Gero channel): `freedomOS/channels/zoltan-gero/`
- VoxFlow channel: `freedomOS/channels/voxflow/`
- HyperFrames video pipeline: `brand-frames/STATUS.md` (its own long-standing
  pattern, do not merge with this)

## The Drive mirror

The old Handover Google Doc is not deleted. It becomes a mirror of STATUS.md
plus DECISIONS.md, so Zoltan can still read it on his phone and have it read
aloud. It gets:

- A banner at the very top: "CANONICAL SOURCE: <repo>, files STATUS.md and
  DECISIONS.md. This Google Doc is a mirror ... trust the repo if they differ."
  plus a "Last mirrored: <date>" line.
- The full text of STATUS.md, then a horizontal rule, then the full text of
  DECISIONS.md.
- No emoji and no dashes as sentence punctuation, because it is read aloud.

If the project already has a separate ARCHIVE doc (C2C, PDT), that link stays
in the banner and the raw session history is never copied into the mirror.

## Cadence: when to update

1. **During a session**, edit STATUS.md and DECISIONS.md in the repo like any
   other file, on a feature branch, merged by PR. A decision made mid-session
   goes into DECISIONS.md the same session.
2. **At the end of a session**, before wrapping up:
   - Make sure STATUS.md "Now", "Next", "Waiting on Zoltan", and "Recently
     done" reflect reality. Bump the "Last updated" date.
   - Re-mirror to the Drive doc: overwrite its body with the banner plus the
     current STATUS.md plus DECISIONS.md, update the "Last mirrored" date.
3. **At the start of a session**, read STATUS.md and DECISIONS.md first (from
   the repo if working in a terminal, from the Drive mirror otherwise). State
   back the end goal, where we are, and the next step. Only open the ARCHIVE
   doc if STATUS.md and DECISIONS.md do not answer the question.

## Claude Project wiring

Each Claude Project's Space instructions carry one line:

> At the start of each chat read STATUS.md and DECISIONS.md first (repo or the
> Drive mirror). Do not read the old raw handover history unless STATUS.md
> points you there.

## Change log

- 2026-08-27: Pattern adopted. Migrated C2C, PDT, and the VoxFlow channel.
  Old one-off mini-handover docs (SESSION 27, MOD02 Router, Google-Docs MCP
  fix, Billingo and Szamlazz reviews, and similar) are archived, not migrated.

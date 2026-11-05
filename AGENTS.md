---
type: guide
status: active
tags: [meta, routing, guide]
---

You are working in a Baalda vault: a folder of plain Markdown files that people and AI assistants read and edit together. Everything durable lives in these files, so whatever you write here becomes context for the next session, yours or a teammate's.

Core context:
- Where to start: `Welcome.md`
- How Baalda works: `Getting Started/How Baalda works.md`

## Session Startup

Read this file first, then the `AGENTS.md` inside any folder you are about to work in. Each one says what the folder holds and its local rules. Do not narrate the loading unless it matters to the answer.

## Knowledge Routing

Every piece of information needs one specific home. Do not create catch-all notes.

| What it is | Where it goes | Read first |
|---|---|---|
| How to use Baalda: the basics, shortcuts, AI, teamwork | `Getting Started/` | `Getting Started/AGENTS.md` |
| One idea or method, explained on its own | `Concepts/` | `Concepts/AGENTS.md` |
| Working notes: projects, meetings, reading, reviews | `Examples/` | `Examples/AGENTS.md` |
| A vault-wide rule for AI assistants | `AGENTS.md` (this file) | |

The three folders above are a starter set. As the vault grows (`Projects/`, `Meetings/`, `People/`, whatever fits the work), give each new top-level folder its own `AGENTS.md` and a row in this table.

## Where Work Lands

1. File a note in the folder whose `AGENTS.md` describes it. If no folder fits, choose the closest one and say which you chose, or propose a new folder.
2. Use today's actual date for date-stamped notes and filenames, written `YYYY-MM-DD`. Nearby notes may be stale.
3. A note's title is its file name. Do not start a note with a `# Title` heading that repeats it.
4. Keep the vault root for control files: `AGENTS.md` and `Welcome.md`. Everything else goes in a folder.
5. Corrections get written down without asking. Vault-wide behavior goes in this file; folder-specific behavior goes in that folder's `AGENTS.md`.

## Folders And Indexes

1. Every folder has an `AGENTS.md` index: a one-line purpose, a table of its notes, and its rules. Open it before filing work there.
2. When you add, rename, move or delete a note, update its folder's index table in the same change.
3. When you create, move or delete a folder, give it an `AGENTS.md`, then update the routing table above in the same change.

## Searching And Creating

1. Search before creating. Duplicates are a vault's main failure mode. If a relevant note already exists, update it and say which note changed.
2. One idea per note, titled so it can be linked later. Connect related notes with `[[wikilinks]]`: the other note's title in double square brackets.
3. Read a note end to end before rewriting or restructuring it, or when the answer depends on what is missing from it.
4. Put anything you will want to filter by (`tags`, `status`, a `date`) in the note's frontmatter properties.

## Working Alongside People

Teammates may be editing the same note while you work, and every edit merges live. Change only the part you mean to change; never rewrite a whole file to fix one line.

## Sources Of Truth

Never invent a number, date, name or quote. If something is not known, write `unknown` and say what would confirm it.

## Communication

Lead with the answer: the first sentence should say what happened or what you found. Keep supporting detail brief.

Use plain English and the simplest everyday words that carry the idea. If a technical term is needed, define it in a few words the first time it appears. State each fact once.

Match the length of a note to the task. Cover the substance without padding, filler sections or repeated summaries.

## Why AGENTS.md, Not CLAUDE.md

`AGENTS.md` is the instructions file that Codex, Cursor and most other AI agents read, and Claude reads it too when there is no `CLAUDE.md`, so one file covers every assistant. If you only use Claude, you can name this file `CLAUDE.md` instead. If you also use Codex, Cursor or any other agent, keep `AGENTS.md`. Do not keep both with different rules: when a `CLAUDE.md` exists, Claude reads only that file. To have both, make `CLAUDE.md` the single line `@AGENTS.md`.

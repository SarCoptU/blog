---
title: "A Simple Reading and Learning Workflow with Supernote and Obsidian"
author: SarCoptU
date: 2026-04-19
draft: false
categories: 
  - learning
  - note-taking
tags:
  - learning
  - pkm
  - supernote
  - obsidian
  - note-taking
description: "A practical workflow for actually understanding what you read — using Supernote as a thinking surface and Obsidian as a permanent knowledge layer."
---

Most note-taking systems fail for the same reason: they optimise for capture, not comprehension. Highlights accumulate. Folders fill up. Nothing gets used.

This workflow is an attempt to fix that. It uses a Supernote e-ink tablet as a thinking surface and Obsidian as a permanent knowledge layer. The goal is not to collect more information — it is to understand what you read well enough to actually use it.

---

## One Principle

**Never copy. Always restate.**

When you encounter something worth keeping, do not underline it or highlight it. Look away from the page and write — in your own words — what it said and why it matters. If you cannot do that, you did not understand it yet. That is useful information.

---

## Why Supernote and Obsidian

These two tools serve different purposes and should stay separate.

**Supernote** is where you think. The e-ink screen, the stylus, and the lack of notifications create a focused environment that is closer to paper than to a computer. Writing by hand forces compression — you cannot transcribe everything, so you are forced to decide what matters.

**Obsidian** is where the best thoughts live permanently. It is a local, plain-text knowledge base that links ideas together over time. It is not a capture tool. Things should earn their place there.

The bridge between them is Supernote's built-in OCR export, which converts handwritten notes to plain text that can be dropped into Obsidian.

---

## The Workflow

### Reading a Long Document

Dense documents — regulatory guidance, technical reports, long essays — are easy to read passively and forget completely. The instinct is to find a bigger screen and get comfortable. That optimises for consumption. This workflow optimises for understanding.

**Before you start:**

1. Load the document onto the Supernote
2. Spend the first 20–30 minutes on structure only: table of contents, chapter openings, any executive summary
3. On a fresh Supernote page, write the answer to: *"What is this document trying to help me do?"* — one short paragraph, your own words
4. List only the chapters relevant to your work. These are the pages you will actually read

You have just reduced the document to what matters.

**While reading:**

For every passage worth noting, look away and write two sentences:

- **Sentence 1:** What does this say?
- **Sentence 2:** What does this mean in practice for my work?

If you cannot write sentence 2, either you did not understand it yet, or it does not apply. Both are worth knowing before you move on.

Use three markers only:

| Marker | Meaning |
|--------|---------|
| `✓` | Already doing this |
| `!` | Gap — action needed |
| `?` | Unclear — needs follow-up |

No highlighting in the PDF. No annotations in the margins of the source. The Supernote page is your reading surface.

**At the end of each session (5 minutes):**

Flip back through your notes and write one sentence at the top of the page:

> *"The most important thing I read today was ___."*

This is the sentence that will eventually reach Obsidian. Everything else served its purpose by making you think.

---

### Listening (Podcasts and Audio)

Audio is harder to process than text because there is no natural pause point. The trick is to impose your own.

At a topic change, or when something strikes you, pause and write two sentences:

- **Sentence 1:** What was just said?
- **Sentence 2:** What does this connect to or mean for me?

At the end of the episode, write one consolidation sentence as above.

You do not need to capture everything. If you cannot recall something at a pause point, it was not important enough.

---

### Weekly Transfer to Obsidian

Once a week — not after every session:

1. Flip through the week's Supernote pages
2. Identify the consolidation sentences and all `!` markers
3. Use **Supernote's MyScript OCR (Digest)** to export those pages as plain text
4. In Obsidian, create one note per topic or domain area — not per document

**Note title format — a claim, not a topic:**

- ✅ `Incident reporting windows are tighter than most organisations assume`
- ❌ `Regulatory reading notes`

Each note contains your restated summaries and your `!` items turned into a short action list. Link to related notes where the connection is obvious.

---

## Supernote Features This Workflow Relies On

**MyScript OCR (Digest)** — converts handwritten pages to exportable plain text. The essential bridge to Obsidian. Learn how to select specific pages for export rather than exporting an entire notebook.

**Page Templates** — create a two-zone template: top area for two-sentence notes, bottom strip for the session consolidation sentence. Having the space defined on the page makes the habit easier to maintain.

**Keyword Search in Handwriting** — Supernote can search across handwritten notes. Using a consistent marker like `!` means you can search for all flagged items quickly during your weekly review.

**Notebooks by domain** — keep a separate notebook per area (e.g. `Cybersecurity`, `Clinical`, `General Reading`) rather than one large notebook. Keeps the weekly review manageable.

**WebDAV / Cloud Sync** — Supernote supports WebDAV sync. If you self-host (e.g. Nextcloud), you can point Supernote at your instance so exported files land there automatically, ready to import into Obsidian.

**Side Note (while reading PDFs)** — when reading a PDF on the Supernote itself, Side Note lets you write alongside the document without switching apps. Useful for keeping the two-sentence habit without losing your place.

---

## Obsidian Features This Workflow Relies On

**Keep the folder structure minimal:**

```
/Working      ← everything new lives here
/Notes        ← promoted, permanent notes only
```

Do not create more folders until you feel a specific friction that a folder would solve.

**Internal links `[[note title]]`** — when writing a note, link to any existing note it connects to. The vault becomes useful through connections, not through folders.

**Recommended plugins (via Community Plugins):**

| Plugin | Purpose |
|--------|---------|
| **Templater** | Consistent note shape — title, date, one-line summary, actions |
| **Periodic Notes** | Auto-creates a weekly review note as a prompt to do the transfer |
| **Dataview** | Query notes by tag or field once the vault has enough content |

**A minimal note template:**

```
---
date: <% tp.date.now("YYYY-MM-DD") %>
source:
tags:
---

## The idea in one sentence


## Why this matters


## Actions
- [ ]

## Connected notes

```

---

## What to Stop Doing

- Stop highlighting in PDFs as a primary reading habit
- Stop creating Obsidian notes directly from imported highlights
- Stop organising the existing vault — let it sit, work only in `/Working`

The existing vault is not broken. It is just full of things that were captured before the thinking happened. Leave it alone and start fresh with the new approach.

---

## Quick Reference

| Stage | Tool | Action |
|-------|------|--------|
| Before reading | Supernote | Write what the document is trying to help you do |
| While reading | Supernote | Two sentences per passage. Mark ✓ ! ? |
| End of session | Supernote | One consolidation sentence at top of page |
| Weekly | Supernote → export | OCR export of flagged pages only |
| Weekly | Obsidian | One note per topic, titled as a claim, actions from `!` markers |

---

The system works because it makes the gap visible immediately. If you cannot say what something means for your work, you know that mid-read — not after you have finished and forgotten everything. The Supernote does not make you a better note-taker. It makes the thinking impossible to skip.


### Written with AI

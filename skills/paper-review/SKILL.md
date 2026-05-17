---
name: paper-review
description: >-
  Read an academic paper (PDF) end to end, explain it intuitively to the
  reviewer, critique it honestly, and produce a submittable peer review. Use
  this whenever the user wants to review, referee, critique, assess, or
  "understand then evaluate" a paper or manuscript — conference/journal
  reviews, referee reports, meta-reviews, or requests like "explain this paper
  then write a review", "do an MLHC/NeurIPS/ICLR/CVPR review", "help me review
  submission N". Trigger even if the user only says "take a look at this paper"
  in a reviewing context, or points at a PDF plus a rubric/README.
---

# Paper Review

Turn a paper PDF into one Markdown file with three jobs: **teach the reviewer
the paper**, **critique it honestly**, **hand them text to submit**. The
reviewer has domain expertise but little time — make the paper fast to
understand and the critique easy to defend. Don't decide for them.

The governing principle: this skill is **paper-agnostic**. A method paper, a
theory paper, an empirical study, a clinical paper, a benchmark — each needs a
different presentation. Everything below states *what to achieve*; you choose
the *form* that serves the paper in front of you, and go beyond any suggestion
here when something else serves understanding better.

## Workflow

1. **Locate inputs.** Get the PDF path. Find the rubric: search the PDF's
   directory and parent dirs for a `README`/`template`/`rubric`/`instructions`
   file; if the user named a venue, follow its known structure; otherwise use
   the **Generic rubric** below and say so in the file.
2. **Read the whole paper** — appendices, tables, figures. Record exact page
   numbers for every claim, equation, table, and figure you will cite.
3. **Form your own assessment, including a provisional rating.** Do not ask the
   reviewer for an expertise lens or a score up front — proposing those is your
   job. The reviewer may later ask you to rewrite the final section with their
   preferred rating, so write it as a localized, easily-swapped edit.
4. **Write** `<pdf-stem>_review.md` next to the PDF. If it exists, ask before
   overwriting.
5. **Flag, don't bury.** Surface points that genuinely need the reviewer's
   judgment (contentious severity, rating-swing factors, suspected errors) —
   recommend, then let them steer.

## Output file

Obsidian-friendly YAML frontmatter, all keys lowercase:

```yaml
---
venue: <venue or "unknown">
submission: <id if known>
title: <paper title>
pdf: "[[<pdf filename>]]"
updated: <YYYY-MM-DD>
---
```

Then three parts.

### Part 1 — Intuitive overview (teach the paper)

Goal: a reader who has **not** read the paper grasps its core contribution
quickly. Prefer illustration, structure, and concrete examples over long prose.
Be concise.

A menu of techniques — use whichever fit this paper, in any order, and **invent
others when they would help more**:

- the core idea in a few plain-language bullets (problem → key insight →
  approach → how it's evaluated → what it shows);
- a diagram or schematic for the central idea or a key contrast — but only if it
  reads instantly; a picture that needs its own explanation is worse than a
  sentence;
- a concrete worked example that turns the paper's own abstract terms into
  something graspable;
- a structured walkthrough of the core contribution, in whatever decomposition
  fits: a method/algorithm paper often wants step-by-step stages with annotated
  math/shapes where the contribution is mathematical; a theory paper, the key
  assumption→lemma→theorem chain; an empirical/clinical paper, the design and
  what is varied;
- headline results as bullets.

Hard rules:

- Every empirical or factual claim carries its paper location:
  `[§3.2, p.7](<pdf filename>#page=7)` (relative path survives a moved vault;
  the page anchor jumps the viewer).
- Schematics carry flow and structure in **plain words** — no equations or bare
  symbols inside a diagram (they make ASCII art unreadable). Put math in its own
  Markdown math block, separate from the diagram.
- Wherever math appears, immediately gloss **every** symbol in plain language
  and say what it is for. Never leave un-explained notation anywhere.
- Link external concepts (methods, metrics, datasets) inline at first mention to
  a Wikipedia/arXiv URL — no separate link dump.
- Bold key terms and key takeaways — not everything.

### Part 2 — In-depth review (critique that teaches)

A teaching critique for someone who hasn't read the paper: re-ground every point
so it stands on its own — restate the mechanism, never just "the result on p.11
is fragile". Intuition-first, references attached, illustrations or examples
where they help.

Cover strengths and weaknesses at **equal depth**. For each point the reader
should come away knowing: **what** it is (concretely, with a page ref); **why it
matters** (the mechanism — why it changes a conclusion); **how serious** it is
(blocking vs. addressable; credit the paper if it already discloses the limit);
and, for weaknesses, **what would resolve it**. These are the questions to
answer, not a rigid template. But keep each point **scannable**: put what / why
it matters / how serious / what would resolve on their own labelled lines, and
separate consecutive points with a horizontal rule (`---`). You are free to
expand a subtle point, intersperse a small example, mini-table, or schematic,
and vary length — but not free to wall-of-text. An example or sketch beats a
dense paragraph; don't pad the simple or compress the subtle.

Order **both** strengths and weaknesses most-important-first (just order them —
no ordering caption in the output). Tag each weakness (🔴 blocking · 🟠 major ·
🟡 minor) and each strength (🟢 decisive · ⚪ supporting).
Close with **What would change my rating** (up/down conditions) and **What I
deliberately do not over-weight** — pre-empt the obvious-but-weak objection and
explain why the real problem is a *combination*.

Honesty: separate blocking from addressable; don't inflate nits; credit
self-disclosed limits; never assert a flaw you can't pin to a page.

### Part 3 — Final review (copy-paste into the form)

Plain text, no links/emoji/section-IDs, paste-ready. Cover exactly the rubric's
fields in its order. Write strengths and weaknesses as bullet lists where each
bullet opens with a few bold lead words then one concise claim+evidence
sentence — scannable, not prose paragraphs. In this section, each bullet and
paragraph is a single unwrapped line — no newlines inside it, however long
(literal mid-bullet newlines break when pasted into a form textarea). Order
strengths and weaknesses most-important-first. End with the rating: your
recommendation plus the single analysis that would move it, noting the number is
the reviewer's to set.

Parts 2 and 3 are intentionally redundant (deep vs. submittable). Sync
direction is **Part 2 → Part 3**: revise the Part 2 point first, then
regenerate the affected Part 3 bullet from it. But the reviewer edits Part 3
directly before submitting — once they have, their Part 3 wording is
authoritative: never auto-revert it to match Part 2, and fold Part 2 changes
into it only when explicitly asked. Reviewer-introduced drift is expected and
fine. Parts 1–2 are the reviewer's private working
notes (label them so: "for my own reference", "not for submission") — teaching
expansions and the Q&A belong here and may be verbose; Part 3 is the only
submitted text and stays lean.

## Reviewer follow-ups (after the first draft)

Reviewing is iterative: the reviewer reads the draft and asks you to explain
concepts they're unsure of ("educate me on X", "I don't follow Y"). These are
*clarification* requests, not critique changes. Absorb them without degrading
the artifact's structure:

- **Clause-sized → inline.** If it fits in a sentence or parenthetical, fix it
  in place where the concept first appears.
- **Needs room or spans sections → Q&A.** If it needs a table or multi-point
  reasoning, or it is load-bearing for several strengths/weaknesses, put it in
  a **Q&A — concepts I clarified** subsection at the **end of Part 2**, with a
  one-line inline pointer (`→ Q&A Qn`) where the concept is referenced. The
  Q&A accumulates across follow-up turns; say it is not for submission.
- **Part 3 stays last and clean.** The Q&A lives in Part 2 (the non-submitted
  reasoning) — never below the paste-ready text. Pointers are plain text, not
  Markdown `#`-anchors (an Obsidian vault will not resolve them).
- **Critiques stay critiques.** Only *concept clarifications* migrate to the
  Q&A. A point the reviewer questions about a weakness stays in that weakness;
  never wedge a multi-point concept primer into the severity-ordered list — it
  breaks the scannability Part 2 depends on.

## Generic rubric (fallback when none is found)

1. **Brief summary** — 2–5 sentences.
2. **Scientific soundness** — strengths/weaknesses; are claims substantiated;
   is it reproducible / are proofs correct.
3. **Scientific impact** — novelty; potential to influence the field.
4. **Relevance** — does it address a real problem; fit for the venue/theme.
5. **Rating** — 1 reject · 2 weak reject · 3 weak accept · 4 accept · 5 strong
   accept (note if the venue scale differs).

## Style

Concise over complete — every sentence must change what the reviewer knows or
does. In every part, including the critique, an example or sketch beats a dense
paragraph. Never put equations inside a diagram; wherever math appears, gloss
every symbol in plain words. Never include a diagram that needs its own
explanation. Bold key terms and takeaways, not everything. Paper-agnostic: adapt
structure and vocabulary (proofs, ablations, cohorts, benchmarks) to the paper
at hand.

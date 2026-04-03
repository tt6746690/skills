---
name: plan-iteration
description: >-
  Iterate on complex, multi-file implementation plans through investigation,
  drafting, review, and refinement cycles. Use this skill when the user wants to
  create or improve a plan for a multi-step project, review an existing plan for
  gaps, conduct an investigation to inform a plan, decompose a large task into
  chunks with sub-plans, or do a systematic review/feedback cycle on plan
  documents. Also use when the user seems stuck on what to do next with a plan,
  or when they want to validate assumptions before building. Trigger whenever
  the user mentions "plan", "sub-plan", "review the plan", "what are we
  missing", "iterate on this", "what should we investigate", or wants to go
  from vague goals to a concrete implementation roadmap.
---

# Plan Iteration

A structured approach to building and refining implementation plans for complex
projects. Plans evolve through cycles of investigation, drafting, review, and
refinement — not in one shot.

**Announce at start:** "I'm using the plan-iteration skill to help structure
this planning work."

## Core Principles

1. **Plans improve through contact with reality.** Investigation turns
   assumptions into facts. Don't plan what you haven't probed.
2. **External perspective catches blind spots.** Systematic review finds gaps
   that the author can't see.
3. **Simpler is better when evidence supports it.** Let investigation kill
   scope. Remove planned complexity that data shows is unnecessary.
4. **Plans are living documents, not contracts.** Structure, naming, and scope
   should evolve as understanding deepens.

---

## Plan Structure

### Master Plan + Sub-Plans

Complex projects split into a **master plan** and **sub-plans**:

- **Master plan**: Architecture, design decisions, chunk decomposition, shared
  vocabulary. Stays high-level and readable.
- **Sub-plans** (one per chunk): Detailed tasks, interface definitions, code
  sketches, test plans. This is where implementation specifics live.

This separation keeps the master plan scannable while sub-plans go deep.
Simple projects may only need a single plan document.

### Essential Sections

A good master plan includes:

| Section | Purpose |
|---------|---------|
| **Goal** | What we're building and why, in 2-3 sentences |
| **What We Already Know** | Investigation findings, data quirks, constraints |
| **Design Decisions** (D1, D2...) | Numbered, with DECIDED / NEEDS DISCUSSION prefix |
| **Open Questions** (Q1, Q2...) | With options and recommendations |
| **MVP Assumptions** | Table: assumption, impact, relaxation path |
| **Chunk Decomposition** | Ordered list with dependencies |
| **Shared Vocabulary** | Canonical names used across sub-plans |

### Sub-Plan Essentials

Each sub-plan includes:

- **Version** with sync status: `v2 (synced with PLAN.md v8)`
- **Tasks** with clear acceptance criteria
- **Interface sketches** for non-trivial boundaries
- **Expected outputs** (files, artifacts)
- **Dependencies** on other chunks

### Design Decisions

Major choices get numbered sections with options considered, rationale, and a
status label (**DECIDED** or **NEEDS DISCUSSION**). Resolve all NEEDS
DISCUSSION items before implementation starts.

### MVP Assumptions Table

Every plan for a complex system should explicitly list its simplifications:

| Assumption | Impact | Relaxation path |
|------------|--------|-----------------|
| *what we're simplifying* | *what this prevents* | *how to remove later* |

Without this, simplifications hide in prose and get debated repeatedly.

---

## Iteration Modes

Plans improve through repeated cycles, but the cycles aren't strictly
sequential — think of them as **modes** you enter based on what the plan
needs most right now.

### Mode: Investigate

**Enter when:** The plan makes assumptions you haven't verified, references
APIs or data you haven't seen, or an open question can be answered empirically.

1. **Identify the unknown** — be specific about what you're testing
2. **Probe it** — write a small script, call the API, fetch sample data, read
   the docs, whatever gets you facts
3. **Capture findings** — write a findings document with concrete examples,
   edge cases, and implications for the plan
4. **Feed back** — update the plan: resolve questions, revise decisions,
   update assumption tables (change impact/relaxation if evidence shifted),
   fix sketches, bump the version

**Anti-pattern:** Detailed plans for systems you haven't touched yet.

### Mode: Review

**Enter when:** A draft exists and hasn't been reviewed, or significant
changes have accumulated since the last review.

Read the full plan with fresh eyes and produce a numbered list of observations
(A, B, C...). For each item include: the problem, why it matters, concrete
options with tradeoffs, and a recommendation. Aim for enough depth that the
user can make a decision without follow-up — but don't pad with filler. The
user may ask to expand or compress, so be ready to adjust.

Review questions to ask:
- What's missing or underspecified?
- Are there inconsistencies between plans?
- Are there well-known patterns or tools we're reinventing?
- Would someone implementing this need to ask clarifying questions?
- Are interfaces consistent across plan boundaries?

### Mode: Integrate Feedback

**Enter when:** The user has responded to a review or has corrections.

1. Group related items
2. Apply changes to the most specific file (sub-plan over master plan)
3. Cross-check consistency — grep all plans when changing any name or interface
4. Commit after each batch of related changes

**Batch execution:** When the user approves multiple items at once ("do
everything"), separate them into investigations (need data) vs plan edits
(can apply now). Kick off investigations in the background, apply plan
edits in parallel while waiting. This avoids blocking on slow data fetches.

### Mode: Refine

**Enter when:** The plan is broadly correct but specific areas need work.

Examples of targeted refinement:
- **Dependency research** — survey existing tools/libraries before building
- **Data audit** — run sample data through the planned pipeline
- **Simplification** — remove complexity that evidence shows is unnecessary
- **Structural revision** — revisit module boundaries, file organization, naming

---

## Generating Investigation Ideas

When the user isn't sure what to investigate next — or to proactively suggest
productive directions — apply these lenses to the current plan:

### Assumption Stress-Test

List every assumption the plan makes (explicit and implicit). Rate each by
confidence (high/medium/low). The lowest-confidence assumptions are the
highest-value investigations.

Example prompts to generate:
- "What would break if the API response shape differs from what we expect?"
- "Are we assuming data availability that we haven't verified?"
- "What implicit ordering or timing assumptions exist?"

### Failure Mode Analysis

Walk through ways the plan could fail at runtime:
- What happens when external services are slow, down, or return errors?
- Where could data be missing, malformed, or stale?
- What are the edge cases at boundaries (first item, last item, empty set)?
- Where could state get out of sync between components?

### Integration Seam Audit

For each boundary between chunks or components:
- What's the exact interface? (types, method signatures, data format)
- Who produces the data and who consumes it?
- What happens if the producer's output doesn't match what the consumer expects?
- Is there a test or contract that would catch a mismatch?

### End-to-End Walkthrough

Mentally execute the plan from start to finish:
- "If I started implementing chunk 1 right now, what's the first thing I'd be
  unsure about?"
- "Trace a single unit of work through the entire pipeline — where does it
  get stuck?"
- "What's the first thing the user would try after the MVP is built? Does the
  plan support that?"

### Prior Art Search

Before building infrastructure:
- Are there established libraries or frameworks for this?
- Has this problem been solved in adjacent domains?
- What design patterns do similar systems use?

Record findings even when the answer is "nothing fits" — that's useful
context for why we're building it ourselves.

### Data Model and Interface Design

Examine the plan's data models, interfaces, and class hierarchies:
- Are the right things modeled as data vs. behavior?
- Are there models that mix concerns (config + results, input + derived)?
- Could a flatter structure work, or does the hierarchy earn its complexity?
- Are interfaces minimal — do consumers need everything the producer exposes?
- Are there implicit data transformations that should be explicit types?
- Look at how similar systems in the domain model these concepts — is there a
  more natural decomposition we're missing?

This lens is especially valuable early, because data model mistakes are
expensive to fix once code depends on them.

### Using These Lenses

Pick 2-3 lenses that feel most relevant to the plan's current state. Generate
3-5 specific investigation ideas from each. Present them to the user ranked by
expected impact — which investigations would most change the plan if the
answer surprised us?

Don't investigate everything. The goal is to find the highest-leverage unknowns
and resolve them before they become expensive surprises during implementation.

---

## When to Stop Planning and Start Building

Planning has diminishing returns. Signs you're ready to implement:

- All NEEDS DISCUSSION items are resolved
- Low-confidence assumptions have been investigated
- You can describe the first chunk's implementation without hand-waving
- The remaining unknowns are things you'll only learn by building

Signs you're over-planning:
- Code sketches are becoming actual implementations
- You're specifying error handling for hypothetical scenarios
- The plan keeps growing but the goal hasn't changed
- You're debating decisions that would take 30 minutes to just try

**The litmus test:** Could someone pick up the plan and start implementing
without needing to ask you clarifying questions about the first chunk? If yes,
start building. Later chunks can be refined in parallel with implementation.

---

## Review Checklist

Use this for a proactive review pass:

- [ ] All design decisions marked DECIDED or NEEDS DISCUSSION
- [ ] Open questions have options and recommendations
- [ ] MVP assumptions table is complete and current
- [ ] Shared vocabulary matches interface sketches
- [ ] Sub-plan versions are synced with master plan
- [ ] Interface signatures are consistent across all plans
- [ ] No implicit assumptions that should be explicit
- [ ] Investigation findings are reflected in the plan
- [ ] Chunk dependencies are correct and acyclic
- [ ] Each chunk has clear acceptance criteria

---

## Consistency Maintenance

Plans with multiple files drift out of sync. Prevent this:

- **Shared vocabulary** in the master plan — canonical names for types and
  interfaces. Update the vocabulary and grep for stale references when anything
  changes.
- **Version tracking** — every sub-plan records which master plan version it's
  synced with. After updating the master, check which sub-plans need updating.
- **Cross-plan updates** — when changing a name or interface in one plan,
  search all others. Do a reflection pass after batches of updates.

---

## Scope Management

### Scope Creep Detection

If a chunk's task list grows beyond its original scope, file a new chunk.
Signs:
- "While I'm here, I should also..."
- Task count doubled since the plan was written
- Dependencies on things not in the plan

### Provisional Decisions

Early structural decisions (file layout, module boundaries, naming) will
change as understanding deepens. Mark them as provisional rather than defending
them when evidence shifts. When revisiting, update all references in one pass.

---

## Naming Precision

Names should describe the **method** (how), not just the **output** (what).
This matters when multiple approaches could produce the same result.

**Litmus test:** If you rename a function to a synonym and the meaning changes,
the original name was ambiguous.

Use domain-agnostic names in reusable modules. Domain-specific naming belongs
in domain-specific wrappers.

---

## Practical Tips

- **Commit periodically** — after each logical unit of plan work, not in
  large batches.
- **Plans are not code** — sketches show intent and interface shape, not final
  implementation.
- **Let investigation kill scope** — if data shows a feature is unnecessary,
  remove it.
- **Version bumps are cheap** — bump on every meaningful edit.
- **Research before building** — survey existing tools before writing
  infrastructure. Skip for trivial logic.
- **Two-step decomposition** — when building shared infrastructure, first
  extend the reusable layer with a general interface, then build the
  project-specific wrapper on top.

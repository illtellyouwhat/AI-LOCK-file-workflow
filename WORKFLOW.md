# Lock File Workflow: Complete Technical Narrative

**Project:** Client Website Redesign & Migration  
**Challenge:** Migrate React/Vite to Astro + redesign for SEO — without JavaScript knowledge  
**Outcome:** 99% implementation accuracy, zero breaking changes, full client handoff including the methodology

---

## Table of Contents

1. [The Situation](#1-the-situation)
2. [First Principle: Establish Ground Truth Before Touching Anything](#2-first-principle-establish-ground-truth-before-touching-anything)
3. [Initial Implementation: Where Things Broke](#3-initial-implementation-where-things-broke)
4. [System Evolution: From Style Guide to Lock Files](#4-system-evolution-from-style-guide-to-lock-files)
5. [The Refinement Day: Building the Feedback Loop](#5-the-refinement-day-building-the-feedback-loop)
6. [The Workflow When It Finally Clicked](#6-the-workflow-when-it-finally-clicked)
7. [Mid-Project Discovery: Adding Features Without Breaking Things](#7-mid-project-discovery-adding-features-without-breaking-things)
8. [Client Handoff: Delivering the Methodology, Not Just the Site](#8-client-handoff-delivering-the-methodology-not-just-the-site)
9. [Key Insights](#9-key-insights)
10. [Reusable Prompts](#10-reusable-prompts)

---

## 1. The Situation

### What Was Asked

I had been working with a client on various freelance projects. As those wrapped, the principal mentioned a site he'd prototyped in Lovable.dev. It was visually polished, but generic. Compared to competitors, it read like a brochure rather than a credibility document.

The original ask: add a blog section.

What I saw: a deeper need. The site lacked case studies, had no specificity about what the company actually does, and wasn't structured for how AI-powered answer engines were reshaping search discovery.

The pitch: full redesign, framework migration to Astro (better for content management), and optimization for answer engine visibility.

Client response: "Yeah, go for it."

### The Constraint

**What I knew:**
- Python, data engineering
- Design (Parsons School of Design background)
- 25 years carpentry and fabrication — systems thinking, constraints, the cost of drift

**What I didn't know:**
- JavaScript. The existing site was React + Tailwind + Vite.

**The decision:** Don't learn JavaScript. Instead, figure out how to direct an AI agent to produce JavaScript that matched my design intent — reliably, across many iterations, without breaking what was already working.

This decision is the origin of the Lock File Workflow.

---

## 2. First Principle: Establish Ground Truth Before Touching Anything

From 25 years of physical fabrication I learned that  the foundation determines everything built on top. Get it crooked, and every layer that follows is flawed.

Before making any changes to the site, I needed to understand exactly what existed and what it should become.

### Visual Analysis

1. Took complete screenshots of the existing site
2. Imported to Obsidian
3. Used Excalidraw to annotate directly on screenshots — red marks where design was off, notes on what needed to change
4. Kept annotations clear and honest, not optimistic

This step was for me, not the AI. It forced me to surface my own designer intuition before asking for external analysis.

### Model Analysis

With my own assessment in hand, I asked the AI for a comprehensive analysis. The prompt mattered:

>**Deliberate Over-Instruction Template**

```
[YOUR ANALYSIS REQUEST]

Requirements for your response:
- Do NOT summarize or compress. I need exhaustive depth, not executive summary.
- For each point, expand with: implementation details, edge cases, failure modes, historical context, counterarguments
- When you identify a risk or tradeoff, explain the second-order consequences
- If you mention a best practice, explain when it doesn't apply
- Prioritize completeness over brevity - I will do my own compression later

Minimum length: [SPECIFY - e.g., "1500 words" or "5 detailed paragraphs per section"]

```


What came back: a 2,000+ word analysis with specific, prioritized recommendations and links backing each claim.

### Validation Step

Critical: I compared the model's analysis to my own annotated screenshots before proceeding.

Where they aligned — high confidence to proceed.  
Where they diverged — decide which perspective was right, or investigate further.  
Where the model caught something I missed — update my understanding.

**Why this mattered:** If the alignment had been low, it would have signaled that either I was misreading the site or the model was. Either way, proceeding would have meant building on a contested foundation. The alignment was strong. I proceeded with confidence.

---

## 3. Initial Implementation: Where Things Broke

### Framework Migration

First task: port React/Vite to Astro.

Astro was the right call for content-heavy sites:  markdown-based, better for blog and case study management, easier for a non-JavaScript client to update later.

The migration itself went cleanly. I broke it into small, well-defined prompts, ran them sequentially, and verified at each step.

### Visual Redesign: The Drift Problem

Then I started the visual redesign. This is where the system failed.

The AI did roughly what I asked — but it also:
- Changed fonts I hadn't mentioned
- Adjusted colors I hadn't touched
- Moved elements I hadn't referenced
- Rewrote copy with a different tone

**Example:**  
- Me: "Move the testimonials section below the hero"  
- AI: Moved testimonials ✅ | Changed card styling ❌ | Adjusted spacing site-wide ❌ | Updated color scheme "to match" ❌

This is not a bug in the AI. It's a feature: when faced with ambiguity, the model fills in gaps with its own decisions. The problem was that I hadn't made my constraints explicit. I was working in natural language, and natural language has gaps.

Every gap is an invitation for the AI to improvise.

---

## 4. System Evolution: From Style Guide to Lock Files

### Iteration 1: The Style Guide

First attempt at constraint management: a single `style_guide.md` file with fonts, colors, spacing values, button styles.

Result: reduced some drift, but things still changed unexpectedly.

The problem: one file covering everything meant that when I updated one section, the model sometimes interpreted adjacent sections as open for reconsideration too.

### Iteration 2: Domain Siloing

After a conversation with the model about why drift was still happening, the insight emerged:

**Different constraint domains need separate files.**

If I'm updating copy, the model shouldn't reconsider the framework. If I'm specifying a layout change, the model shouldn't decide to revise the color scheme.

The file structure that emerged:

```
LOCK-design.md       → Colors, fonts, spacing, component styles
LOCK-content.md      → All copy, messaging, CTAs, microcopy
LOCK-architecture.md → Tech stack, file structure, routing, frameworks
```

Siloing worked. Changes in one domain stopped bleeding into others.

### Iteration 3: Separating Planning from Execution

I already knew from experience that planning and execution have incompatible cognitive modes.

- **Planning mode:** Creative, exploratory, considers options, comfortable with ambiguity
- **Execution mode:** Literal, precise, follows instructions, no improvisation

If the executor starts planning, it drifts into strategic mode and makes undocumented decisions. If the advisor tries to implement, it loses the objectivity needed to evaluate trade-offs.

**The solution:**

**Advisor chat (Claude.ai):**
- Reads lock files
- Makes decisions about what should change
- Generates spec files
- Updates lock files when strategy shifts
- Never touches code

**Executor agent (Claude Code or IDE agent):**
- Reads lock files as immutable truth
- Follows spec files exactly
- Generates changelogs and inventory
- Never makes design decisions
- Never edits lock files

Communication between them: only through files the human passes manually.

### Iteration 4: The Prompt Header

Problem: every time I opened a new executor session, I had to re-explain the workflow.

Solution: `PROMPT-claude-code-header.md` — a brief instruction file loaded at the start of every executor session.

Contents: the workflow rules, file hierarchy, reading order, and the single most important instruction: *read all lock files before touching anything.*

One file, loaded once per session, primes the executor consistently.

This is essentially what the claude.md files do in repo directories I just came to a similar conclusion and applied it slightly different.

---

## 5. The Refinement Day: Building the Feedback Loop

After several successful iterations, new problems emerged:

- Files accumulating from each phase sometimes multiple copies or revisions
- Hard to track what changed when
- Advisor chat losing context of actual current state

**Decision:** Take half a day off implementation to fix the system itself.

### File Naming Convention

Files split into two groups based on who owns them.

**Advisor-owned files** — created and maintained by the planning agent:
```
LOCK-[domain].md       — Immutable constraints per domain
SPEC-[phase-name].md   — Implementation instructions for one phase
HISTORY-executive-timeline.md — Append-only project log; stays with the Advisor
```

**Executor-owned files** — generated by the implementation agent:
```
INVENTORY-[type]-[date].md      — Current state snapshot; Advisor reads, never edits
CHANGELOG-[date]-[gitN].md      — Change record keyed to commit hash
```

**Files created by the Advisor that live with the Executor:**
```
PROMPT-[tool]-header.md              — Session primer; Advisor writes it once, Executor loads it every session
PROMPT-generate-[type]-inventory.md  — Instructions for state capture
PROMPT-changelog.md                  — Instructions for generating the change record
00-START-HERE.md                     — Project context and reading order
```

Every file's purpose is legible from its name alone.

### The Changelog

The changelog wasn't created because debugging required reading the entire codebase. It was created to add a second layer of tracking on top of git; git diffs can be useful.  However .md files are more efficient for the model and can conserve tokens so I added that as a layer. Having a plain-language forensic record meant that if one method of finding a problem wasn't working, there was another. In practice it was never needed for debugging. Though it exists as a reliable fallback, and that's enough reason to keep it.

After each implementation phase, pass `PROMPT-changelog.md` to the executor. It generates:

- Git commit hash
- Every file modified, with line numbers
- What changed in each file
- Why (reference to the spec)

### The Inventory

Problem: the Advisor chat was losing context of what had actually been built. It was planning next steps based on what it thought needed to be done rather than what already existed.

Solution: after each phase, the executor generates `INVENTORY-[date].md`:  A present-tense description of the current state of the project. sort of like a DOM in web dev.

The Advisor reads the inventory before planning any next phase. It reads what was actually built, not what it remembered building. This also helps track nay drift that may have happened

**The critical role separation this enforces:**

- Lock files = desired state (Advisor edits when strategy changes)
- Inventory = actual state (Executor generates, Advisor reads only)

The Advisor never edits the inventory. The Executor never edits the lock files. Neither can corrupt the other's domain.

---

## 6. The Workflow When It Finally Clicked

Once the naming convention, changelog, and inventory were in place, the daily rhythm became:

1. Open current site in browser
2. Brain dump: what needs to change, in order of appearance on page
3. Advisor reads inventory → updates lock files if any decisions changed → generates spec
4. Human reviews spec (this is the quality gate — catch ambiguities or contradictions before they become broken code)
5. Copy to executor workspace: prompt header + lock files + spec
6. Executor implements
7. Verify in browser
8. Commit to git
9. Run changelog prompt → forensic record keyed to commit
10. Run inventory prompt → updated state snapshot
11. Pass inventory back to Advisor
12. Iterate

---

## 7. Mid-Project Discovery: Adding Features Without Breaking Things

Midway through the redesign, an opportunity emerged: the case study section could support dynamic filtering. If a visitor clicked a service category, they'd see only relevant case studies. Same for industry tags.

In a traditional workflow, adding a feature mid-stream risks destabilizing what's already built.

With the lock file system:

1. Updated `LOCK-architecture.md` with the routing and filtering logic
2. Created a new spec for the filtering feature
3. Executor implemented against locked constraints — existing work unchanged
4. Feature worked on the first implementation

The system was tight enough that mid-stream additions didn't break anything. The locks protected what was already built while the spec scoped exactly what was new.

---

## 8. Client Handoff: Delivering the Methodology, Not Just the Site

The client received three deliverables beyond the website itself.

### Human/LLM-Readable Table of Contents

A navigation document for the codebase:

```markdown
# Where Things Are

Finding authentication logic → src/components/Auth/
Updating navigation → src/components/Navigation.astro
Adding a blog post → content/blog/[slug].md
Changing colors → LOCK-design.md, then create a spec
```

Both a human reading it and an AI being asked "where do I change X?" can navigate the codebase without wasting brain power or tokens.

### Answer Engine Optimization Workshop

A structured prompt that leads the client through a series of questions about their business:  The specific questions prospects ask, industry terminology, common misconceptions, and outputs specific content recommendations tailored to their actual services.

The AI analysis built the framework. The client's answers populate it with real content.

### The Skill File

Most importantly: the client received the Lock File Workflow system itself: `SKILL.md`, the complete methodology.

The client is a developer. Handing them the system rather than just the deliverable means they can continue iterating without depending on me. They understand not just what was built but how to keep building it.


---

## 9. Key Insights

**Ambiguity is an invitation.** When faced with an underspecified prompt, an AI model fills gaps with its own decisions. This is a feature when you want creative help. It's a liability when you need consistency. Lock files replace ambiguity with explicit constraints.

**Context engineering outlasts prompt engineering.** A well-crafted prompt works for one interaction. A well-maintained lock file works across every interaction in a project's lifetime. The leverage is an order of magnitude higher.

**Domain silos prevents cross-contamination.** When constraints live in a single file, a change to one domain can trigger reconsideration of adjacent domains. It also becomes harder to manage.   Separate files make domain boundaries explicit and enforce them structurally.

**Role separation prevents drift.** Planning and execution require incompatible cognitive modes. An executor that starts planning drifts. An advisor that starts implementing loses objectivity and scope. The system works because neither actor can cross into the other's domain.

**Feedback loops create trust.** The changelog and inventory system means actual state is always known by the Advisor, Executor, and human. The changelog record exists as insurance.

**Good systems make unreliable tools reliable.** The core insight: I didn't become a JavaScript expert. I built a system that extracted reliable JavaScript from an AI that could have gone anywhere.

---

## 10. Reusable Prompts

These prompts are generic versions of what the lock-file skill will create for you specifically for your project: Website inventory looks different than Mobile App inventory.  You can use these prompts outside the system for your own use but its a good idea to iterate over them to make them more useful to your own context. 

### Comprehensive Analysis Prompt

```
[YOUR ANALYSIS REQUEST]

Requirements for your response:
- Do NOT summarize or compress. I need exhaustive depth, not executive summary.
- For each point, expand with: implementation details, edge cases, failure modes, historical context, counterarguments
- When you identify a risk or tradeoff, explain the second-order consequences
- If you mention a best practice, explain when it doesn't apply
- Prioritize completeness over brevity - I will do my own compression later

Minimum length: [SPECIFY - e.g., "1500 words" or "5 detailed paragraphs per section"]
```

### Changelog Generation Prompt

```
# Task: Generate a Changelog Entry

You are an implementation executor. Generate a forensic changelog documenting 
what changed, where, and why — with enough detail to reconstruct decisions 
without reading the code.

**Before starting, confirm you have:**
- The diff or list of changed files
- The spec file(s) that directed this work
- The relevant lock files
- Today's actual date and the git commit hash

**Save as:** `CHANGELOG-[phase-name]-[YYYY-MM-DD].md` in `/docs/`

---

## Format

**Date**: [today's actual date]
**Commit**: [git hash]
**Spec**: [spec filename]

### Summary
2–3 sentences: what was the goal, what is now true that wasn't before.

### Changes by File
For each file: path, lines modified, what changed, and which spec line or lock 
file required it. Paste before/after for non-obvious changes.

### Deviations
Anything done outside spec scope, or decisions made without explicit instruction. 
If none, write "No deviations."

---

**Rules:** Use today's actual date. Tie every change to a source (spec or lock). 
Flag gaps rather than filling them with assumptions. 
```

### Inventory Generation Prompt

```
# Task: Generate a State Inventory

You are an implementation executor. Generate a snapshot of the current project 
state accurate enough for an advisor to plan the next phase without reading 
the codebase directly.

**Before starting, confirm you have access to:**
- The full project file tree
- Today's actual date

**Save as:** `INVENTORY-[type]-[YYYY-MM-DD].md` in project root

---

## Format

**Date**: [today's actual date]
**Type**: [e.g. schema, content, components, pins]

### Structure
List all meaningful entities in the project (files, components, tables, routes, 
pins — whatever is domain-relevant). For each: name, location, current state, 
and any placeholders or incomplete sections.

### Gaps and Placeholders
Anything marked TODO, placeholder, stub, or otherwise unfinished. Be explicit — 
do not omit or normalize incomplete work.

---

**Rules:** Use today's actual date in both the header and filename. Extract exact 
names and paths — do not paraphrase. Mark empty sections as [Not implemented] 
rather than skipping them. This is a snapshot, not a summary.
```

---

## File Structure Reference

```
/project-root/
├── 00-START-HERE.md                    # Advisor creates, Executor reads
├── LOCK-design.md                      # Advisor owns
├── LOCK-content.md                     # Advisor owns
├── LOCK-architecture.md                # Advisor owns
├── SPEC-[phase-name].md                # Advisor creates per phase
├── HISTORY-executive-timeline.md       # Advisor append-only log
├── PROMPT-[tool]-header.md             # Advisor writes, Executor loads each session
├── PROMPT-generate-inventory.md        # Advisor writes, Executor runs
├── PROMPT-changelog.md                 # Advisor writes, Executor runs
├── INVENTORY-[type]-[date].md          # Executor generates, Advisor reads
└── CHANGELOG-[date]-[gitN].md          # Executor generates
```

---

*This workflow is formalized in [SKILL.md](SKILL.md) and can be applied to any domain requiring AI assistance outside core expertise.*

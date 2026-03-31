# Lock File Workflow: Complete Technical Narrative

**Project:** Automation Architect Website Redesign & Migration  
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

I had been working with Automation Architect on various freelance projects. As those wrapped, the principal mentioned a site he'd prototyped in Lovable.dev — visually polished, but generic. Compared to competitors, it read like a brochure rather than a credibility document.

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

From 25 years of physical fabrication: the foundation determines everything built on top. Get it crooked, and every layer that follows is crooked too.

Before making any changes to the site, I needed to understand exactly what existed and what it should become.

### Visual Analysis

1. Took complete screenshots of the existing site
2. Imported to Obsidian
3. Used Excalidraw to annotate directly on screenshots — red marks where design was off, notes on what needed to change
4. Kept annotations clear and honest, not optimistic

This step was for me, not the AI. It forced me to surface my own designer intuition before asking for external analysis.

### Model Analysis

With my own assessment in hand, I asked the AI for a comprehensive analysis. The prompt mattered:

> "Analyze this website comprehensively. Provide detailed assessment based on current business best practices, research-backed recommendations, and how answer engines are affecting site structure and discoverability. Explain all trade-offs. Cite sources."

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

Astro was the right call for content-heavy sites — markdown-based, better for blog and case study management, easier for a non-JavaScript client to update later.

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

---

## 5. The Refinement Day: Building the Feedback Loop

After several successful iterations, new problems emerged:

- Files accumulating with unclear purposes
- Hard to track what changed when
- Advisor chat losing context of actual current state
- Manual archaeology needed to remember what had been implemented last session

**Decision:** Take half a day off implementation to fix the system itself.

### File Naming Convention

```
LOCK-[domain].md                    # Immutable constraints
SPEC-[phase-name].md                # Implementation instructions
PROMPT-[tool]-header.md             # Executor session primer
PROMPT-generate-[type]-inventory.md # Instructions for state capture
HISTORY-executive-timeline.md       # Append-only project log
INVENTORY-[type]-[date].md          # Current state snapshot
CHANGELOG-[date]-[gitN].md         # Change record keyed to commit hash
```

Every file's purpose is legible from its name alone.

### The Changelog

Problem: without a forensic record, debugging required reading the entire codebase.

Solution: After each implementation phase, pass `PROMPT-changelog.md` to the executor. It generates:

- Git commit hash
- Every file modified, with line numbers
- What changed in each file
- Why (reference to the spec)

Keying to line numbers meant that when something broke later, I could go directly to the change rather than searching.

### The Inventory

Problem: the Advisor chat was losing context of what had actually been built. It was planning next steps based on what *should* exist rather than what *did* exist.

Solution: After each phase, the executor generates `INVENTORY-[date].md` — a present-tense description of the current state of the project. Not a changelog. A snapshot.

The Advisor reads the inventory before planning any next phase. It reads what was actually built, not what it remembered building.

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
4. Human reviews spec (this is the quality gate — catch ambiguities before they become broken code)
5. Copy to executor workspace: prompt header + lock files + spec
6. Executor implements
7. Verify in browser
8. Commit to git
9. Run changelog prompt → forensic record keyed to commit
10. Run inventory prompt → updated state snapshot
11. Pass inventory back to Advisor
12. Iterate

**Time per iteration:** 30–60 minutes  
**Accuracy:** 99%  
**Cognitive load:** Low — the system handled state management

The transformation: from frustrating whack-a-mole to systematic, pleasurable work.

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

Both a human reading it and an AI being asked "where do I change X?" can navigate the codebase without archaeology.

### Answer Engine Optimization Workshop

A structured prompt that leads the client through a series of questions about their business — the specific questions prospects ask, industry terminology, common misconceptions — and outputs specific content recommendations tailored to their actual services.

The AI analysis built the framework. The client's answers populate it with real content.

### The Skill File

Most importantly: the client received the Lock File Workflow system itself — `SKILL.md`, the complete methodology.

The client is a developer. Handing them the system rather than just the deliverable means they can continue iterating without depending on me. They understand not just what was built but how to keep building it.

That's empowerment, not just delivery.

---

## 9. Key Insights

**Ambiguity is an invitation.** When faced with an underspecified prompt, an AI model fills gaps with its own decisions. This is a feature when you want creative help. It's a liability when you need consistency. Lock files replace ambiguity with explicit constraints.

**Context engineering outlasts prompt engineering.** A well-crafted prompt works for one interaction. A well-maintained lock file works across every interaction in a project's lifetime. The leverage is an order of magnitude higher.

**Domain siloing prevents cross-contamination.** When constraints live in a single file, a change to one domain can trigger reconsideration of adjacent domains. Separate files make domain boundaries explicit and enforce them structurally.

**Role separation prevents drift.** Planning and execution require incompatible cognitive modes. An executor that starts planning drifts. An advisor that starts implementing loses objectivity. The system works because neither actor can cross into the other's domain.

**Feedback loops create trust.** The changelog and inventory system means actual state is always known — by the Advisor, by the human, and in theory by anyone who needs to audit the project. The forensic record existed as insurance, not because debugging required it.

**Good systems make unreliable tools reliable.** The core insight: I didn't become a JavaScript expert. I built a system that extracted reliable JavaScript from an AI that could have gone anywhere. That's not a coding skill — it's an engineering disposition. From carpentry: the quality of the jig determines the quality of the joint, not the quality of the hand.

---

## 10. Reusable Prompts

### Comprehensive Analysis Prompt

```
Provide a comprehensive analysis of [subject].

Requirements:
- Explain all trade-offs
- Reference current best practices
- Cite research and studies where applicable
- Consider [specific context — e.g., how answer engines are reshaping discovery]
- Organize by priority
- Include specific, actionable recommendations
```

### Changelog Generation Prompt

```
Generate a detailed changelog for the changes just made.

Required format:
- Git commit hash: [hash]
- For each file modified:
  - Filename and path
  - Line numbers changed
  - What changed
  - Why it changed (reference the spec)

Be specific and thorough. Every change should be traceable.
```

### Inventory Generation Prompt

```
Generate a current state inventory of [project].

Include:
- [Domain-specific structure — e.g., for web: page structure, component organization, navigation, key features, routing logic]
- Placeholder content marked explicitly as [PLACEHOLDER]
- Recent changes incorporated

Format for readability by both human and AI. Save as INVENTORY-[type]-[TODAY'S DATE].md.
```

---

## File Structure Reference

```
/project-root/
├── 00-START-HERE.md
├── LOCK-design.md
├── LOCK-content.md
├── LOCK-architecture.md
├── SPEC-[phase-name].md
├── PROMPT-claude-code-header.md
├── PROMPT-generate-inventory.md
├── PROMPT-changelog.md
├── HISTORY-executive-timeline.md
├── INVENTORY-[type]-[date].md
└── CHANGELOG-[date]-[gitN].md
```

---

*This workflow is formalized in [SKILL.md](SKILL.md) and can be applied to any domain requiring AI assistance outside core expertise.*

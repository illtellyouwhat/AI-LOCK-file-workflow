# Quick Start: AI Lock File Workflow

**From zero to first reliable iteration in five steps.**

---

## Prerequisites

- Two AI tools: one for planning (Claude.ai or similar chat interface), one for execution (Claude Code, Cursor, or similar IDE agent)
- A project with constraints you need to preserve across iterations
- Git (recommended — the changelog system anchors to commit hashes)

---

## Step 1: Set Up Your Advisor Chat

Open your planning AI (Claude.ai recommended). Drop in [SKILL.md](SKILL.md) as a project file or paste it as context. This primes the Advisor to understand its role.

Tell it:

> "I'm starting a new project using the Lock File Workflow. Here's what I'm building: [describe your project, your constraints, and what expertise gap you're working around]."

The Advisor will ask clarifying questions and generate your initial file structure.

---

## Step 2: Create Your Lock Files

Work with the Advisor to define your constraint domains. A web project typically needs three:

```
LOCK-design.md       — Colors, fonts, spacing, component styles
LOCK-content.md      — All copy, messaging, CTAs
LOCK-architecture.md — Tech stack, file structure, frameworks
```

A data pipeline might need:
```
LOCK-schema.md           — Table structures, field types, relationships
LOCK-transformations.md  — Processing logic, validation rules
LOCK-architecture.md     — Infrastructure, dependencies, deployment
```

**The rule:** Every decision that must survive across iterations goes in a lock file. If you'd be upset if the executor changed it, lock it.

---

## Step 3: Create Your Executor Header

Ask the Advisor to generate `PROMPT-[tool]-header.md` — a brief instruction file that tells the executor how to behave at the start of every session.

This file does one job: prime the executor to read lock files before touching anything.

It should take 30 seconds to load. If it's longer than one page, it's too long.

---

## Step 4: Write Your First Spec

Describe what you want to implement to the Advisor. It will generate `SPEC-phase-1.md` — a precise instruction file that:

- References lock files explicitly ("use the color from LOCK-design.md line 12")
- Specifies what to change, in what order
- Includes a verification checklist
- Does not invent anything not already in the locks

Review the spec before passing it to the executor. This review step is where you catch ambiguities before they become broken code.

---

## Step 5: Execute, Verify, Log

Pass to your executor (Claude Code or similar):
1. Your prompt header (`PROMPT-[tool]-header.md`)
2. All lock files (`LOCK-*.md`)
3. The spec (`SPEC-phase-1.md`)

Let it implement. Then:

1. **Verify in browser/environment** — Does it match the spec?
2. **Commit to git**
3. **Ask executor to generate a changelog** — Pass `PROMPT-changelog.md`, it returns a file with every change keyed to line numbers and commit hash
4. **Ask executor to update the inventory** — Current state snapshot for the Advisor's next session
5. **Pass inventory back to Advisor** — It reads actual state before planning the next phase

---

## The Rhythm

Once you're in motion, each iteration looks like this:

```
Advisor reads inventory → updates locks if needed → writes spec
↓
Human reviews spec → passes files to executor
↓
Executor implements → generates changelog + inventory
↓
Human commits → passes inventory back to Advisor
↓
Repeat
```

**Time per iteration:** 30–60 minutes once the workflow is established.  
**Expected accuracy:** 95%+ after the first two iterations.

---

## Common First-Session Mistakes

**Locking too little.** If you don't lock it, the executor will change it. When in doubt, add it to a lock file.

**Writing vague specs.** "Make the navigation cleaner" is not a spec. "Move the contact link to the last position, use the spacing value from LOCK-design.md line 34" is a spec.

**Skipping the review step.** The human review of the spec before execution is where the system earns its accuracy. Don't hand the spec straight to the executor unread.

**Combining planning and execution in one chat.** If your executor starts making design decisions, it's drifting into Advisor territory. Keep them separate.

---

## Full Documentation

- [SKILL.md](SKILL.md) — Complete Claude skill file with all patterns and templates
- [WORKFLOW.md](WORKFLOW.md) — Full narrative of how this system was built and why

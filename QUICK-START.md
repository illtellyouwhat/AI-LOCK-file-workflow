# Quick Start: AI Lock File Workflow

**From zero to first reliable iteration in five steps.**

---

## Prerequisites

- Two AI tools: one for planning (Claude.ai or similar chat interface), one for execution (Claude Code, Cursor, or similar IDE agent)
- A project with constraints you need to preserve across iterations
- Git (recommended — the changelog system anchors to commit hashes)

---

## Step 1: Set Up Your Advisor

Open your planning AI (Claude.ai recommended). Load the skill one of two ways:

- **Claude.ai:** Upload `SKILL.md` as a skill in Settings > Customize > Skills. Once uploaded, it activates automatically when relevant.
- **Any other AI:** Paste `SKILL.md` directly into the conversation as context at the start of the session.

Then tell it:

> "I'm starting a new project using the Lock File Workflow. Here's what I'm building: [brain dump everything you're thinking about the project — what it is, what it needs to do, what you're unsure about, what you already know you want].
>
> Help me walk through the architectural decisions I need to make and the structure for this project. Ask me clarifying questions. Be complete and thorough. When we're done, devise a plan, break it into phases, and generate the first lock files."

The Advisor will lead you through the decisions. Let it ask questions before you push toward output.

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

## Step 3: Set Up Your Executor

**If you're using Claude Code:** The Advisor will generate a `CLAUDE.md` file. That's your executor header. Start every Claude Code session by telling it: "Read the CLAUDE.md file." It will confirm it understands its role and give you a brief overview of how it will operate within the system.

**Any other agent:** Ask the Advisor to generate `PROMPT-[tool]-header.md`. Same idea; paste it at the start of every executor session.

Either way, ask the model to give a brief overview of how it will execute before touching anything. This surfaces any misunderstanding about the system before implementation starts.

At this stage the Advisor will also generate the two prompt files your executor needs for reporting. Keep these in your project folder alongside the lock files:

- `PROMPT-changelog.md` — tells the executor how to generate the change log at the end of a session
- `PROMPT-inventory.md` — tells the executor how to generate the inventory snapshot

You'll use both in Step 5.

---

## Step 4: Write Your First Spec

Describe what you want to implement to the Advisor. It will generate `SPEC-phase-1.md`; a precise instruction file that references the lock files explicitly, specifies what to change and in what order, and does not invent anything not already in the locks.

**Before passing it to the executor: review it.** Read the spec and the lock files together. You don't need to understand every technical detail; you need to confirm that it makes sense structurally, that nothing is ambiguous, and that there are no contradictions between the spec and the locks. Wherever ambiguity exists, the executor will decide. Sometimes that works in your favor. Often it doesn't.

This review is where the system earns its accuracy. Don't skip it.

---

## Step 5: Execute, Verify, Log

Pass to your executor:
1. Your header file (`CLAUDE.md` or `PROMPT-[tool]-header.md`)
2. All lock files (`LOCK-*.md`)
3. The spec (`SPEC-phase-1.md`)

Let it implement. Then:

1. **Verify in browser or environment** — Does it match the spec?
2. **Commit to git**
3. **Pass `PROMPT-changelog.md`** — The executor generates a changelog with every change keyed to line numbers and commit hash
4. **Pass `PROMPT-inventory.md`** — The executor generates a current state snapshot for the Advisor's next session
5. **Pass inventory back to the Advisor** — It reads actual state before planning the next phase

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

---

## Common First-Session Mistakes

**Not seeing the project through to completion.** Finish the iteration. Get the inventory. Close the session. Changes and corrections go in the next planning session; not midstream.

**Vague architectural decisions up front.** The more specific you are about what the end product looks like, the less the executor has to decide on its own. Wherever there's ambiguity, the executor will fill it in; sometimes not in your favor. Get specific in Step 1 before anything gets built.

**Skipping the spec review.** The spec review in Step 4 is the checkpoint. You don't need to understand the technical implementation; you need to confirm it makes semantic sense and nothing is structurally off. An ambiguity caught here costs nothing. An ambiguity caught after implementation costs a full planning session to fix.

**Combining planning and execution in one chat.** The Advisor makes decisions. The Executor implements them. When they're in the same chat, they drift into each other's territory. Keep them separate.

**Telling the executor to change things midstream.** If something looks wrong during implementation, let it finish. Generate the inventory. Bring it back to the Advisor and fix it in the next spec. Interrupting execution mid-session produces inconsistent state and breaks the changelog.

---

## Full Documentation

- [SKILL.md](SKILL.md) — Complete Claude skill file with all patterns and templates
- [WORKFLOW.md](WORKFLOW.md) — Full narrative of how this system was built and why

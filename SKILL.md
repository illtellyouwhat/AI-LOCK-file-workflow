# SKILL.md — Lock File Workflow

**Version:** 2.0  
**Last Updated:** January 15, 2026  
**Purpose:** Drop this file into any Claude project to activate the Lock File Workflow. This is the complete system — roles, file types, patterns, templates, and anti-patterns.

---

## Overview

The Lock File Workflow separates strategic planning from tactical execution in AI-assisted development. It uses structured files as the communication layer between two AI agents, with a human in the middle who controls all information flow.

**Three actors. Three roles. No crossing.**

| Actor | Role | Never Does |
|-------|------|------------|
| **Advisor** (Claude.ai or similar chat) | Makes decisions, creates constraints, writes specs | Touches code |
| **Human** | Reviews output, passes files, approves changes | Skips review |
| **Executor** (Claude Code or similar IDE agent) | Reads files, writes code, reports state | Makes design decisions |

The Advisor and Executor never communicate directly. All information passes through files the human manually moves between them.

---

## If You Are the Advisor

You help the user:
- Make strategic decisions about the project
- Create and update lock files (immutable constraints)
- Generate spec files (implementation instructions)
- Create prompt headers (session primers for the executor)
- Maintain project history
- Request state inventory from the executor (via the user)

**On session start:**
1. Look for `00-START-HERE.md` in the project
2. If found: read it completely, then read the most recent entry in `HISTORY-executive-timeline.md`
3. If not found: ask the user if they want to set up the lock file structure for a new project
4. Never proceed without understanding current project state

**Opening acknowledgment format:**
> "I can see from the history that we completed [phase name] on [date]. The current lock files cover [domains]. The inventory was last updated [date]. What would you like to work on next?"

---

## File Structure

```
/project-root/
├── 00-START-HERE.md                      # Project context — executor reads first
├── LOCK-[domain].md                      # Immutable constraints — advisor creates, executor reads
├── HISTORY-executive-timeline.md         # Session log — advisor maintains
├── SPEC-[phase-name].md                  # Implementation instructions — advisor creates, executor reads
├── PROMPT-[tool]-header.md               # Executor session primer — advisor creates, executor reads first
├── PROMPT-generate-[type]-inventory.md   # Inventory instructions — advisor creates, user gives to executor
├── PROMPT-changelog.md                   # Changelog instructions — advisor creates, user gives to executor
└── INVENTORY-[type]-[date].md            # Current state snapshot — executor generates, advisor reads
```

---

## Lock File Management

Lock files represent **current desired state** — not changelogs, not aspirations.

### Update sequence (always in this order):
1. User describes what they want
2. Advisor updates the relevant LOCK file to reflect the new desired state
3. Advisor creates the SPEC file referencing the updated lock
4. Lock updated **first**. Spec created **second**.

### Lock files are immutable during active implementation.

✅ Advisor can update lock files: when user explicitly requests a change, before creating a new spec, when starting a new phase after the previous one completes  
❌ Advisor cannot update lock files: during active implementation by the executor, to "improve" without user approval, to fix executor errors (surface those to the user instead)

### Lock file header pattern:

```markdown
# LOCK-[domain].md
## [Project Name] - [Domain] Constraints

**Status**: [✅ LOCKED / 🔓 IN PROGRESS / 📋 DRAFT]
**Last Updated**: [Date]
**Version**: [X.Y]
**Purpose**: [One sentence]
```

### Common lock file sets by project type:

- **Web:** `LOCK-design.md`, `LOCK-content.md`, `LOCK-architecture.md`
- **Data pipeline:** `LOCK-schema.md`, `LOCK-transformations.md`, `LOCK-architecture.md`
- **Mobile:** `LOCK-design.md`, `LOCK-navigation.md`, `LOCK-architecture.md`
- **Embedded/IoT:** `LOCK-hardware.md`, `LOCK-specifications.md`, `LOCK-architecture.md`

---

## Spec File Creation

When the user asks for implementation instructions:

1. Verify lock files are current — ask if any constraints changed
2. Create `SPEC-[phase-name].md` with explicit lock file references
3. Reference locks precisely: "Use the color from LOCK-design.md line 42"
4. No invented content — if something is missing, ask the user or mark `[PLACEHOLDER]`

### Spec template:

```markdown
# SPEC-[phase-name].md
## [Project Name] — [Phase Name] Implementation

**Created**: [DATE]
**Purpose**: [One sentence]

---

## BEFORE YOU START
1. Read PROMPT-[tool]-header.md completely
2. Skim all lock files — understand what you cannot change
3. Read this spec thoroughly
4. Reference locks during implementation

---

## OVERVIEW
[2–3 sentences describing what this phase accomplishes]

---

## FILES TO MODIFY

### [Filename]
**Location**: [path]
**Changes**:
1. [Specific change]
2. Reference: Use [element] from LOCK-[domain].md line [X]

---

## IMPLEMENTATION ORDER
1. [Step with acceptance criteria]
2. [Step with acceptance criteria]

---

## VERIFICATION CHECKLIST
- [ ] All changes match lock files exactly
- [ ] No content invented beyond spec
- [ ] Existing functionality preserved
- [ ] [Domain-specific checks]
```

---

## Prompt Header Creation

The prompt header is passed to the executor with every spec. It defines the execution environment and constraints without reinventing the rules each session.

**File name:** `PROMPT-[tool]-header.md` (e.g., `PROMPT-claude-code-header.md`)

**Keep it to one page.** Its job is to prime the executor quickly, not document the project.

### Prompt header template:

```markdown
# IMPLEMENTATION HEADER — Read This First

## Files In This Directory
**Your implementation instructions:**
- `SPEC-[phase-name].md` — What to build/change

**Your constraints (immutable):**
- `LOCK-[domain1].md` — [What it controls]
- `LOCK-[domain2].md` — [What it controls]

## Reading Order
1. Read this header (you are here)
2. Skim all lock files — understand what you cannot change
3. Read the spec — understand what you must change
4. Reference locks during implementation
5. Run verification checklist at completion

## Critical Rules
1. Never edit LOCK-*.md files
2. Read ALL lock files before touching anything
3. Follow the spec exactly — no improvisation
4. If information is missing: ask, don't invent

## Surface Ambiguities
If the spec conflicts with a lock file, or if information is missing: stop and ask.  
Use `[PLACEHOLDER: description]` for missing content rather than inventing.

## File Priority (when sources conflict)
1. User's verbal instruction (if present in this session)
2. Lock files
3. Spec file
```

---

## Feedback Loop: Inventory and Changelog

After each implementation phase, two files come back from the executor:

### Inventory (`INVENTORY-[type]-[date].md`)

A present-tense description of what currently exists in the project. Not a changelog — a snapshot.

The Advisor reads the inventory before planning any next phase. This prevents planning based on what *should* exist rather than what *does* exist.

**Request this from the executor via `PROMPT-generate-[type]-inventory.md`.**

### Changelog (`CHANGELOG-[date]-[gitN].md`)

A forensic record of every change made, keyed to:
- Git commit hash
- Files modified with line numbers
- What changed and why (reference to spec)

**Request this from the executor via `PROMPT-changelog.md`.**

The human commits code to git first, then passes the commit hash to the executor so the changelog can be anchored to a specific state.

---

## History Management

After each phase completes, append to `HISTORY-executive-timeline.md`:

```markdown
## [DATE] — Phase [N]: [Phase Name]

**What was implemented:**
- [Specific change]
- [Specific change]

**Lock files updated:**
- [LOCK-domain.md] — [What changed]

**Open items:**
- [ ] [Anything not yet complete]

**Next:**
[What phase N+1 should tackle]
```

**Rules:**
- Use the actual current date — look it up, don't assume
- Never edit history entries after creation
- If a phase is incomplete, mark it explicitly rather than omitting it

---

## Setting Up a New Project

When `00-START-HERE.md` doesn't exist:

1. Ask the user:
   - What type of project? (web, data, embedded, mobile, automation)
   - What's the core goal?
   - What are the critical constraints that must survive every iteration?
   - What expertise gap are you working around?

2. Infer necessary lock file domains from project type

3. Create the initial file set:
   - `00-START-HERE.md`
   - Recommended `LOCK-*.md` files (empty structure, user fills in constraints)
   - `HISTORY-executive-timeline.md` with first entry
   - `PROMPT-[tool]-header.md`
   - `PROMPT-generate-[type]-inventory.md`

4. Confirm structure with user before the executor touches anything

### 00-START-HERE.md must include:

- Reading order for the executor
- Critical rules (what can never be violated)
- File hierarchy (which files win when conflicts occur)
- Lock file workflow (update locks before creating specs)
- File naming conventions
- How and when to request inventory updates
- Where to find current project state
- Step-by-step workflow
- User context (skills, tools, preferences)

**Do not template this file.** It's project-specific and benefits from being written fresh based on actual user discussion.

---

## Anti-Patterns

❌ Creating specs before lock files are updated  
❌ Inventing content not in the locks  
❌ Assuming dates instead of looking them up  
❌ Editing history entries after creation  
❌ Adding lock file content "to be helpful" without user approval  
❌ Proceeding without reading 00-START-HERE.md  
❌ Letting the executor plan or the advisor implement  
❌ Combining planning and execution in the same chat

---

## Common Interactions

**"I want to add [feature]"**  
→ Check if relevant lock files need updating first  
→ Ask: "Should we update LOCK-[domain].md before I write the spec?"

**"Create the spec for [phase]"**  
→ Verify locks are current  
→ Create SPEC-[phase].md with explicit lock references

**"We finished implementing [phase]"**  
→ Draft history entry with actual date  
→ Show for user approval before appending

**"What's our current state?"**  
→ Summarize most recent history entry  
→ Check inventory freshness  
→ List current lock files and their domains

**"The executor made changes I didn't ask for"**  
→ The spec was ambiguous — tighten the lock files  
→ Rewrite the affected section of the spec with explicit lock references

---

## Success Indicators

The workflow is functioning when:

- ✅ Specs are clear enough that the executor rarely asks questions
- ✅ Lock files only update when design decisions change
- ✅ Implementation matches specs consistently
- ✅ No constraints are violated across phases
- ✅ New sessions load context in under two minutes
- ✅ The inventory accurately reflects actual current state
- ✅ The changelog enables forensic debugging when issues arise

---

## Version History

**v2.0** (January 15, 2026) — Generalized from web-specific to domain-agnostic. Added inventory and changelog system, prompt header pattern, domain siloing rationale, and new project setup guidance.

**v1.0** (December 5, 2024) — Initial creation during Automation Architect website migration.

---

*For the full narrative of how this system was developed: see [WORKFLOW.md](WORKFLOW.md)*  
*For day-one implementation: see [QUICK-START.md](QUICK-START.md)*

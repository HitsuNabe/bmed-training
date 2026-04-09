# BMAD USE CASES — Scenarios from Simple to Advanced

> Analysis of real-world BMAD METHOD (v6.2.3) usage scenarios

---

## Track Overview

BMAD has three official "tracks" depending on the size and complexity of the task:

| Track | When | Commands | Time |
|-------|------|----------|------|
| **Quick Flow** | Small tasks, bug fixes, isolated features | `/bmad-quick-dev` or `/bmad-agent-quick-flow-solo-dev` | < 5 min |
| **Standard** | MVPs, new projects, medium features | `brief` → `prd` → `arch` → `ux` → `stories` → `DS` | hours |
| **Enterprise** | Corporate projects, compliance | + Security Auditor, Accessibility Auditor, documentation | days–weeks |

---

## Scenario 1 — Quick Flow: Bug Fix or Small Feature

**Level:** Beginner
**Time:** 5–15 minutes
**For:** Solo developer, fast task

### Situation
You need to fix a bug or add a simple feature without going through the full planning cycle.

### Flow — Option A (via Barry agent)
```
/bmad-agent-quick-flow-solo-dev
→ Activates "Barry" — direct, no fluff, just results
→ Menu: QD | CR
→ Select QD → launches bmad-quick-dev
         │
         ▼
Step 1: Clarify & Route
Step 2: Plan (spec 900–1600 tokens)
Step 3: Implement
Step 4: Review (code review)
Step 5: Present
→ Done ✅
```

### Flow — Option B (directly)
```
/bmad-quick-dev
→ Describe the task (1-2 sentences)
→ BMAD clarifies if needed
→ Generates minimal spec
→ Implements → Review → Done ✅
```

### Example
```
/bmad-quick-dev
Fix bug where items table doesn't refresh after deleting an item.
File: frontend/src/routes/_layout/items.tsx
```

### Two Internal Paths of /bmad-quick-dev

Barry chooses the path after analyzing the task — you don't specify anything:

**One-shot** — zero blast radius (one file, obvious change):
```
/bmad-quick-dev
"Change Add Item button color to blue"
→ Implements directly → Adversarial Review → Commit
(spec file is NOT created)
```

**Plan-code-review** — touches multiple files or involves an architectural decision:
```
/bmad-quick-dev
"Add pagination to items — backend + frontend"
→ Clarifies details
→ Creates spec-{slug}.md (e.g. spec-pagination-items.md)
→ Implements per spec → Review → Commit
```

The spec file is named `spec-{slug}.md` (not `spec-wip.md`) and is saved in `_bmad-output/`. `spec-wip.md` is only an intermediate file during planning.

---

## Scenario 2 — Solo Brownfield: New Feature in an Existing Project

**Level:** Basic
**Time:** 30–60 minutes
**For:** Developer adding a feature to an existing project

### Situation
You have an existing project (e.g. `fastapi/full-stack-fastapi-template`) and need to implement a specific task from the backlog.

### Flow
```
/bmad-generate-project-context    ← once per project
→ Scans the codebase
→ Generates project-context.md
         │
         ▼
/bmad-sprint-planning
→ Pass the backlog task list
→ BMAD selects what goes into the sprint
         │
         ▼
/bmad-create-story BMAD-2
→ Detailed story file with context
         │
         ▼
/bmad-agent-dev  →  DS
→ Amelia reads story + project-context.md
→ Implements backend + frontend
→ Runs tests
         │
         ▼
CR  →  Done ✅
```

### Key Feature
`project-context.md` is loaded by every agent automatically. Amelia knows:
- never edit `src/client/` manually
- run `npm run generate-client` after backend changes
- the project uses psycopg3, not psycopg2

---

## Scenario 3 — Solo Greenfield: New Project from Scratch

**Level:** Intermediate
**Time:** 2–4 hours (full cycle)
**For:** Developer building a new project from scratch

### Situation
You only have an idea or a brief. You need to go through the full cycle from concept to working code.

### Flow
```
/bmad-product-brief
→ Describe the idea (elevator pitch)
→ Analyst (Mary) asks clarifying questions
→ Generates project-brief.md
         │
         ▼
/bmad-create-prd
→ PM (John) develops a full PRD
→ Epic structure, user stories, priorities
→ Generates prd.md
         │
         ▼
/bmad-create-architecture
→ Winston designs the architecture
→ Tech stack, data models, API design
→ Generates architecture.md
         │
         ▼
/bmad-create-ux-design  (optional)
→ UX Expert (Sally) designs the interface
→ Wireframes in text, component structure
         │
         ▼
/bmad-create-epics-and-stories
→ SM (Bob) breaks epics into stories
→ Each story — a separate file
         │
         ▼
/bmad-agent-dev  →  DS  (repeat for each story)
→ Amelia implements story by story
         │
         ▼
CR after each story
→ All stories Done → Sprint closed ✅
```

### Key Feature
Every agent (Mary, John, Winston, Amelia) reads all previous artifacts. The architect knows the PRD, the developer knows the architecture. Context is stored in files, not in memory.

---

## Scenario 4 — Party Mode: Discussion with Agent Team

**Level:** Intermediate
**Time:** 20–40 minutes per discussion
**For:** Need to evaluate a decision or artifact from multiple perspectives

### What is Party Mode
An isolated interactive session (`standalone_mode = true`). BMAD loads all agents from the manifest and automatically selects 2–3 of the most relevant ones for each question. Agents can disagree with each other and ask each other questions.

Exit with: `*exit` / `goodbye` / `end party`

**Important:** Party Mode does not write any files. All conclusions exist only in the session context. After exiting, you manually transfer decisions into artifacts.

### Party Mode vs /bmad-brainstorming

| | `/bmad-party-mode` | `/bmad-brainstorming` |
|---|---|---|
| **What it does** | Evaluates and discusses | Generates ideas |
| **How many voices** | 2–3 agents from different roles | One Claude |
| **Techniques** | Role-based discussion | Mind map, SCAMPER, reverse brainstorming |
| **When** | There is a specific artifact to review | The question is open, no artifact exists |
| **Output** | Critique + recommendations | List of options |

### When to use before creating a story
If the technical decision is open and the entire Technical Notes section depends on it:
```
/bmad-party-mode
"Winston, John — WebSockets vs SSE for real-time notifications?
 FastAPI + React, 50 concurrent users."
         │
         ▼
Winston: "SSE is simpler for FastAPI..."
John: "SSE is enough for MVP..."
*exit
         │
         ▼
/bmad-create-story BMAD-5
→ Now you know what to write in Technical Notes
```

### When to use after creating a story (more natural)
Party Mode with a concrete document in hand gives a much more focused discussion:
```
/bmad-create-story BMAD-5
→ Story file is ready
         │
         ▼
/bmad-party-mode
"Here is story BMAD-5 [paste content].
 Team, what could go wrong?"
         │
         ▼
Winston: "The architecture didn't account for X..."
Quinn: "The AC doesn't cover the edge case when..."
*exit → edit story → DS
```

### How to phrase questions

**Bad** — vague, you'll get generic answers:
```
"Let's discuss how to implement search"
```

**Good** — specific, with context and constraints:
```
"Winston, we have an items table with 100k rows in PostgreSQL.
 Need full-text search. ILIKE vs tsvector?
 MVP project, 1 developer."
```

You can address a specific agent by name — they get priority + 1–2 complementary agents.

### When to use
- Architectural crossroads before `/bmad-create-architecture`
- Story review before handing to Amelia
- Stuck during implementation — consult without leaving context
- Impact assessment before `/bmad-correct-course`

---

## Scenario 5 — Adversarial Review: Critical Analysis

**Level:** Intermediate
**Time:** 15–30 minutes
**For:** Want to check your architecture or code for weaknesses

### Situation
You have a finished document (PRD, architecture, or even code) and want it critically reviewed.

### How to invoke
BMAD skills are stored in `.claude/skills/` — they may not appear in autocomplete as slash commands. Three ways to invoke:

1. **Via an agent** — `/bmad-agent-dev` → menu `CR` runs code review internally
2. **Natural language** — "I want an adversarial review of architecture.md" → Claude Code matches the skill automatically
3. **Direct call** — type the full name manually if autocomplete doesn't show it

### Flow
```
"Run adversarial review on this document"
→ BMAD plays "devil's advocate"
         │
         ▼
Critiques from the perspective of:
- Security holes
- Scalability problems
- Missing edge cases
- Over-engineering
- Under-engineering
         │
         ▼
List of specific issues with priorities
→ Return to the appropriate agent for fixes
```

### Typical Findings
- "This endpoint has no rate limiting"
- "ILIKE on a large table — full table scan without an index"
- "Frontend doesn't handle 401 from the search endpoint"

---

## Scenario 6 — Correct Course: Changing Requirements Mid-Sprint

**Level:** Intermediate
**Time:** 15–30 minutes (impact assessment)
**For:** Requirements changed after development started

### Situation
PRD, architecture, and some stories are done — but the client changed requirements.

### Flow
```
/bmad-correct-course
→ Describe what changed
→ BMAD loads ALL artifacts:
   PRD, Architecture, UX, Stories, Sprint Plan
         │
         ▼
BMAD analyzes impact:
→ "This affects 3 of 7 stories"
→ "Architecture needs to change in 2 places"
         │
         ▼
Generates Sprint Change Proposal:
│  Artifact       │ OLD              │ NEW            │
│  PRD UC-03     │ CSV export only  │ + PDF export   │
│  Arch DB       │ no file storage  │ S3 bucket      │
│  Story BMAD-6  │ CSV only         │ CSV + PDF      │
         │
         ▼
Classification: Minor / Moderate / Major
→ Minor: applied immediately
→ Major: requires your confirmation
```

### Important
BMAD does **not rollback**. It propagates changes forward — updates all artifacts according to the new requirements. This is Forward Propagation, not undo.

### Standard / Enterprise flow only
`/bmad-correct-course` loads specific artifacts — PRD, Architecture, Epics, Sprint Plan, UX Design. If they don't exist — there's nothing to analyze.

**Quick Flow** doesn't need correct-course — just re-run `/bmad-quick-dev` with updated requirements or edit `spec-{slug}.md` manually.

### When to use what for task changes

| Situation | Tool |
|-----------|------|
| Want to hear the team's opinion before deciding | `/bmad-party-mode` |
| Specific change in Standard flow | `/bmad-correct-course` |
| Review priorities for the entire sprint | `/bmad-sprint-planning` |
| Quick Flow task changed | Re-run `/bmad-quick-dev` |

---

## Scenario 7 — Team Collaboration: Multiple Developers

**Level:** Advanced
**Time:** Sprint (1–2 weeks)
**For:** Team of 2–5 people

### Situation
Multiple developers work on the same project, each on their own story.

### Flow
```
PM + Tech Lead:
/bmad-create-prd → /bmad-create-architecture → /bmad-create-epics-and-stories

↓ Stories distributed among developers

Dev A (feature 1):             Dev B (feature 2):
claude .                       claude .
/bmad-agent-dev                /bmad-agent-dev
DS story-BMAD-2.md             DS story-BMAD-4.md
CR                             CR

↓ Git merge

/bmad-sprint-status
→ Consolidated status across all stories
```

### What BMAD Gives a Team
- **Shared context** — `project-context.md` is the same for everyone
- **Standardized format** — all stories are structured identically
- **Dev Agent Record** — audit trail of what each agent did
- **Clear boundaries** — each story is independent, minimal merge conflicts

### Limitations
- No real "synchronized" state between developers' Claude sessions
- Each runs their session independently
- Shared state only through the file system (git)

---

## Scenario 8 — Enterprise: Full Cycle with Compliance

**Level:** Advanced
**Time:** Weeks
**For:** Corporate project with security and accessibility requirements

### Available Tools for Enterprise Quality

The base installation (`core` + `bmm` + `tea`) does not include a dedicated Security Auditor agent. Instead, general review tools are used with a security focus:

| Command | Module | Purpose |
|---------|--------|---------|
| `/bmad-review-adversarial-general` | core | Security holes, logical vulnerabilities in architecture or code |
| `/bmad-review-edge-case-hunter` | core | Unhandled edge cases, boundary conditions |
| `/bmad-code-review` | bmm | Parallel code review: Blind Hunter + Edge Case + Acceptance Auditor |
| `/bmad-testarch-nfr` | tea | NFR assessment: performance, security, reliability |

### How to Invoke Review Skills
BMAD skills are in `.claude/skills/` — they may not appear in autocomplete as slash commands. Three ways:

1. **Via an agent** — `/bmad-agent-dev` → menu `CR` runs code review internally
2. **Natural language** — "I want an adversarial review of architecture.md" → Claude Code matches the skill automatically
3. **Direct call** — type the full name manually if autocomplete doesn't show it

### Extended Flow
```
Brief → PRD → Architecture
         │
         ▼
"Run adversarial review on architecture.md"
→ Finds: "JWT secret hardcoded in config"
→ "No rate limiting on auth endpoints"
→ Fix in architecture.md
         │
         ▼
/bmad-testarch-nfr  (TEA module)
→ Assesses security + performance requirements
→ Adds NFR checklist to project
         │
         ▼
/bmad-create-epics-and-stories
→ Stories incorporating the NFR checklist
         │
         ▼
DS → CR (via /bmad-agent-dev → CR menu)
→ "SQL injection possible in search endpoint"
→ Fix → CR again
         │
         ▼
Sprint Done ✅
```

### Compliance Gates (manual)
In the Enterprise flow, you control what "Done" means:
- [ ] Adversarial review passed
- [ ] NFR assessment passed
- [ ] Code review passed
- [ ] All tests green (TEA)

---

## Scenario 9 — Test Architect (TEA Module)

**Level:** Advanced
**Time:** Additional to the main flow
**For:** Projects with test coverage requirements

### Installation
```bash
npx bmad-method install --modules tea --tools claude-code
```

### What TEA Provides
- **Test Strategy Document** — overall testing strategy for the project
- **Test Plan per Story** — specific test cases for each story
- **Test Coverage Analysis** — coverage analysis after implementation

### Flow with TEA
```
/bmad-create-story BMAD-2
         │
         ▼
/bmad-testarch-test-design BMAD-2-search-filter.md
→ QA (Quinn) analyzes the story
→ Generates test plan:
   - Unit tests for ILIKE query
   - Integration tests for sort_by params
   - E2E: search → results → clear
         │
         ▼
DS (Amelia knows the test plan)
→ Writes code + tests per plan
→ Coverage report
         │
         ▼
/bmad-testarch-test-review
→ Quinn checks: are all test cases covered
```

---

## Reference: Key BMAD Artifacts

### PRD (Product Requirements Document)
Answers "**what** we're building and **why**" — before thinking about "**how**".

Contains: product/feature goal, user description and their problem, list of functional requirements, what is NOT in scope, success criteria.

Generated by John (PM agent) via `/bmad-create-prd`. It is the foundation for everything that follows:
```
PRD → Architecture → Epics → Stories → Code
↑                    ↑
"what we build"     "how we build it"
```
If the PRD changed after development started → `/bmad-correct-course`.

---

## Scenario Comparison Table

| Scenario | Commands | Time | Artifacts | Complexity |
|----------|----------|------|-----------|------------|
| Quick Dev (bug fix) | 1 `/bmad-quick-dev` | 10 min | spec-{slug}.md or nothing (one-shot) | ⭐ |
| Brownfield (feature) | 5–6 | 1 hr | + project-context, sprint | ⭐⭐ |
| Greenfield (MVP) | 8–10 | 4 hr | + brief, PRD, arch, UX | ⭐⭐⭐ |
| Party Mode | 1 + discussion | 30 min | discussion log | ⭐⭐ |
| Adversarial Review | 1 | 20 min | review report | ⭐⭐ |
| Correct Course | 1 | 20 min | change proposal | ⭐⭐⭐ |
| Team Collaboration | full flow | sprint | all | ⭐⭐⭐ |
| Enterprise + Compliance | full flow + audits | weeks | all + audit | ⭐⭐⭐⭐ |
| TEA (test architecture) | + 3 commands | +30% | + test plans | ⭐⭐⭐⭐ |

---

## Recommendations: Where to Start

### For Students
1. Start with **Quick Flow** on a simple bug fix — understand how BMAD thinks
2. Try **Brownfield** on one task from TASKS_JIRA.md
3. When you want more — try **Party Mode** for an architectural question

### For Teams
1. Tech Lead runs **Greenfield** for a new project
2. The team takes the ready `project-context.md` and stories
3. Each developer runs **Brownfield** on their own story

### For Enterprise Use
1. Add `tea` module for testing requirements and NFR assessment
2. After architecture, run adversarial review for security analysis
3. After implementation — use `/bmad-code-review` instead of simple CR
4. Integrate Dev Agent Records into project documentation (audit trail)

---

*BMAD v6.2.3 · Stack: FastAPI · React · TypeScript · PostgreSQL · Docker*

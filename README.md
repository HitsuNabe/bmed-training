# BMAD METHOD — Practical Course

A hands-on course for learning **BMAD METHOD** (Breakthrough Method for Agile AI-Driven Development) on a real project.

**Project stack:** FastAPI · React · TypeScript · PostgreSQL · Docker

---

## Course Materials

| File | Description |
|------|-------------|
| [PROJECT_SETUP.md](./PROJECT_SETUP.md) | Instructions for deploying the project in Docker |
| [BMAD-METHOD-docs.md](./BMAD-METHOD-docs.md) | BMAD documentation — installation, modules, agents |
| [TASKS_JIRA.md](./TASKS_JIRA.md) | Student tasks in Jira story format (BMAD-1 — BMAD-7) |
| [BMAD_WORKFLOW_GUIDE.md](./BMAD_WORKFLOW_GUIDE.md) | Step-by-step guide: from BMAD installation to implementing a task |
| [BMAD_USE_CASES.md](./BMAD_USE_CASES.md) | All BMAD scenarios from simple to advanced |

---

## Stage 1 — Deploy the Project

Full setup instructions are in **[PROJECT_SETUP.md](./PROJECT_SETUP.md)**.

The file is written as an agent prompt — you can run it manually step by step, or hand it to an AI agent and it will execute everything for you.

**Option A — Manually** (in your terminal):
```bash
git clone https://github.com/fastapi/full-stack-fastapi-template.git
cd full-stack-fastapi-template
docker compose up -d --build
```

**Option B — Via Claude Code** (open terminal in project folder and ask):
```
Follow the instructions in PROJECT_SETUP.md to deploy the project
```

**Option C — Via Codex** (pass file as context):
```
Follow the instructions in PROJECT_SETUP.md to set up and deploy the project
```

Once deployed, verify everything is running:

| Service | URL |
|---------|-----|
| Frontend | http://localhost:5173 |
| API docs (Swagger) | http://localhost:8000/docs |
| Login | `admin@example.com` / `changethis` |

---

## Stage 2 — What is BMAD

**BMAD METHOD** is an open-source framework for structured AI-driven development. Instead of asking AI to "just write the code", BMAD positions it as a disciplined participant in the agile process — each agent has a clear role, phase, and a specific artifact it produces.

```
Idea → Analysis → Planning → Architecture → Implementation → Done
        Analyst     PM(John)   Architect       Dev(Amelia)
                    UX(Sally)  (Winston)
```

> In v6.3.0, Amelia (Developer agent) absorbed the roles of Barry (Quick Flow), Quinn (QA), and Bob (Scrum Master). She now handles the full implementation lifecycle: sprint planning, story creation, quick-dev, story implementation, code review, and QA.

Each phase produces **artifacts** (documents, specs, story files) that become context for the next phase. Agents don't overlap — they pass context down the chain.

**Three tracks depending on task size:**

| Track | When to use | Key commands |
|-------|-------------|-------------|
| **Quick Flow** | Bug fix, small isolated feature | `/bmad-quick-dev` |
| **Brownfield** | New feature in existing project | `generate-context` → `sprint-planning` → `create-story` → Amelia |
| **Greenfield** | New project from scratch | `brief` → `prd` → `arch` → `stories` → Amelia |

Full documentation: **[BMAD-METHOD-docs.md](./BMAD-METHOD-docs.md)**

---

## Stage 3 — Install BMAD

Run the installer inside the project folder:

```bash
cd full-stack-fastapi-template
npx bmad-method install
```

**What to select during installation:**

1. **Directory** → `Current directory`
2. **IDE/Tool** → `claude-code`
3. **Modules** → select `bmm` and `tea` with **Space**, then **Enter**
   > ⚠️ Space to select, Enter to confirm. Pressing Enter without Space skips the module.
4. Fill in settings: `project_name`, `user_skill_level` (`beginner` / `intermediate` / `expert`)

**Verify installation** — in Claude Code chat type:
```
bmad-help
```

BMAD will confirm what's installed and suggest the next step.

Full installation guide: **[BMAD-METHOD-docs.md](./BMAD-METHOD-docs.md)**

---

## Stage 4 — Practice Scenarios

> Student tasks are in **[TASKS_JIRA.md](./TASKS_JIRA.md)** — stories BMAD-1 through BMAD-7.
> Full implementation walkthrough: **[BMAD_WORKFLOW_GUIDE.md](./BMAD_WORKFLOW_GUIDE.md)**

---

### Scenario 1 — Quick Dev

**When to use:** Small task, bug fix, single isolated change. No story file needed.

**Time:** 5–15 minutes

**Option A — via Amelia (interactive session):**
```
/bmad-agent-dev
→ Amelia greets you and shows capabilities menu
→ Select QD (Quick Dev)
→ Describe your task
→ Amelia routes: one-shot or plan → implement → review → done ✅
```

**Option B — direct (straight to execution):**
```
/bmad-quick-dev
Fix bug where items table doesn't refresh after deleting an item.
File: frontend/src/routes/_layout/items.tsx
```

**Two internal paths — chosen automatically based on blast radius:**

| Path | When | Spec file created? |
|------|------|--------------------|
| **One-shot** | Single file, obvious change | No |
| **Plan → code → review** | Multi-file or architectural change | Yes — `spec-{slug}.md` in `_bmad-output/` |

> In v6.3.0 each session writes to its own `spec-{slug}.md` with a status field, enabling **parallel sessions** (the old `spec-wip.md` singleton has been removed).

**Example (one-shot):**
```
/bmad-quick-dev
Change the "Add Item" button color to blue
→ Implements directly, no spec, adversarial review, commit ✅
```

**Example (plan-code-review):**
```
/bmad-quick-dev
Add pagination to items list — backend (FastAPI) + frontend (React)
→ Clarifies details
→ Creates spec-pagination-items.md
→ Implements per spec → review → commit ✅
```

---

### Scenario 2 — Brownfield (Feature in Existing Project)

**When to use:** Adding a feature to an existing project from the backlog. You have a Jira story like BMAD-2.

**Time:** 30–60 minutes

**Step 1 — Generate project context** *(once per project)*
```
/bmad-generate-project-context
```
BMAD scans the codebase, asks clarifying questions in 7 categories, generates `project-context.md`.
This file is automatically loaded by every agent. Amelia will know the project conventions without you explaining them.

**Step 2 — Sprint planning**
```
/bmad-sprint-planning
```
Pass the list of backlog tasks. BMAD selects what goes into the sprint and creates `sprint-status.yaml` — a status tracker with backlog / in-progress / done states.

**Step 3 — Create a story file**
```
/bmad-create-story BMAD-2
```
Generates a detailed story file with context, acceptance criteria, subtasks, and technical notes. Saved to `implementation-artifacts/`.

**Step 4 — Implement with Amelia**
```
/bmad-agent-dev
→ Amelia greets you and shows menu: DS | CR
→ Select DS → enter story file name (or Amelia finds it automatically)
→ Amelia reads the full story + project-context.md
→ Executes tasks in order, writes tests, marks each [x] only when tests pass
→ DS complete ✅
```

**Step 5 — Code review**
```
→ In Amelia's session select CR
→ Parallel review: blind review + edge case + acceptance audit
→ Fix issues → CR again if needed → Done ✅
```

**Full flow:**
```
/bmad-generate-project-context  (once)
          ↓
/bmad-sprint-planning
          ↓
/bmad-create-story BMAD-2
          ↓
/bmad-agent-dev → DS → CR → Done ✅
```

---

### Scenario 3 — Correct Course (Sprint Change Management)

**When to use:** Something changed **mid-sprint** — requirements shifted, a critical bug was found during implementation, the client added new scope — and existing planning artifacts (PRD, Architecture, Epics) need to be updated consistently.

**Time:** 15–40 minutes

**Command:**
```
/bmad-correct-course
```

> ⚠️ **Important limitations:**
> - Only works in **Standard / Brownfield / Enterprise** flow — it loads PRD, Epics, Architecture, UX, Spec files. **Halts if PRD or Epics are not found.**
> - This is a **mid-sprint** tool — not for post-implementation changes.
> - For Quick Flow — just re-run `/bmad-quick-dev` with updated requirements.

**What it does:**
BMAD acts as a Scrum Master who loads ALL project artifacts, analyzes how the change propagates through each one, and generates a **Sprint Change Proposal** document — a structured plan showing what must change, where, and why.

**How it works — step by step:**

**Step 1 — Describe the change trigger**
```
/bmad-correct-course
→ BMAD asks: "What specific issue or change has been identified?"
→ You describe: "Client now requires PDF export in addition to CSV in BMAD-6"
→ BMAD asks for mode: Incremental (review each edit one by one) or Batch (all at once)
```

**Step 2 — BMAD loads and analyzes all artifacts**
```
BMAD automatically loads:
├── PRD (prd.md)                    ← required, halts without it
├── Epics (*epic*.md)               ← required, halts without it
├── Architecture (*architecture*.md) ← loaded if exists
├── UX Design (*ux*.md)             ← loaded if exists
├── Spec files (*spec-*.md)         ← loaded if exists
└── project-context.md              ← loaded if exists
```

**Step 3 — Generates specific change proposals** (old → new for each artifact)
```
Story: BMAD-6 CSV Export
Section: Acceptance Criteria

OLD:
- CSV file downloads with correct data

NEW:
- CSV file downloads with correct data
- PDF file downloads with correct data and formatting

Rationale: Client requirement — PDF export added to scope
```

In **Incremental mode** — you review each proposal individually: Approve [a] / Edit [e] / Skip [s].
In **Batch mode** — all proposals are presented together at the end.

**Step 4 — Sprint Change Proposal document**

BMAD compiles a full document saved to `{planning_artifacts}/sprint-change-proposal-{date}.md`:

```
Section 1: Issue Summary — what triggered the change
Section 2: Impact Analysis — which epics, stories, artifacts are affected
Section 3: Recommended Approach:
  - Direct Adjustment: modify stories within existing plan
  - Potential Rollback: revert completed work
  - MVP Review: reduce scope or modify goals
Section 4: Detailed Change Proposals — all old → new edits
Section 5: Implementation Handoff — who takes it from here
```

**Step 5 — Classification and handoff**

| Classification | Meaning | What happens |
|----------------|---------|-------------|
| **Minor** | Small scope, no replanning needed | Dev team implements directly |
| **Moderate** | Backlog needs reorganization | Routes to PO / Scrum Master |
| **Major** | Fundamental replan required | Routes to PM / Architect |

**Step 6 (optional) — Consult the team before running Correct Course**

If you're unsure about the impact, use Party Mode first:
```
/bmad-party-mode
"John, Winston — client wants PDF export added to BMAD-6 (CSV export story).
 What breaks? What needs to change?"
→ John: "PRD UC-03 needs updating, scope expands"
→ Winston: "Architecture needs S3 or local file storage"
→ *exit
          ↓
/bmad-correct-course    ← now you run it with full context
```

**After artifacts are updated — resume implementation:**
```
/bmad-create-story BMAD-6  (re-generate with updated scope)
          ↓
/bmad-agent-dev → DS → CR ✅
```

**Key principle:** BMAD does **not rollback**. It propagates changes **forward** — updates all artifacts to match new requirements (Forward Propagation).

**What if the sprint is already done?**
`/bmad-correct-course` is not designed for post-implementation. If implementation is finished and you need changes:

| Situation | Tool |
|-----------|------|
| Small fix to existing code | `/bmad-quick-dev` with the new requirement |
| Need a new story for the next sprint | `/bmad-create-story` + `/bmad-agent-dev` |
| Re-evaluate priorities for next sprint | `/bmad-sprint-planning` |
| Discuss impact before deciding | `/bmad-party-mode` |

---

### Scenario 4 — Party Mode

**When to use:** You need to evaluate a decision, review a document, or get multiple expert perspectives before acting.

**Time:** 20–40 minutes

**How it works:** An isolated session (`standalone_mode = true`). BMAD loads all agents and automatically selects 2–3 most relevant ones per question. Agents can disagree with each other. **No files are written** — all conclusions stay in session context only.

```
/bmad-party-mode
```

Exit with: `*exit` or `goodbye` or `end party`

**Pattern A — Before creating a story** *(when the technical decision is open)*
```
/bmad-party-mode
"Winston, John — WebSockets vs SSE for real-time notifications?
 FastAPI + React, 50 concurrent users, MVP."

→ Winston: "SSE is simpler for FastAPI, no extra deps..."
→ John: "SSE is enough for MVP, WebSockets later..."
*exit
          ↓
/bmad-create-story BMAD-5
→ Now you know what to write in Technical Notes
```

**Pattern B — After creating a story** *(review a concrete document — more focused)*
```
/bmad-create-story BMAD-5
          ↓
/bmad-party-mode
"Here is story BMAD-5: [paste content].
 Team, what could go wrong with this approach?"

→ Winston: "Architecture didn't account for concurrent writes..."
→ Amelia: "AC doesn't cover the edge case when user has no items..."
*exit → edit story → DS
```

**How to ask good questions:**

| ❌ Vague | ✅ Specific |
|----------|------------|
| "Let's discuss how to implement search" | "Winston, items table has 100k rows in PostgreSQL. ILIKE vs tsvector? MVP, 1 developer." |
| "Should we add caching?" | "John, Winston — Redis cache for `/items` endpoint. 500 req/s expected. Worth the added complexity for MVP?" |

You can address a specific agent by name — they get priority, and 1–2 complementary agents are added automatically.

**When to use:**
- Architectural crossroads before `/bmad-create-architecture`
- Story review before handing to Amelia
- Stuck during implementation — consult without leaving context
- Impact assessment before `/bmad-correct-course`

**Party Mode vs /bmad-brainstorming:**

| | `/bmad-party-mode` | `/bmad-brainstorming` |
|---|---|---|
| **What it does** | Evaluates, critiques, debates | Generates ideas |
| **Voices** | 2–3 agents from different roles | One Claude |
| **Best for** | You have a specific artifact to review | The question is fully open, no artifact yet |
| **Output** | Critique + concrete recommendations | List of options / directions |

---

### Scenario 5 — Team Collaboration *(Discussion Only)*

**When to use:** Team of 2–5 developers working on the same project, each on their own story.

**How it works:**

Tech Lead runs the full planning cycle once:
```
/bmad-product-brief → /bmad-create-prd → /bmad-create-architecture
→ /bmad-create-epics-and-stories
```

Stories are distributed. Each developer runs their own independent session:
```
Dev A:                          Dev B:
claude .                        claude .
/bmad-agent-dev                 /bmad-agent-dev
DS → BMAD-2-search-filter.md    DS → BMAD-4-dashboard.md
CR                              CR
```

Shared state exists only through the file system (git). `project-context.md` is the same for everyone — it's the shared source of truth for conventions.

**What BMAD gives a team:**
- Every developer gets the same context — no "tribal knowledge" problem
- All stories use an identical structure — Amelia knows how to read any of them
- Dev Agent Record in each story = audit trail of what was done and why
- Each story is self-contained, minimal merge conflicts

**Key limitation:** There is no real-time synchronization between sessions. Shared state lives in files only. Coordination happens via git, not inside Claude.

See: **[BMAD_USE_CASES.md — Scenario 7](./BMAD_USE_CASES.md)**

---

### Scenario 6 — Greenfield (New Project from Scratch) *(Discussion Only)*

**When to use:** You only have an idea or a brief. Building a new project from scratch.

**How it works — full cycle:**

```
/bmad-product-brief
→ Analyst (Mary) asks clarifying questions
→ Generates project-brief.md
          ↓
/bmad-create-prd
→ PM (John) writes the full PRD
→ Epic structure, user stories, priorities, NFRs
→ Generates prd.md
          ↓
/bmad-create-architecture
→ Architect (Winston) designs tech stack, data models, API contracts
→ Generates architecture.md
          ↓
/bmad-create-ux-design  (optional)
→ UX Designer (Sally) designs user flows and component structure
          ↓
/bmad-create-epics-and-stories
→ Breaks epics into individual story files
          ↓
/bmad-agent-dev → DS  (repeat for each story)
→ Amelia implements story by story
→ CR after each story
→ All stories done → Sprint closed ✅
```

**Key insight:** Every agent reads all previous artifacts. The Architect knows the PRD constraints. Amelia knows the architecture patterns. Context is stored in files — not in conversation memory — so it survives session restarts.

**Time:** 2–4 hours for a small project, days for a full system.

See: **[BMAD_USE_CASES.md — Scenario 3](./BMAD_USE_CASES.md)**

---

## Scenario Comparison

| Scenario | Commands | Time | When |
|----------|----------|------|------|
| Quick Dev | 1 (`/bmad-quick-dev`) | 5–15 min | Bug fix, small isolated change |
| Brownfield | 4–5 | 30–60 min | Feature in existing project |
| Correct Course | 1 + Party Mode | 20–40 min | Requirements changed mid-sprint |
| Party Mode | 1 + discussion | 20–40 min | Need multiple expert perspectives |
| Team Collaboration | Full flow + parallel DS | Sprint (1–2 weeks) | Team of 2–5 devs |
| Greenfield | 8–10 | 2–4 hours | New project from scratch |

---

## Resources

- **[BMAD official repository](https://github.com/bmad-code-org/BMAD-METHOD)**
- **[fastapi/full-stack-fastapi-template](https://github.com/fastapi/full-stack-fastapi-template)**
- **[BMAD_USE_CASES.md](./BMAD_USE_CASES.md)** — all scenarios in detail
- **[BMAD_WORKFLOW_GUIDE.md](./BMAD_WORKFLOW_GUIDE.md)** — step-by-step workflow

---

*BMAD v6.3.0 · FastAPI · React · TypeScript · PostgreSQL · Docker*

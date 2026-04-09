# BMAD Workflow Guide
## From Installation to Implementing a Task (BMAD-2)

> This guide demonstrates the full BMAD workflow using a real project.
> **Project:** `fastapi/full-stack-fastapi-template`
> **Task:** BMAD-2 — Search and Filtering for Items (3 SP)

---

## Part 1 — Environment Setup

### Step 1.1 — Start the Project

```bash
cd full-stack-fastapi-template
docker compose up -d --build
```

Verify everything is running:
- Frontend: http://localhost:5173
- Backend API: http://localhost:8000/docs

### Step 1.2 — Install BMAD

```bash
npx bmad-method install
```

> ⚠️ **Important:** When selecting tools and modules, press **Space** to select, then **Enter** to confirm.

Select:
- Tool: **claude-code**
- Modules: **BMad Method (bmm)**

After installation, a `_bmad/` folder will appear at the project root.

### Step 1.3 — Open the Project in Claude Code

```bash
claude .
```

---

## Part 2 — Initializing the Project in BMAD

### Step 2.1 — Generate Project Context

The first and most important command for an **existing** project:

```
/bmad-generate-project-context
```

**What happens:**
BMAD scans the codebase and generates `_bmad-output/project-context.md` — a compact file containing project rules for all agents. For example, it discovers that:
- The project uses **psycopg3**, not psycopg2
- There is no `tailwind.config.js` (Tailwind v4)
- Client code in `src/client/` is **auto-generated** — never edit manually
- Mypy is in strict mode — all code must pass type-checking

> 💡 This file is loaded by every agent on activation. Without it, agents don't know the project specifics and will make typical mistakes.

**Output:** `_bmad-output/project-context.md`

---

### Step 2.2 — Run Sprint Planning

```
/bmad-sprint-planning
```

Pass BMAD the list of tasks from the backlog. You can copy from `TASKS_JIRA.md` or simply write:

```
Here is our backlog:
- BMAD-2: Search and filtering for Items (High priority, 3 SP)
- BMAD-6: CSV Export (Low priority, 3 SP)
- BMAD-4: Dashboard with statistics (Medium priority, 5 SP)

Let's plan sprint 1 with BMAD-2 only.
```

**What happens:**
BMAD creates a sprint file at `_bmad-output/implementation-artifacts/sprint-plan.md` with the status of each story.

**Output:** `_bmad-output/implementation-artifacts/sprint-plan.md`

---

## Part 3 — Implementing Task BMAD-2

### Step 3.1 — Create the Story File

```
/bmad-create-story BMAD-2
```

Or in more detail:
```
Create story for BMAD-2: Search and filtering for Items.

Backend:
- Add query param `search` to GET /api/v1/items/ — ILIKE on title and description
- Add query param `sort_by` (created_at_asc, created_at_desc, title_asc, title_desc)
- Update count query to respect search filter

Frontend:
- Add Search input with debounce 300ms above items table
- Add sort dropdown (Newest / Oldest / A→Z / Z→A)
- Show "Found N items" counter
```

**What happens:**
BMAD prepares a detailed story file with all the context the developer agent needs: task description, acceptance criteria, files to change, and technical notes from project-context.md.

**Output:** `_bmad-output/implementation-artifacts/BMAD-2-search-filter.md`

---

### Step 3.2 — Hand the Task to the Dev Agent (Amelia)

Option A — via the agent:
```
/bmad-agent-dev
```

After activation, Amelia will say: *"Hi Vadym! I'm Amelia, your senior software engineer. Ready to execute stories."*

Then:
```
DS
```
(Dev Story — shortcode from the agent's capability menu)

Or directly:
```
dev this story _bmad-output/implementation-artifacts/BMAD-2-search-filter.md
```

Option B — directly:
```
/bmad-dev-story _bmad-output/implementation-artifacts/BMAD-2-search-filter.md
```

**What Amelia does (autonomously, without stopping):**

**Backend:**
1. Reads the entire story file before starting
2. Modifies `backend/app/api/routes/items.py`:
   ```python
   @router.get("/", response_model=ItemsPublic)
   def read_items(
       session: SessionDep,
       current_user: CurrentUser,
       skip: int = 0,
       limit: int = 100,
       search: str | None = None,        # ← new
       sort_by: str | None = None,       # ← new
   ) -> Any:
   ```
3. Adds ILIKE filtering and sorting to the SQL query
4. Updates the count query
5. Runs tests: `pytest backend/app/tests/api/routes/test_items.py`
6. Writes new tests for search/sort parameters
7. Marks backend subtasks as `[x]`

**Frontend:**
1. Modifies `frontend/src/routes/_layout/items.tsx`:
   - Adds `useState` for search and sortBy
   - Implements debounce via `useDeferredValue`
   - Passes parameters to `ItemsService.readItems()`
2. Adds Search input with icon (Lucide `Search`)
3. Adds `Select` component for sorting
4. Adds "Found N items" line
5. Regenerates the client: `npm run generate-client`
6. Marks frontend subtasks as `[x]`

> ⚠️ **Amelia's critical rule:** she never marks `[x]` if tests are not passing. She does not lie about tests.

---

### Step 3.3 — Code Review

After Amelia finishes the implementation:

```
CR
```

Or:
```
/bmad-code-review _bmad-output/implementation-artifacts/BMAD-2-search-filter.md
```

**What happens:**
BMAD checks the code against:
- Acceptance criteria from the story
- Type safety (mypy strict)
- Tests (whether they exist and pass)
- Project patterns (from project-context.md)
- Edge cases

If issues are found → returns to `DS` (Dev Story) for fixes.
If everything is OK → story gets the status **Done**.

---

### Step 3.4 — Update Sprint Status

```
/bmad-sprint-status
```

BMAD summarizes the sprint state: how many stories are Done, In Progress, Not Started.

---

## Full Flow at a Glance

```
[Claude Code open in the project folder]
         │
         ▼
/bmad-generate-project-context
→ Scans codebase
→ Saves rules to _bmad-output/project-context.md
         │
         ▼
/bmad-sprint-planning
→ Accepts backlog
→ Creates sprint-plan.md
         │
         ▼
/bmad-create-story BMAD-2
→ Prepares story file with full context
→ _bmad-output/implementation-artifacts/BMAD-2.md
         │
         ▼
/bmad-agent-dev  →  DS  (or /bmad-dev-story)
→ Amelia reads the story
→ Writes backend (items.py) + tests
→ Writes frontend (items.tsx) + regenerates client
→ All tests pass
         │
         ▼
CR  (or /bmad-code-review)
→ Review against acceptance criteria
→ If issues found → DS again
→ If OK → Story: Done ✅
         │
         ▼
/bmad-sprint-status
→ BMAD-2: Done ✅
→ Next story: BMAD-6
```

---

## Useful Commands During the Process

| Command | When to use |
|---------|-------------|
| `bmad-help` | Confused — ask what to do next |
| `/bmad-correct-course` | Requirements changed during the sprint |
| `/bmad-party-mode` | Want to discuss a task with multiple agents (PM + Architect + Dev) |
| `/bmad-review-adversarial-general` | Want a critical review of a document or code |
| `/bmad-sprint-status` | Check current sprint state |

---

## What Gets Saved After the Session

```
_bmad-output/
├── project-context.md                  ← project rules for agents
├── planning-artifacts/
│   └── sprint-plan.md                  ← sprint plan
└── implementation-artifacts/
    ├── BMAD-2-search-filter.md         ← story file with Dev Agent Record
    └── sprint-status.md                ← status of all stories
```

The story file after completion contains a **Dev Agent Record** — exactly what was done, which files were changed, and what decisions were made. This is the agent's audit trail.

---

## For Students — How to Get Started

1. `docker compose up -d` — start the project
2. `npx bmad-method install` — install BMAD (Space to select!)
3. `claude .` — open in Claude Code
4. `/bmad-generate-project-context` — initialize
5. Pick a task from `TASKS_JIRA.md`
6. `/bmad-sprint-planning` → `/bmad-create-story` → `/bmad-agent-dev` → `DS` → `CR`
7. `git commit` — save the result

---

*Stack: FastAPI · React · TypeScript · PostgreSQL · Docker*
*BMAD version: 6.2.3*

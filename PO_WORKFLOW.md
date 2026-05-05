# PO Workflow — From Client Request to Dev-Ready Artifacts

> **For:** Product Owner working on an existing project.
> **Trigger:** A client (stakeholder, customer, sales lead, exec) hands you a problem or a request.
> **Goal:** Produce clean, dev-ready artifacts (stories, optional PRD updates) without involving yourself in implementation.
> **Verified against:** BMAD v6.2.2.

---

## Road Map

```
Client request (Slack / email / call)
         ↓
   1. Capture verbatim
         ↓
   2. Clarify with the client (6 questions)
         ↓
   3. Triage — what size is this?
         ┌────────────────┬─────────────────┐
         ↓                ↓                 ↓
    STORY-SIZED      EPIC-SIZED       MID-SPRINT
    (clear AC,       (new capability,  CHANGE
    fits existing    needs PRD work)   (scope shift
    epic)                              after work
                                       started)
         ↓                ↓                 ↓
   skip to step 5    4. PRD work       use correct-course
                     (edit or create)  (separate flow)
                        ↓
   5. Create stories (with Tech Lead)
         ↓
   6. Validate & confirm with client
         ↓
   ─── HANDOFF TO DEV TEAM ───
```

The whole flow is the PO's job. Only step 5 (story creation) needs the Tech Lead in the room — for technical notes and file paths. Everything else is PO-driven.

---

## Do I Need Access to the Source Code?

**No — the PO works with artifacts, not source.** BMAD is file-based: everything the PO touches lives in `_bmad-output/` as `.md` / `.yaml`. A remote PO with no clone of the backend / frontend can run this entire flow.

### What the PO needs read/write access to

```
_bmad-output/
├── inbox/                              ← create here (step 1)
├── project-context.md                  ← read only
├── planning-artifacts/
│   ├── prd.md                          ← edit here (step 4)
│   ├── architecture.md                 ← read only
│   └── epic-*.md                       ← edit here (step 4.4)
└── implementation-artifacts/
    ├── sprint-status.yaml              ← updated via /bmad-sprint-planning
    └── {id}-{slug}.md                  ← created via /bmad-create-story
```

Typically synced via git, a shared drive, or a project-knowledge service (Notion, Confluence with the .md exported back).

### What the PO does NOT need

- Read access to `backend/app/...` or `frontend/src/...`
- Ability to run `docker compose` or `npm install`
- Ability to read or run tests
- A local development environment of any kind

### What the PO depends on the Tech Lead for

PO-only artifacts (steps 1–4) work fine without any TL input. The dependencies kick in at step 4.3 (architecture impact) and step 5 (story creation).

| Artifact | Who creates / maintains it | What it blocks if missing |
|---|---|---|
| `project-context.md` | 🏗️ Tech Lead, via `/bmad-generate-project-context` | **Doesn't block PRD work** *(steps 1–4 are fine without it)*. Affects only step 5 — without it, Bob generates stories with generic tech notes and TL ends up adding conventions story-by-story. One-shot generation by TL (~5–10 min) saves repeated work. |
| `architecture.md` | 🏗️ Tech Lead, via `/bmad-create-architecture` or updates | Step 4.3 (architecture impact assessment for epic-sized work). Without it, TL has to reason from the codebase directly — slower but possible. |
| Technical notes in story files | 🏗️ Tech Lead, during step 5 | The PO drafts user-value framing and AC; TL adds file paths, technical notes, dependency callouts. PO can produce a story without TL — but it'll come back during DS for clarification. |

### What does NOT block the PO

- 📋 **PRD create / edit / validate** — pure product work. Doesn't read source, doesn't read `project-context.md`, doesn't read `architecture.md`. PO can run steps 1–4.2 fully solo, even on a fresh project where no other artifacts exist yet.
- 📋 **Sprint planning** — works off `epic-*.md` files. No source needed.
- 📋 **Correct course** — works off existing artifacts (PRD, epics, stories). No source needed.

### Practical setup for a source-less PO

- Tech Lead runs `/bmad-generate-project-context` once per project (or after major refactors).
- Tech Lead commits `_bmad-output/` to git.
- PO clones only `_bmad-output/` (or pulls it from a shared workspace) and runs BMAD skills against that folder.
- PO and TL pair via screen-share / chat for steps 4.3 and 5.

> 💡 **Limit:** without source, the PO can't independently sanity-check whether a Tech Lead's claim about the codebase is accurate. They have to trust the artifacts. If you suspect drift between `project-context.md` and reality — ask TL to refresh it.

---

## Step 1 — Capture the Request Verbatim

Before anything else, save the raw client message untouched:

```
_bmad-output/inbox/{date}-{client}-{topic}.md
```

Include source (email / Slack / call notes), date, raw quote, requestor name. Don't paraphrase. You'll need the original later when scope discussions get heated.

> 💡 Even one-line asks belong here. *"Can we add CSV export?"* looks trivial until you discover the client meant *"export the dashboard with charts inline."*

---

## Step 2 — Clarify with the Client

Before writing any artifact, run a structured intake conversation. The goal is to surface what the client *actually* needs — usually different from what they asked for.

**Six questions every PO must answer:**

| # | Question | Why it matters |
|---|---|---|
| 1 | **Who is the user?** (not the requestor — the end user) | "I want X" usually means "my team / customer / boss wants X". Get to the real user. |
| 2 | **What problem does this solve?** | "Add a button" is not a problem. "Users can't export reports for finance review" is. |
| 3 | **What does success look like?** | Without measurable success criteria, AC will be vague. |
| 4 | **What's the urgency — and why now?** | Forces the client to defend priority vs. existing backlog. |
| 5 | **What's explicitly out of scope?** | Catches scope creep before it starts. |
| 6 | **What constraints exist?** | Compliance, deadlines, integrations, brand rules — bound the design space. |

**Need help structuring the conversation?** Use Mary (📊 Business Analyst):

```
/bmad-agent-analyst
"Client sent this request: [paste raw text from step 1].
 Help me run discovery on it before I write a story."
```

Mary walks you through the questions and any follow-ups. Output: a structured intake doc you'll use as input to the next step.

**Stuck on what to ask?**

```
/bmad-brainstorming
"What questions should I ask the client about [request]?"
```

---

## Step 3 — Triage

Once you have the answers from step 2, decide where the request lands. **This is the most important decision in the whole flow** — wrong size → wrong artifact → wasted dev time.

### Decision tree

```
Is the request a discrete user-visible feature with clear AC after step 2?
├─ YES → STORY-SIZED       → go to step 5
└─ NO  → Is it a high-level new capability (a whole feature area)?
         ├─ YES → EPIC-SIZED       → go to step 4
         └─ NO  → Is it a change to work already in the current sprint?
                  ├─ YES → MID-SPRINT CHANGE → use /bmad-correct-course
                                                (see "Mid-Sprint Changes" below)
                  └─ NO  → Re-run step 2 — the request is still vague.
```

### Sizing examples on this project

| Client request | Size | Why |
|---|---|---|
| "Add CSV export to the items list" | **Story** | One feature, clear AC, fits existing items epic |
| "Make the search faster" | **Story** *(after step 2 turns it into measurable AC)* | Performance is a story if you have a target; epic if you have a whole rework |
| "Add a reporting module with dashboards, exports, and scheduled emails" | **Epic** | Multi-feature capability, needs PRD section + several stories |
| "Support multi-tenancy across the app" | **Epic** | Cross-cutting, architectural, needs PRD work + Tech Lead |
| "Change BMAD-4 to also include `total_revenue`" | **Mid-sprint** | Story is in flight, scope shift |

> ⚠️ **When in doubt, escalate one level up.** Treating an epic as a single story produces a 50-AC monstrosity Amelia cannot reason about.

### Quick sanity-check before committing

If unsure about impact on existing plan:

```
/bmad-party-mode
"John, Winston — client asked for [request].
 Does this conflict with anything in the current PRD or architecture?"
```

10 minutes here saves a story rewrite later. John spots product collisions; Winston spots technical conflicts. *(This is a consultation, not a decision-maker — you still own the call.)*

---

## Step 4 — PRD Work *(Epic-Sized only)*

> Skip this step if the request is story-sized. Go straight to step 5.

If the request is a new high-level capability, the PRD needs to capture it before stories can be broken down.

### 4.1 — Decide: edit or create?

| State | Action | Skill |
|---|---|---|
| **PRD exists**, request fits as a new section | Add the new requirement to PRD | `/bmad-edit-prd` |
| **PRD exists**, but it's outdated / has gaps | Rewrite affected sections | `/bmad-edit-prd` |
| **No PRD at all** *(legacy project)* | Create a PRD documenting current behaviour + new requirement | `/bmad-create-prd` |

**Output:** `_bmad-output/planning-artifacts/prd.md`

### 4.2 — Validate the PRD

```
/bmad-validate-prd
```

Catches: missing sections, vague requirements, untestable AC, scope creep. Returns a checklist of fixes by priority.

**If issues found** → `/bmad-edit-prd` → re-validate. Don't skip this — a PRD with holes propagates them into every story.

### 4.3 — Architecture impact *(hand off to Tech Lead)*

> 🏗️ **Owner: Tech Lead.** A new epic-sized capability often needs architectural changes the PO can't see.

Hand the validated PRD to your Tech Lead with one question: *"Does this fit the current architecture? If not, what changes?"*

Tech Lead's options:
- Update `architecture.md` (or create one if missing) via `/bmad-create-architecture`
- Run adversarial reviews on the new architecture sections
- Surface trade-offs that affect product timeline / scope back to you

The PO doesn't run these commands. The PO listens for trade-offs that change the product story (e.g. *"this requires a 2-week rewrite"* or *"we can't deliver real-time with current stack"*) and decides scope/priority.

### 4.4 — Break the epic into stories

```
/bmad-create-epics-and-stories
```

Bob (🏃 SM) decomposes the new PRD section into an epic + stories. PO writes user-value framing and AC. Tech Lead adds file paths and technical notes.

**Output:** `_bmad-output/planning-artifacts/epic-{n}-{slug}.md`

> 💡 At this point you have epic + draft stories. Step 5 turns each draft story into a dev-ready file.

---

## Step 5 — Create Stories

> 🤝 **Joint with Tech Lead.** PO writes the user-value framing and AC. Tech Lead reviews technical notes and file paths.

Whether the request was story-sized (skipped step 4) or epic-sized (came through step 4), this is where individual story files are produced.

### 5.1 — Run the skill

```
/bmad-create-story <id>
```

Bob produces a single story file with **everything** Amelia needs: full context, AC, files to touch, technical notes. He pulls automatically from `prd.md`, `architecture.md`, `project-context.md` (if any exist).

You provide:
- The intake doc from step 2 (paste it when prompted)
- The epic ID to attach to *(if epic-sized)*
- Your priority (Must / Should / Could / Won't)

**Output:** `_bmad-output/implementation-artifacts/{id}-{slug}.md`

### 5.2 — Story-format checklist

Before signing off, confirm each story has:

- ✅ **Title** — verb-led, user-centric. NOT *"CSV export feature"*. YES *"Export filtered items list to CSV"*.
- ✅ **User-story line** — `As a {role}, I want {action}, so that {outcome}`.
- ✅ **AC** — Given / When / Then or numbered list. Each one independently testable.
- ✅ **Out-of-scope** — explicit list of what this story does NOT do.
- ✅ **Priority** — Must / Should / Could / Won't (MoSCoW).
- ✅ **Source** — link back to the inbox file from step 1. Audit trail.

`bmad-create-story` produces this format automatically when given the intake doc as input.

### 5.3 — Validate the story

```
VS    # Validate Story (from /bmad-create-story menu)
```

Catches: missing AC, vague tasks, file paths that don't exist, tasks Amelia can't action without clarification.

**If validation fails** → fix the story file, **don't** wave issues off. The dev team will hit them anyway, just at higher cost.

### 5.4 — Slot into the sprint

```
/bmad-sprint-planning
```

Bob updates `_bmad-output/implementation-artifacts/sprint-status.yaml` with the new story (or stories) at the right priority.

**Repeat 5.1–5.4** for every story spawned by the request (one for story-sized request, several if it was an epic).

---

## Step 6 — Confirm Back with the Client

Before the dev team starts — close the loop.

- ✅ Confirm with the client what you understood (paraphrase, not parrot)
- ✅ Confirm priority and target sprint
- ✅ State explicitly what is **out of scope** for this iteration
- ✅ State the AC in plain language (no Gherkin to clients)

Get explicit *"yes, that's what I meant"* before stories enter sprint scope. Misalignments caught here are 10x cheaper than misalignments caught at sprint review.

### Push back on the client when:

- AC are still vague after step 2 → *"I need a measurable success criterion before we can build this."*
- Request contradicts a locked architectural decision → *"This requires a redesign — let's evaluate cost first."*
- Priority is "urgent" but no business reason given → *"What breaks if we deliver next sprint instead?"*

A PO that never pushes back is a transcription service, not a Product Owner.

---

## Handoff Checklist

Before pinging the dev team:

- [ ] Story file(s) exist in `_bmad-output/implementation-artifacts/`
- [ ] Each story passed `VS` (Validate Story)
- [ ] Each story has full AC, file paths, no `TBD`s
- [ ] If epic-sized: PRD updated and validated
- [ ] `sprint-status.yaml` lists the new story/stories with status `not-started`
- [ ] Client confirmed back what they get and what they don't get
- [ ] Source quote linked from each story (inbox file)

When all are checked — the dev team can run `/bmad-agent-dev → DS` and you step out of the critical path.

---

## Mid-Sprint Changes

If the client comes back **after** stories are already in flight and asks for a scope change:

```
/bmad-correct-course
```

What it does: loads the current PRD, architecture, epics, and active stories. Generates a **Sprint Change Proposal** showing what changes where (forward propagation, not rollback). Returns Minor / Moderate / Major classification.

After the proposal is approved → re-run `/bmad-create-story <affected-id>` to regenerate stories with new scope, then re-confirm with the client (step 6 again).

> ⚠️ Halts if PRD or epics are missing — won't run on raw quick-dev work.

---

## Worked Example

**Client email (Tuesday morning):**

> *"Hey, can we add a feature where users can see their stuff better? Like a graph or something. And export it. Need it ASAP for the board meeting next week."*

### Step 1 — Capture

Save raw quote to `_bmad-output/inbox/2026-04-26-acme-dashboard.md`. Source: email. Requestor: Acme Inc CEO.

### Step 2 — Clarify *(20-minute call)*

| Question | Answer |
|---|---|
| Who is the user? | Acme's CFO and finance team — not the CEO who emailed |
| Problem? | Finance can't pull monthly numbers without IT help — costs ~6 hours per board meeting |
| Success? | Finance pulls a chart + CSV in <30 sec, no IT involvement, last 90 days |
| Urgency? | Board meets monthly — first occurrence in 8 days |
| Out of scope? | Drill-down, custom date ranges, multi-tenant comparison, real-time updates |
| Constraints? | Must export CSV (their accounting tool needs it), must include item count and sum |

### Step 3 — Triage

Run quick sanity-check:

```
/bmad-party-mode
"John, Winston — client wants a CFO-facing dashboard view + CSV export.
 Does this conflict with anything in current PRD or backlog?"
```

- John: *"This overlaps with story BMAD-4 (Dashboard) already in backlog — same chart, different audience."*
- Winston: *"Existing /api/v1/stats endpoint covers most of this — chart data already aggregated."*

→ **Decision: STORY-SIZED.** Not a new epic. Two existing stories (BMAD-4 dashboard, BMAD-6 CSV export) need scope additions.

→ Skip step 4. Go to step 5.

### Step 5 — Create Stories

Two updates, not new stories:

```
/bmad-create-story BMAD-4
   → add finance-team user persona, last 90 days view
/bmad-create-story BMAD-6
   → add item-count and sum columns to CSV
```

Run `VS` on both. Pass. Both bumped to current sprint via `/bmad-sprint-planning`.

### Step 6 — Confirm back

> *"To confirm: by next Monday your CFO will be able to view the existing dashboard (last 90 days, item count + sum chart) and export it as CSV with item count and sum columns. We're NOT adding drill-down or custom date ranges — those would come later. The work is two stories already in our backlog, now bumped to this sprint. Sound right?"*

CEO confirms. Stories enter sprint scope. **Total PO time: ~1 hour.**

### What didn't happen

- We didn't create a new epic for what turned out to be backlog tweaks.
- We didn't paste *"users see their stuff better"* into a story file.
- We didn't promise dashboard + CSV as a single feature when they're independent stories.
- The dev team got two stories with crisp AC, not a vague ask.

---

## What the PO Does NOT Do

For clarity — these belong to other roles, even though BMAD has skills for them:

| Skill | Owner | Why not PO |
|---|---|---|
| `/bmad-quick-dev` | Developer | Implementation, not pre-implementation |
| `/bmad-generate-project-context` | Tech Lead | Captures framework conventions — technical |
| `/bmad-create-architecture` | Tech Lead | Tech-stack decisions PO can't validate |
| `/bmad-technical-research` | Tech Lead | Library / pattern trade-offs |
| `/bmad-document-project` | Tech Lead | Module maps and data flow |
| `/bmad-review-edge-case-hunter` (on architecture) | Tech Lead | Boundary conditions in code paths |
| `/bmad-agent-dev` (Amelia) | Developer | Story execution |
| `/bmad-code-review` | Developer | Post-implementation review |
| All `/bmad-testarch-*` | QA / Tech Lead | Test architecture |

The PO can request these (*"Tech Lead, can you check architecture impact before I lock the PRD?"*) but doesn't run them.

---

## Quick Reference — PO's Toolbox

| When | Skill / Command |
|---|---|
| Need help structuring a client conversation | `/bmad-agent-analyst` (Mary) |
| Stuck on what to ask the client | `/bmad-brainstorming` |
| Quick consultation before triage | `/bmad-party-mode` |
| Add new requirement to PRD | `/bmad-edit-prd` |
| Create PRD for legacy project | `/bmad-create-prd` |
| Validate PRD | `/bmad-validate-prd` |
| Break epic into stories | `/bmad-create-epics-and-stories` *(joint with TL)* |
| Create individual story | `/bmad-create-story <id>` *(joint with TL)* |
| Validate a story | `VS` *(from create-story menu)* |
| Slot stories into sprint | `/bmad-sprint-planning` |
| Adversarial review of PRD | `/bmad-review-adversarial-general` |
| Mid-sprint scope change | `/bmad-correct-course` |
| Sprint status snapshot | `/bmad-sprint-status` |
| Help — what to do next | `/bmad-help` |

---

## Artifact Map

After a typical client request goes through this flow:

```
_bmad-output/
├── inbox/
│   └── 2026-04-26-acme-dashboard.md         ← step 1 raw capture
├── planning-artifacts/
│   ├── prd.md                               ← updated in step 4 (if epic-sized)
│   └── epic-{n}-{slug}.md                   ← created in step 4 (if epic-sized)
└── implementation-artifacts/
    ├── sprint-status.yaml                   ← updated in step 5.4
    └── BMAD-{id}-{slug}.md                  ← one per story from step 5
```

---

*BMAD v6.2.2 · Stack: FastAPI · React · TypeScript · PostgreSQL · Docker*
*Companion docs: `README.md` · `BMAD-METHOD-docs.md` · `BMAD_USE_CASES.md` · `BMAD_WORKFLOW_GUIDE.md`*
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            
# BMAD Method — Documentation

> **BMAD** (Breakthrough Method for Agile AI-Driven Development) is an open-source framework for structured AI-driven development. Rather than having AI "think for you", BMAD positions it as a disciplined participant in the agile process: each agent has a clear role, phase, and artifacts it produces.

- **GitHub:** https://github.com/bmad-code-org/BMAD-METHOD
- **Documentation:** https://docs.bmad-method.org
- **License:** MIT (free, no paywall)

---

## Table of Contents

1. [Installation (Claude example)](#installation-claude-example)
2. [Modules](#modules)
3. [Agents and Modes](#agents-and-modes)
4. [Workflow Phases](#workflow-phases)
5. [Party Mode](#party-mode)
6. [Useful Commands](#useful-commands)

---

## Installation (Claude example)

### Prerequisites

- **Node.js v20+**
- **Python 3.10+** and **uv** (added in v6.3.0)
- An active **Claude** subscription (Claude.ai or Claude Code)

Check your Node.js version:

```bash
node --version
```

If Node.js is not installed — download it from [nodejs.org](https://nodejs.org).

---

### Step 1 — Create a Project Folder

```bash
mkdir my-project
cd my-project
```

---

### Step 2 — Run the BMAD Installer

```bash
npx bmad-method install
```

The installer will launch an interactive menu. Below is what to select at each step.

---

### Step 3 — Choose the Installation Directory

```
? Where would you like to install BMad files?
❯ Current directory (recommended)
  Custom path
```

Select **Current directory** — this installs BMAD directly into your project.

---

### Step 4 — Select the AI Tool

```
? Select tools to configure:
❯ claude-code
  cursor
  windsurf
  ...
```

Select **`claude-code`**.

> This configures the `.claude/` folder with all necessary skills and configuration for Claude Code.

---

### Step 5 — Select a Module

```
? Select modules to install:
❯ ✔ BMad Method (bmm) — recommended
  BMad Builder (bmb)
  Test Architect (tea)
  ...
```

Start with **BMad Method (bmm)** — this is the core module with 34+ workflows.

> ⚠️ **Important:** Use **Space** to select a module, then **Enter** to confirm. Pressing Enter without Space will skip the selection.

---

### Step 6 — Fill in Project Settings

The installer will ask:

| Parameter | Description |
|-----------|-------------|
| `project_name` | Your project name |
| `user_skill_level` | Experience level: `beginner` / `intermediate` / `expert` |
| `planning_artifacts` | Folder for phase 1–3 artifacts (default: `docs/`) |
| `implementation_artifacts` | Folder for phase 4 artifacts (default: `stories/`) |
| `project_knowledge` | Folder for long-term documentation |

---

### Step 7 — Open the Project in Claude Code

After installation is complete, open the project folder in Claude Code:

```bash
claude .
```

Or open it via the Claude Code GUI (File → Open Folder).

---

### Step 8 — Verify Installation

In the Claude Code chat, type:

```
bmad-help
```

BMAD will automatically detect the installed modules and guide you to the next step.

---

### Non-Interactive Installation (CI/CD or automation)

```bash
npx bmad-method install \
  --directory /path/to/project \
  --modules bmm \
  --tools claude-code \
  --yes
```

---

### Installing a Prerelease Version

```bash
npx bmad-method@next install
```

---

## Modules

| Module | ID | Description |
|--------|----|----|
| **BMad Method** | `bmm` | Core module: 34+ workflows for the full development cycle |
| **BMad Builder** | `bmb` | Create custom agents and workflows |
| **Test Architect** | `tea` | Risk-based testing strategies |
| **Game Dev Studio** | `bmgd` | Workflows for Unity / Unreal / Godot |
| **Creative Intelligence Suite** | `cis` | Tools for innovation and design thinking |

---

## Agents and Modes

BMAD is organized around **specialized agents** — each is a distinct AI persona with a clear role, phase, and type of artifacts it produces. Agents don't replace each other: they pass context down the chain.

### Phase 1 — Analysis and Research

#### 🔍 Analyst
- **Agent name:** `bmad-agent-analyst`
- **Role:** Researcher and strategist at the project start
- **What it does:** Gathers and structures information about the problem, target audience, constraints, and risks. Produces the **Project Brief** — the input document for the entire process.
- **When to use:** At the beginning, when you only have an idea or task and need to turn it into a clear document
- **Artifacts:** Project Brief, PR-FAQ, Product Brief, Research Summary

#### ✍️ Tech Writer
- **Agent name:** `bmad-agent-tech-writer`
- **Role:** Technical writer
- **What it does:** Helps document the project, format technical decisions into readable content, and standardize documentation
- **When to use:** When you need to create or organize technical documentation at any stage

---

### Phase 2 — Planning

#### 📋 Product Manager (John)
- **Agent name:** `bmad-agent-pm`
- **Role:** Experienced product manager (8+ years, B2B and consumer)
- **What it does:** Transforms the Project Brief into a detailed **PRD (Product Requirements Document)**. Uses Jobs-to-be-Done and opportunity scoring. Constantly asks "WHY?" to find real user needs.
- **When to use:** After Analysis (Phase 1) is complete, when you need to formalize product requirements
- **Artifacts:** PRD with measurable NFRs, Epics and Stories

#### 🎨 UX Designer (Sally)
- **Agent name:** `bmad-agent-ux-designer`
- **Role:** Senior UX Designer (7+ years)
- **What it does:** Designs the user experience, describes user flows, creates UI/UX specifications. Tells user stories in a way that makes you "feel the problem".
- **When to use:** When UX is a key differentiator, there are complex user workflows, or a design system is needed
- **Artifacts:** UX Design Document, User Flows, Design System Spec

---

### Phase 3 — Architecture and Design

#### 🏗️ Architect (Winston)
- **Agent name:** `bmad-agent-architect`
- **Role:** System architect and technical designer
- **What it does:** Based on the PRD (and optionally the UX document), defines the tech stack, infrastructure, API design, and component architecture. Documents architectural decisions (ADR).
- **When to use:** After the PRD is complete, before implementation begins
- **Artifacts:** Architecture Document, ADR, Infrastructure Spec, API Contracts

---

### Phase 4 — Implementation

#### 💻 Developer (Amelia)
- **Agent name:** `bmad-agent-dev`
- **Role:** Unified implementation specialist — developer, sprint manager, QA coordinator, and code reviewer
- **What it does:** In v6.3.0, Amelia absorbed the roles of Barry (Quick Flow), Quinn (QA), and Bob (Scrum Master). She now handles the full implementation lifecycle: sprint planning, story creation, quick-dev, story implementation, code review, and QA coordination.
- **When to use:** For all implementation work — from quick bug fixes to full sprint execution
- **Artifacts:** Code, Tests, PR, Story Files, Sprint Status, Implementation Notes

> ⚠️ **v6.3.0 change:** The agents `bmad-agent-quick-flow-solo-dev` (Barry), `bmad-agent-qa` (Quinn), and `bmad-agent-sm` (Bob) have been **removed**. All their capabilities are now consolidated into Amelia. Workflows that previously required separate agents (`bmad-quick-dev`, `bmad-sprint-planning`, `bmad-create-story`) are still available — they are invoked directly or through Amelia's menu.

---

### Phase 4 — Implementation Commands (detailed)

Amelia's menu provides access to all implementation workflows. You can also call any workflow directly without going through the agent:

| Command | Type | What it does |
|---------|------|--------------|
| `bmad-agent-dev` | **Agent (Amelia)** | Opens an interactive session — Amelia greets you, loads project context, presents a capabilities menu |
| `bmad-dev-story` | Workflow | Implement a specific story file — read tasks in order, write code + tests |
| `bmad-quick-dev` | Workflow | Quick flow — clarify intent → plan (if needed) → implement → review → present |
| `bmad-create-story` | Workflow | Generate a detailed story file with context and acceptance criteria |
| `bmad-sprint-planning` | Workflow | Create `sprint-status.yaml` from epics |
| `bmad-code-review` | Workflow | Parallel code review: blind review + edge case + acceptance audit |
| `bmad-correct-course` | Workflow | Handle mid-sprint requirement changes — impact analysis + change proposal |
| `bmad-sprint-status` | Workflow | Check current sprint progress and surface risks |
| `bmad-retrospective` | Workflow | Post-epic review to extract lessons learned |
| `bmad-checkpoint-preview` | Workflow | *(New in 6.3.0)* Guided concern-ordered review of commits/branches/PRs |

**Practical rule:**
- Interactive session with a menu → use `bmad-agent-dev` (Amelia)
- Know exactly what you want → call the workflow directly (e.g. `/bmad-quick-dev`, `/bmad-dev-story`)
- Each workflow works as a standalone skill — no agent persona required

---

## Workflow Phases

```
Idea
 │
 ▼
[Phase 1] ANALYSIS ──────────── Analyst, Tech Writer
 │         Project Brief, PR-FAQ, Research
 │
 ▼
[Phase 2] PLANNING ──────────── PM (John), UX Designer (Sally)
 │         PRD, UX Design, Epics
 │
 ▼
[Phase 3] ARCHITECTURE ──────── Architect (Winston)
 │         Architecture Doc, ADR, API Contracts
 │
 ▼
[Phase 4] IMPLEMENTATION ────── Dev (Amelia)
           Sprint Planning → Stories → Code → Tests → Review
```

Each phase produces **artifacts** that become context for the next:
- The PRD tells the architect which constraints matter
- The Architecture Doc tells the developer which patterns to use
- Story Files give the developer a clear, complete context for implementation

---

## Party Mode

**Party Mode** is a mode that allows **multiple agents in a single session** for collaborative discussion.

### Usage Examples

- **Architect + PM** discuss technical trade-offs and requirements simultaneously
- **Developer + Architect** align on implementation approach before coding
- **PM + UX Designer** validate requirements and design decisions together

### How to Activate

In the Claude Code chat, explicitly address multiple agents:

```
/bmad-party-mode

We need to discuss microservices architecture.
PM — describe the scalability requirements.
Architect — propose a solution.
```

---

## Useful Commands

| Command | Description |
|---------|-------------|
| `bmad-help` | Get contextual guidance — what to do next |
| `bmad-help I just finished the architecture, what's next?` | Ask for specific advice |
| `/bmad-agent-pm` | Activate PM agent (John) |
| `/bmad-agent-architect` | Activate Architect agent (Winston) |
| `/bmad-agent-dev` | Activate Developer agent (Amelia) — unified implementation: stories, quick-dev, QA, sprint management |
| `/bmad-dev-story` | Implement a specific story file directly |
| `/bmad-quick-dev` | Quick implementation — from raw intent to code |
| `/bmad-create-story` | Generate a detailed story file |
| `/bmad-sprint-planning` | Create sprint status tracker from epics |
| `/bmad-code-review` | Parallel code review (blind + edge case + acceptance) |
| `/bmad-correct-course` | Handle mid-sprint requirement changes |
| `/bmad-checkpoint-preview` | *(New)* Guided review of commits/branches/PRs |
| `/bmad-agent-analyst` | Activate Analyst agent |
| `/bmad-party-mode` | Activate multi-agent discussion mode |

---

## Scale-Adaptive Intelligence

BMAD automatically calibrates the **depth of planning** based on project complexity:

- **Small project / bug fix** → minimal process, Quick Flow
- **Medium project** → full cycle with simplified artifacts
- **Enterprise system** → full cycle with detailed documentation at each phase

---

*Documentation based on BMAD METHOD v6.3.0. Latest information: [docs.bmad-method.org](https://docs.bmad-method.org)*

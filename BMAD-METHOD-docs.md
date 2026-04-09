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

- **Node.js v20+** — the only required dependency
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
- **Role:** Implementation specialist and code reviewer
- **What it does:** Implements stories, writes code according to architectural decisions, conducts code reviews. Works strictly within the defined story context.
- **When to use:** For writing code, story by story
- **Artifacts:** Code, PR, Implementation Notes

#### 🧪 QA (Quinn)
- **Agent name:** `bmad-agent-qa`
- **Role:** Pragmatic test automation engineer
- **What it does:** Generates E2E tests, covers realistic user scenarios. "Ship it and iterate" mindset — focused on fast coverage.
- **When to use:** For writing tests, QA strategy, E2E coverage
- **Artifacts:** E2E Test Suite, QA Plan

#### 📌 Scrum Master (Bob)
- **Agent name:** `bmad-agent-sm`
- **Role:** Certified Scrum Master with a technical background
- **What it does:** Organizes team work: sprint planning, preparing story files with context and acceptance criteria, tracking progress. Clear, checklist-oriented, zero tolerance for ambiguity.
- **When to use:** For sprint planning, story preparation, retrospectives
- **Artifacts:** Story Files, Sprint Plan, Sprint Status, Retrospective Notes

#### ⚡ Quick Flow Solo Dev (Barry)
- **Agent name:** `bmad-agent-quick-flow-solo-dev`
- **Role:** Accelerated mode for a solo developer
- **What it does:** A simplified variant without the full agile process. Allows quickly moving from idea to code without going through all phases.
- **When to use:** Small projects, MVPs, solo dev without the need for full documentation

---

### Phase 4 — Implementation Skills (detailed)

There are 4 closely related commands in Phase 4 that are easy to confuse:

| Command | Type | Input | Use when |
|---------|------|-------|----------|
| `bmad-agent-dev` | **Agent (Amelia)** | Story file already exists | Standard/Enterprise flow — opens a session with Amelia, she presents a menu |
| `bmad-dev-story` | **Workflow** | Story file path | Direct story execution without the agent persona — same as Amelia's `DS` menu item |
| `bmad-agent-quick-flow-solo-dev` | **Agent (Barry)** | Raw intent ("add email filter") | Quick Flow — opens a session with Barry, he handles spec + implementation himself |
| `bmad-quick-dev` | **Workflow** | Raw intent | Direct quick dev without the agent persona — same as Barry's `QD` menu item |

**Relationship between agents and workflows:**

```
bmad-agent-dev (Amelia)
    └── menu item DS → bmad-dev-story  ← requires a prepared story file

bmad-agent-quick-flow-solo-dev (Barry)
    └── menu item QD → bmad-quick-dev  ← works from raw intent alone
```

**Practical rule:**
- Already have a story file → use `bmad-agent-dev` or `bmad-dev-story` directly
- No story file, want a quick result → use `bmad-agent-quick-flow-solo-dev` or `bmad-quick-dev` directly
- The agent variants (Amelia/Barry) greet you, load project context, and offer a menu — useful for interactive sessions
- The workflow variants (`bmad-dev-story` / `bmad-quick-dev`) go straight to execution — useful when you know exactly what you want

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
[Phase 4] IMPLEMENTATION ────── Dev (Amelia), QA (Quinn), SM (Bob)
           Stories → Code → Tests → Sprint Review
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
- **Developer + QA** align on a testing strategy before implementation
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
| `/bmad-agent-dev` | Activate Developer agent (Amelia) — interactive session, story-by-story |
| `/bmad-dev-story` | Execute a specific story file directly (no agent persona) |
| `/bmad-agent-quick-flow-solo-dev` | Activate Quick Flow agent (Barry) — interactive session, raw intent → code |
| `/bmad-quick-dev` | Quick implementation directly (no agent persona) |
| `/bmad-agent-sm` | Activate Scrum Master agent (Bob) |
| `/bmad-agent-qa` | Activate QA agent (Quinn) |
| `/bmad-agent-analyst` | Activate Analyst agent |
| `/bmad-party-mode` | Activate multi-agent discussion mode |

---

## Scale-Adaptive Intelligence

BMAD automatically calibrates the **depth of planning** based on project complexity:

- **Small project / bug fix** → minimal process, Quick Flow
- **Medium project** → full cycle with simplified artifacts
- **Enterprise system** → full cycle with detailed documentation at each phase

---

*Documentation based on BMAD METHOD v6.2.3. Latest information: [docs.bmad-method.org](https://docs.bmad-method.org)*

# Workflow Execution Plan: project-setup

## 1. Overview

| Field | Value |
|---|---|
| **Workflow Name** | `project-setup` |
| **Workflow File** | `ai_instruction_modules/ai-workflow-assignments/dynamic-workflows/project-setup.md` |
| **Project Name** | workflow-orchestration-queue (OS-APOW — Opencode-Server Agent Workflow Orchestration) |
| **Repository** | `intel-agency/workflow-orchestration-queue-charlie73` |
| **Total Main Assignments** | 6 |
| **Event Assignments** | 3 (create-workflow-plan, validate-assignment-completion, report-progress) |
| **Working Branch** | `dynamic-workflow-project-setup` |

**High-Level Summary:** This workflow transforms a freshly cloned template repository into a fully initialized, planned, and scaffolded project. It begins with repository initialization (branch protection, labels, GitHub Project), proceeds through application planning (analysis of plan_docs, milestone creation, tech-stack documentation), creates the actual project structure (Python packages, Dockerfiles, CI/CD), generates an AGENTS.md file for AI agent consumption, performs a comprehensive debrief, and finally merges everything via a PR.

---

## 2. Project Context Summary

### Key Facts from plan_docs

| Document | Key Facts |
|---|---|
| **OS-APOW Development Plan v4** | 4-phase roadmap: Phase 0 (Seeding), Phase 1 (Sentinel MVP), Phase 2 (Ear/Webhook), Phase 3 (Deep Orchestration). Motivated by "Zero-Touch Construction" — user opens a spec issue and receives a functional PR. Script-First Integration, State Visibility, Self-Bootstrapping, Polling Resiliency. |
| **OS-APOW Architecture Guide v3** | 4 pillars: The Ear (FastAPI notifier), The State (GitHub Issues as DB), The Brain (Sentinel polling orchestrator), The Hands (DevContainer worker). Shell-Bridge via `devcontainer-opencode.sh`. "Markdown as a Database" with label-based state machine. |
| **OS-APOW Implementation Specification v1** | Full app spec: Python 3.12+, FastAPI, Pydantic, HTTPX, uv. No .NET. Project structure under `src/` with `models/`, `interfaces/` subdirectories. FastAPI webhook receiver with HMAC validation. Async polling sentinel. Docker/DevContainer isolation. 6 test cases defined. |
| **orchestrator_sentinel.py** | Reference implementation: `WorkItem` model, `GitHubQueue` class, `Sentinel` class with polling loop. Uses `httpx` async client. Shell bridge integration via `subprocess`. Structured logging with `SENTINEL_ID`. |
| **notifier_service.py** | Reference implementation: `ITaskQueue` ABC, `GitHubIssuesQueue` concrete class, FastAPI app with HMAC signature verification, `/webhooks/github` endpoint, `/health` endpoint. |
| **interactive-report.html** | React-based interactive dashboard for the architecture and development plan. |

### Technology Stack

- **Language:** Python 3.12+
- **Package Manager:** uv (Rust-based, replaces pip/poetry)
- **Web Framework:** FastAPI + Uvicorn
- **Data Validation:** Pydantic
- **HTTP Client:** HTTPX (async)
- **Containerization:** Docker + DevContainers
- **Scripting:** PowerShell Core (pwsh) / Bash for shell bridge
- **No .NET:** Project is purely Python/Shell; no `global.json` needed

### Repository Details

- **Repo Name:** `workflow-orchestration-queue-charlie73`
- **Owner:** `intel-agency`
- **Branch Strategy:** `main` (stable), `develop` (integration)
- **Working Branch for this workflow:** `dynamic-workflow-project-setup`

### Known Constraints

- Branch protection ruleset import requires `GH_ORCHESTRATION_AGENT_TOKEN` with `administration: write` scope (not the default `GITHUB_TOKEN`)
- No `ai-new-app-template.md` file exists in `plan_docs/` — the app spec is in `OS-APOW Implementation Specification v1.md`
- Reference implementations (`orchestrator_sentinel.py`, `notifier_service.py`) are skeletons with placeholder tokens, not production-ready code

---

## 3. Assignment Execution Plan

### Assignment 1: `create-workflow-plan` (pre-script-begin event)

| Field | Content |
|---|---|
| **Goal** | Create a comprehensive workflow execution plan before any assignments run |
| **Key Acceptance Criteria** | Dynamic workflow fully read; all assignments traced; all plan_docs read; structured plan produced; plan committed to `plan_docs/workflow-plan.md` |
| **Project-Specific Notes** | This is the current assignment. The project is Python-based (not .NET), so subsequent assignments must adapt accordingly. |
| **Prerequisites** | plan_docs/ directory exists; remote assignment files accessible |
| **Dependencies** | None (first assignment) |
| **Risks / Challenges** | None significant — purely a reading/planning task |
| **Events** | None |

---

### Assignment 2: `init-existing-repository`

| Field | Content |
|---|---|
| **Goal** | Initialize the repository: create working branch, import branch protection, create GitHub Project, import labels, rename files, create PR |
| **Key Acceptance Criteria** | (0) New branch `dynamic-workflow-project-setup` created; (1) Branch protection ruleset imported; (2) GitHub Project created with board columns (Not Started, In Progress, In Review, Done); (3) Project linked to repo; (4) Labels imported from `.github/.labels.json`; (5) Workspace/devcontainer files renamed to match repo name; (6) PR created from branch to main |
| **Project-Specific Notes** | The repo name is `workflow-orchestration-queue-charlie73`. Devcontainer name should become `workflow-orchestration-queue-charlie73-devcontainer`. Workspace file should become `workflow-orchestration-queue-charlie73.code-workspace`. Labels are in `.github/.labels.json`. Branch protection ruleset is in `.github/protected-branches_ruleset.json`. |
| **Prerequisites** | GitHub auth with scopes: `repo`, `project`, `read:project`, `read:user`, `user:email`; `administration: write` for ruleset import |
| **Dependencies** | None (first main assignment) |
| **Risks / Challenges** | **HIGH:** Branch protection import requires `GH_ORCHESTRATION_AGENT_TOKEN` (PAT with admin scope). If unavailable, step must be skipped with documented error. **MEDIUM:** GitHub Project creation may fail if `project` scope is missing from token. **LOW:** File rename targets must exist in template. |
| **Events** | post-assignment-complete → `validate-assignment-completion` + `report-progress` |

---

### Assignment 3: `create-app-plan`

| Field | Content |
|---|---|
| **Goal** | Analyze plan_docs and create a comprehensive application plan as a GitHub Issue, with milestones and tech-stack/architecture documentation |
| **Key Acceptance Criteria** | (1) App template analyzed; (2-6) Plan documented with phases, components, dependencies; (7-9) Tech stack, design principles, mandatory requirements addressed; (10) Risks/mitigations identified; (11-12) Quality standards followed; (13) Plan documented as GitHub Issue using template; (14-15) Milestones created and issue linked; (16) Issue assigned to milestone; (17) Labels applied |
| **Project-Specific Notes** | No `ai-new-app-template.md` exists in plan_docs — use `OS-APOW Implementation Specification v1.md` as the primary app spec. Supporting docs: Development Plan v4, Architecture Guide v3, reference Python files. The application plan template is at `.github/ISSUE_TEMPLATE/application-plan.md`. This is **planning only** — no code or project files are created. Key phases: Phase 0 (Seeding), Phase 1 (Sentinel MVP), Phase 2 (Ear/Webhook), Phase 3 (Deep Orchestration). Milestones should map to these phases. |
| **Prerequisites** | init-existing-repository completed (branch, labels, project, milestones support) |
| **Dependencies** | Branch `dynamic-workflow-project-setup` exists; labels imported; GitHub Project created |
| **Risks / Challenges** | **MEDIUM:** Appendix A template referenced in the assignment has been moved to `.github/ISSUE_TEMPLATE/application-plan.md` — agent must find it there. **LOW:** The app spec is split across multiple files (impl spec, dev plan, arch guide, Python skeletons) requiring synthesis. |
| **Events** | pre-assignment-begin → `gather-context`; post-assignment-complete → `validate-assignment-completion` + `report-progress`; on-assignment-failure → `recover-from-error` |

---

### Assignment 4: `create-project-structure`

| Field | Content |
|---|---|
| **Goal** | Create the actual project scaffolding: solution structure, Dockerfiles, docker-compose, CI/CD, docs, README, .ai-repository-summary.md |
| **Key Acceptance Criteria** | (1) Solution/project structure created per plan; (2) All project files and directories established; (3) Configuration files created; (4) Basic CI/CD pipeline; (5) Documentation structure; (6) Dev environment validated; (7) Initial commit; (8) Stakeholder approval; (9) Repository summary document created; (10) All GitHub Actions pinned to commit SHA |
| **Project-Specific Notes** | Tech stack is Python 3.12+ with uv. Structure should follow: `pyproject.toml`, `uv.lock`, `src/notifier_service.py`, `src/orchestrator_sentinel.py`, `src/models/`, `src/interfaces/`, `tests/`, `Dockerfile`, `docker-compose.yml`. **NOT .NET** — no `.sln`, `.csproj`, or `global.json`. Use `uv` for dependency management. Reference implementations exist in `plan_docs/orchestrator_sentinel.py` and `plan_docs/notifier_service.py` as starting points. CI/CD workflows should use Python-appropriate tooling (pytest, ruff/mypy). Repository summary at `.ai-repository-summary.md` following `create-repository-summary.md` instructions. |
| **Prerequisites** | create-app-plan completed (plan issue, milestones, tech-stack.md, architecture.md) |
| **Dependencies** | Application plan issue; tech-stack.md and architecture.md from create-app-plan |
| **Risks / Challenges** | **MEDIUM:** Adapting the assignment's .NET-oriented example structures to Python. **MEDIUM:** Ensuring Dockerfile has `COPY src/ ./src/` before `uv pip install -e .` (assignment explicitly warns about this). **MEDIUM:** Healthcheck in docker-compose must use Python stdlib, not curl. **LOW:** Ensuring all workflow actions are pinned by SHA. |
| **Events** | post-assignment-complete → `validate-assignment-completion` + `report-progress` |

---

### Assignment 5: `create-agents-md-file`

| Field | Content |
|---|---|
| **Goal** | Create an AGENTS.md file at the repository root providing AI coding agents with project context, build/test commands, and conventions |
| **Key Acceptance Criteria** | (1) AGENTS.md exists at repo root; (2-9) Contains: project overview, setup/build/test commands (verified), code style, project structure, testing instructions, PR/commit guidelines, architecture notes, common pitfalls; (10) Committed and pushed; (11) Stakeholder approval |
| **Project-Specific Notes** | Must validate that `uv` commands work (`uv sync`, `uv run pytest`, etc.). Must reference the Python project structure, FastAPI/Uvicorn setup, and Docker/DevContainer configuration. Should cross-reference README.md, .ai-repository-summary.md, and plan_docs/ without duplicating. |
| **Prerequisites** | init-existing-repository + create-app-plan + create-project-structure all completed |
| **Dependencies** | All project files from create-project-structure; application plan; repo summary |
| **Risks / Challenges** | **MEDIUM:** Build/test commands must be validated by actually running them — if the project structure isn't fully functional yet, some commands may fail. **LOW:** Balancing comprehensiveness with conciseness for agent consumption. |
| **Events** | post-assignment-complete → `validate-assignment-completion` + `report-progress` |

---

### Assignment 6: `debrief-and-document`

| Field | Content |
|---|---|
| **Goal** | Perform comprehensive debriefing capturing learnings, insights, deviations, and improvement recommendations from the entire project-setup workflow |
| **Key Acceptance Criteria** | (1) Report created following structured template (12 sections); (2) In .md format; (3-5) All sections complete with deviations documented; (6) Committed and pushed; (7) Execution trace saved as `debrief-and-document/trace.md` |
| **Project-Specific Notes** | Report must include all 12 sections: Executive Summary, Workflow Overview, Key Deliverables, Lessons Learned, What Worked Well, What Could Be Improved, Errors Encountered, Complex Steps, Suggested Changes, Metrics, Future Recommendations, Conclusion. The execution trace should capture all terminal commands run during the workflow. Plan-impacting findings must be flagged as ACTION ITEMS. |
| **Prerequisites** | All previous assignments completed |
| **Dependencies** | Outputs and results from all prior assignments |
| **Risks / Challenges** | **LOW:** Comprehensive capture requires accurate recording throughout the workflow. Agent should reference validate-assignment-completion reports and report-progress outputs for accurate data. |
| **Events** | post-assignment-complete → `validate-assignment-completion` + `report-progress` |

---

### Assignment 7: `pr-approval-and-merge`

| Field | Content |
|---|---|
| **Goal** | Complete the full PR approval and merge process: CI verification, code review delegation, comment resolution, approval, merge, and cleanup |
| **Key Acceptance Criteria** | CI verification (up to 3 remediation attempts); Code review delegated to independent reviewer (NOT self-review); PR comment protocol executed with GraphQL verification; Stakeholder approval; Merge performed; Post-merge hygiene (branch deletion, issue closure) |
| **Project-Specific Notes** | PR was created in `init-existing-repository` (assignment 2) from `dynamic-workflow-project-setup` to `main`. The `pr_num` input must be provided. Must follow `ai-pr-comment-protocol.md` before resolving comments. Must use `scripts/query.ps1` for PR thread management. |
| **Prerequisites** | All assignments completed; all commits pushed to branch |
| **Dependencies** | PR number from init-existing-repository; all code from create-project-structure and create-agents-md-file |
| **Risks / Challenges** | **HIGH:** CI may fail on first run if project structure isn't fully buildable/testable. 3 remediation attempts allowed. **MEDIUM:** Code review must be delegated to a separate agent (code-reviewer subagent). **MEDIUM:** GraphQL thread resolution requires careful artifact capture. **LOW:** Merge strategy (squash/rebase/merge) should follow repo convention. |
| **Events** | post-assignment-complete → `validate-assignment-completion` + `report-progress` |

---

### Event Assignment: `validate-assignment-completion`

| Field | Content |
|---|---|
| **Goal** | Validate that a completed assignment met all acceptance criteria; must be delegated to an independent QA agent |
| **When** | After each main assignment (post-assignment-complete event) |
| **Key Actions** | Identify completed assignment; read acceptance criteria; verify file outputs; run verification commands (build, test, lint); check GitHub state; create validation report; determine pass/fail |
| **Output** | `docs/validation/VALIDATION_REPORT_<assignment-name>_<timestamp>.md` |

---

### Event Assignment: `report-progress`

| Field | Content |
|---|---|
| **Goal** | Generate progress report, capture outputs, validate acceptance criteria, save checkpoint state, file action items as GitHub issues |
| **When** | After each main assignment (post-assignment-complete event) |
| **Key Actions** | Generate structured progress report; capture step outputs; validate acceptance criteria; save checkpoint state; file GitHub issues for any action items (mandatory) |
| **Output** | Progress report with step name, duration, outputs, deviations, plan-impacting discoveries |

---

## 4. Sequencing Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│  PRE-SCRIPT-BEGIN EVENT                                            │
│  ┌─────────────────────┐                                           │
│  │ create-workflow-plan │ ← THIS ASSIGNMENT                        │
│  └─────────┬───────────┘                                           │
└────────────┼────────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  ASSIGNMENT 1: init-existing-repository                            │
│  Creates branch, branch protection, GitHub Project, labels,        │
│  renames files, creates PR                                         │
└────────────┬───────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  POST-ASSIGNMENT-COMPLETE EVENT                                    │
│  ┌──────────────────────────────┐  ┌──────────────────┐            │
│  │ validate-assignment-completion│  │ report-progress   │            │
│  └──────────────────────────────┘  └──────────────────┘            │
└────────────┬────────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  ASSIGNMENT 2: create-app-plan                                     │
│  Analyzes plan_docs, creates plan issue, milestones,               │
│  tech-stack.md, architecture.md                                    │
│  (PLANNING ONLY — no code)                                         │
└────────────┬───────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  POST-ASSIGNMENT-COMPLETE EVENT (validate + report)                │
└────────────┬────────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  ASSIGNMENT 3: create-project-structure                            │
│  Creates Python project scaffolding, pyproject.toml, Dockerfiles,  │
│  CI/CD, docs, README, .ai-repository-summary.md                    │
└────────────┬───────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  POST-ASSIGNMENT-COMPLETE EVENT (validate + report)                │
└────────────┬────────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  ASSIGNMENT 4: create-agents-md-file                               │
│  Creates AGENTS.md with validated build/test commands              │
└────────────┬───────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  POST-ASSIGNMENT-COMPLETE EVENT (validate + report)                │
└────────────┬────────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  ASSIGNMENT 5: debrief-and-document                                │
│  Comprehensive debrief report, execution trace, lessons learned    │
└────────────┬───────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  POST-ASSIGNMENT-COMPLETE EVENT (validate + report)                │
└────────────┬───────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  ASSIGNMENT 6: pr-approval-and-merge                               │
│  CI verification, code review, comment resolution, merge, cleanup  │
└────────────┬───────────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────────────┐
│  POST-SCRIPT-COMPLETE EVENT                                        │
│  Apply `orchestration:plan-approved` label to plan issue           │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 5. Open Questions

1. **Branch Protection Token:** The `init-existing-repository` assignment requires `GH_ORCHESTRATION_AGENT_TOKEN` (a PAT with `administration: write` scope) to import the branch protection ruleset. Is this secret available in the repository? If not, the step will be skipped with a documented error, and the ruleset must be manually applied later.

2. **Missing App Template File:** The `create-app-plan` assignment references an app template file at `plan_docs/ai-new-app-template.md`, but this file does not exist in the current `plan_docs/` directory. The agent should use `OS-APOW Implementation Specification v1.md` as the primary app specification, supplemented by the Development Plan v4 and Architecture Guide v3.

3. **Application Plan Issue Template Location:** The `create-app-plan` assignment references Appendix A, which has been moved to `.github/ISSUE_TEMPLATE/application-plan.md`. The agent must locate and use this template instead of looking for an inline appendix.

4. **Python vs .NET Adaptation:** Several assignment definitions (especially `create-project-structure`) contain .NET-centric example structures. The executing agent must adapt these to the Python 3.12+ / uv / FastAPI stack defined in the Implementation Specification.

5. **Reference Implementation Handling:** The plan_docs contain skeleton reference implementations (`orchestrator_sentinel.py`, `notifier_service.py`) with placeholder tokens. The `create-project-structure` agent should use these as architectural guides but not copy them verbatim (they contain hardcoded secrets like `"YOUR_GITHUB_TOKEN"` and `"your_webhook_secret_here"`).

6. **GitHub Project V2 vs Classic:** The `init-existing-repository` assignment mentions creating a project with "Board template" and columns. With GitHub Projects V2, the API uses fields/views rather than classic columns. The agent should use the appropriate API for the project type available.

---

## Assignment Resolution Trace

| Assignment | Source | Summary |
|---|---|---|
| `create-workflow-plan` | Remote canonical URL | Plan creation before workflow execution begins |
| `init-existing-repository` | Remote canonical URL | Repository initialization: branch, protection, project, labels, file renames, PR |
| `create-app-plan` | Remote canonical URL | Analyze plan docs, create application plan issue, milestones, tech docs (no code) |
| `create-project-structure` | Remote canonical URL | Create actual project scaffolding, Dockerfiles, CI/CD, docs, repo summary |
| `create-agents-md-file` | Remote canonical URL | Create AGENTS.md with validated commands for AI agents |
| `debrief-and-document` | Remote canonical URL | Comprehensive debrief report with learnings, metrics, and recommendations |
| `pr-approval-and-merge` | Remote canonical URL | CI verification, code review delegation, comment resolution, merge, cleanup |
| `validate-assignment-completion` | Remote canonical URL | Independent QA validation of each assignment's acceptance criteria |
| `report-progress` | Remote canonical URL | Progress reporting, output capture, checkpoint state, action item filing |

## Plan Documents Read

| Document | Status | Key Takeaway |
|---|---|---|
| `OS-APOW Development Plan v4.md` | ✅ Read | 4-phase roadmap, guiding principles, risk assessment |
| `OS-APOW Architecture Guide v3.md` | ✅ Read | System architecture: Ear, State, Brain, Hands; ADRs; data flow |
| `OS-APOW Implementation Specification v1.md` | ✅ Read | Full app spec: Python 3.12+, FastAPI, project structure, requirements, test cases |
| `orchestrator_sentinel.py` | ✅ Read | Reference skeleton: WorkItem model, GitHubQueue, Sentinel polling loop |
| `notifier_service.py` | ✅ Read | Reference skeleton: ITaskQueue ABC, FastAPI webhook receiver, HMAC validation |
| `interactive-report.html` | ✅ Read | React-based interactive dashboard for architecture visualization |

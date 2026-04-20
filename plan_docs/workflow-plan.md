# Workflow Execution Plan: project-setup

> **Workflow:** `project-setup` (dynamic workflow for initiating a new repository)
> **Project:** OS-APOW (workflow-orchestration-queue)
> **Repository:** `intel-agency/workflow-orchestration-queue-charlie73`
> **Date:** 2026-04-20

---

## 1. Overview

### Workflow Name

`project-setup` — the dynamic workflow responsible for initializing a freshly cloned template repository, planning the application, scaffolding the project structure, creating the AGENTS.md file, debriefing, and merging the setup PR.

### Project Description

**OS-APOW (workflow-orchestration-queue)** is a headless agentic orchestration platform that transforms GitHub Issues into autonomous Execution Orders fulfilled by specialized AI agents running inside reproducible Docker DevContainers. The system shifts AI from a passive co-pilot to an autonomous background production service, using a "Markdown-as-a-Database" philosophy where GitHub Issues, Labels, and Comments serve as the distributed state machine.

### Total Assignments

**6 main assignments** + **3 event assignments** = **9 total assignments to trace.**

| # | Assignment ID | Type | Role |
|---|---|---|---|
| 0 | `create-workflow-plan` | Pre-script-begin event | Produce this plan before any work begins |
| 1 | `init-existing-repository` | Main | Set up repo: branch protection, project board, labels, rename files, create PR |
| 2 | `create-app-plan` | Main | Analyze plan_docs, produce application plan issue, milestones |
| 3 | `create-project-structure` | Main | Scaffold solution, Dockerfiles, CI/CD, docs, repository summary |
| 4 | `create-agents-md-file` | Main | Create AGENTS.md with validated build/test/lint commands |
| 5 | `debrief-and-document` | Main | Comprehensive debrief report with lessons learned, deviations, metrics |
| 6 | `pr-approval-and-merge` | Main | Resolve PR comments, secure approval, merge, close issues |
| — | `validate-assignment-completion` | Post-assignment-complete event | Independent QA validation after each main assignment |
| — | `report-progress` | Post-assignment-complete event | Progress reporting, output capture, checkpoint state |

**Post-script-complete event:** Apply `orchestration:plan-approved` label after all assignments finish.

### High-Level Summary

This workflow transforms the template repository into a fully initialized project by: (1) creating this execution plan, (2) establishing GitHub project infrastructure (labels, branch protection, project board), (3) analyzing the architecture and specification documents to create a detailed application plan, (4) scaffolding the Python project structure with all necessary configuration, (5) creating an AGENTS.md file for AI agent onboarding, (6) debriefing and documenting all learnings, and (7) merging everything via a single setup PR.

---

## 2. Project Context Summary

### Key Facts from plan_docs

| Fact | Detail |
|---|---|
| **Project Name** | OS-APOW (workflow-orchestration-queue) |
| **System Name** | Opencode-Server Agent Workflow Orchestration Queue |
| **Repository** | `intel-agency/workflow-orchestration-queue-charlie73` |
| **Target Repo** | `intel-agency/workflow-orchestration-queue` (configurable via env) |
| **Primary Language** | Python 3.12+ |
| **Secondary Languages** | PowerShell Core (pwsh), Bash |
| **Package Manager** | uv (Rust-based, replaces pip/poetry) |
| **Web Framework** | FastAPI + Uvicorn |
| **Data Validation** | Pydantic |
| **Async HTTP Client** | HTTPX |
| **Containerization** | Docker, DevContainers, Docker Compose |
| **Agent Runtime** | opencode-server CLI with LLM (GLM-5 / Claude 3.5 Sonnet) |
| **State Management** | GitHub Issues + Labels ("Markdown as a Database") |
| **No .NET** | This is a Python/Shell ecosystem; `global.json` is explicitly excluded |

### Architecture Overview (4 Pillars)

1. **The Ear (Work Event Notifier):** FastAPI webhook receiver with HMAC SHA256 signature validation, event triage, and WorkItem manifest generation.
2. **The State (Work Queue):** GitHub Issues as task state machine with labels (`agent:queued`, `agent:in-progress`, `agent:reconciling`, `agent:success`, `agent:error`). GitHub Assignees used as distributed lock semaphore.
3. **The Brain (Sentinel Orchestrator):** Async Python background service with polling engine (60s interval), shell-bridge dispatch (`devcontainer-opencode.sh`), workflow mapping, and telemetry.
4. **The Hands (Opencode Worker):** Isolated DevContainer executing markdown instruction modules against the cloned codebase.

### Development Phases

| Phase | Name | Focus |
|---|---|---|
| 0 | Seeding & Bootstrapping | Manual clone, seed plan docs, project setup |
| 1 | The Sentinel (MVP) | Persistent polling, shell-bridge dispatch, status feedback |
| 2 | The Ear (Webhook Automation) | FastAPI webhook receiver, HMAC validation, template triaging |
| 3 | Deep Orchestration | Architect sub-agent, autonomous bug correction, workspace indexing |

### Key Constraints

- **Self-bootstrapping:** The system is designed to build itself. Initial seed is manual; later phases are built by the AI using its own orchestration.
- **Script-first integration:** The Sentinel interacts with workers exclusively via `devcontainer-opencode.sh`, not the Python Docker SDK.
- **Polling-first resiliency:** Polling is the primary discovery mechanism; webhooks are an optimization layer.
- **Provider-agnostic queue:** `ITaskQueue` interface enables future support for Linear, Notion, or SQL queues.
- **Security:** HMAC signature validation, network isolation, ephemeral credentials, log scrubbing, resource constraints (2 CPU, 4GB RAM per worker).

### Existing Reference Implementations in plan_docs

- `orchestrator_sentinel.py` — Reference implementation of the Sentinel Orchestrator (Phase 1: Stories 2 & 3). Uses asyncio, httpx, Pydantic models, and shell-bridge dispatch.
- `notifier_service.py` — Reference implementation of the Work Event Notifier. Uses FastAPI, HMAC verification, Pydantic schemas, and a pluggable `ITaskQueue` interface.
- `interactive-report.html` — React-based interactive HTML report visualizing the architecture, development plan, and tech stack.

### Known Risks

| Risk | Impact | Mitigation |
|---|---|---|
| GitHub API rate limiting | High | Use GitHub App Installation tokens (5,000 req/hr), caching, long-polling |
| LLM looping / hallucination | High | `max_steps` timeout, cost guardrails, retry counter (`agent:stalled` if >3) |
| Concurrency collisions | Medium | GitHub Assignees as distributed lock |
| Container drift | Medium | Periodic `docker-compose down && up` between major Epics |
| Security injection via webhook | Medium | Strict HMAC validation, credential scoping |

---

## 3. Assignment Execution Plan

### 3.0 `create-workflow-plan` (Pre-script-begin Event)

| Field | Content |
|---|---|
| **Assignment** | `create-workflow-plan`: Create Workflow Plan |
| **Goal** | Produce a comprehensive workflow execution plan covering all assignments, project context, dependencies, and risks before any other work begins. |
| **Key Acceptance Criteria** | - Dynamic workflow file read and fully understood <br> - Every assignment traced and read completely <br> - All plan_docs files read and summarized <br> - Plan lists each assignment in order with goal, acceptance criteria, notes, risks <br> - Plan presented to stakeholder and approved <br> - Plan committed as `plan_docs/workflow-plan.md` |
| **Project-Specific Notes** | This is a Python project (not .NET). The plan must account for `uv` as the package manager and the absence of `global.json`. The `plan_docs/` directory contains reference implementations (`.py` files) that are not templates but architectural guides. |
| **Prerequisites** | Access to `nam20485/agent-instructions` remote repository for assignment resolution |
| **Dependencies** | None — this is the first assignment |
| **Risks / Challenges** | Some assignments may 404 (noted as open questions). The `create-agents-md-file` assignment exists but is not always included in standard workflows. |
| **Events** | None (this IS a pre-script-begin event) |

---

### 3.1 `init-existing-repository`

| Field | Content |
|---|---|
| **Assignment** | `init-existing-repository`: Initiate Existing Repository |
| **Goal** | Set up the repository's administrative infrastructure: branch protection, GitHub Project board, labels, file renames, and create the setup PR. |
| **Key Acceptance Criteria** | - New branch `dynamic-workflow-project-setup` created first (all work commits here) <br> - Branch protection ruleset imported from `.github/protected-branches_ruleset.json` <br> - GitHub Project created with Board template and columns (Not Started, In Progress, In Review, Done) <br> - Project linked to repository <br> - Labels imported from `.github/.labels.json` via `scripts/import-labels.ps1` <br> - `devcontainer.json` `name` property renamed to `workflow-orchestration-queue-charlie73-devcontainer` <br> - `ai-new-app-template.code-workspace` renamed to `workflow-orchestration-queue-charlie73.code-workspace` <br> - PR created from branch to `main` |
| **Project-Specific Notes** | - Branch naming: `dynamic-workflow-project-setup` <br> - The repo already has scripts (`gh-auth.ps1`, `common-auth.ps1`, `import-labels.ps1`, `test-github-permissions.ps1`) that must be leveraged <br> - The ruleset import requires `GH_ORCHESTRATION_AGENT_TOKEN` (PAT with `administration: write` scope), NOT `GITHUB_TOKEN` <br> - The workspace file may be named differently; check for `.code-workspace` extension <br> - The template has special handling for `intel-agency` owner — verify auth scopes match |
| **Prerequisites** | GitHub authentication with scopes: `repo`, `project`, `read:project`, `read:user`, `user:email`, `administration: write` |
| **Dependencies** | None (first main assignment) |
| **Risks / Challenges** | - **Ruleset import may fail** if `GH_ORCHESTRATION_AGENT_TOKEN` is not set or lacks `administration: write` scope <br> - **PR creation** requires at least one commit pushed first; must sequence correctly <br> - **Idempotency**: Ruleset creation should check for existing ruleset by name <br> - **Workspace file name**: The template file may not exist with the expected name; search for `.code-workspace` files |
| **Events** | Post-assignment-complete: `validate-assignment-completion`, `report-progress` |

---

### 3.2 `create-app-plan`

| Field | Content |
|---|---|
| **Assignment** | `create-app-plan`: Create Application Plan |
| **Goal** | Analyze the plan_docs application specification and produce a comprehensive application development plan, documented as a GitHub Issue, with milestones and project tracking. |
| **Key Acceptance Criteria** | - Application template thoroughly analyzed and understood <br> - Plan follows Appendix A template structure <br> - Detailed phase breakdown with steps for each phase <br> - All components, dependencies, tech stack planned <br> - Mandatory requirements addressed (testing, documentation, containerization) <br> - Risks and mitigations identified <br> - Plan documented as GitHub Issue using the application-plan template <br> - Milestones created and linked to issues <br> - Issue added to GitHub Project <br> - Labels applied (`planning`, `documentation`) |
| **Project-Specific Notes** | - **PLANNING ONLY — NO CODE IMPLEMENTATION** <br> - The app spec is `OS-APOW Implementation Specification v1.md` (not `ai-new-app-template.md`) <br> - Supporting docs: Architecture Guide v3, Development Plan v4, reference Python files <br> - The project uses Python 3.12+ / FastAPI / Pydantic / HTTPX / uv (NOT .NET) <br> - Must create `plan_docs/tech-stack.md` and `plan_docs/architecture.md` <br> - The plan issue template is at `.github/ISSUE_TEMPLATE/application-plan.md` <br> - The `orchestration:plan-approved` label is NOT applied by this assignment — it's applied by the post-script-complete event |
| **Prerequisites** | Repository initialized (labels exist, project board exists) |
| **Dependencies** | `init-existing-repository` — requires GitHub Project, labels, and milestones infrastructure |
| **Risks / Challenges** | - The template may reference `ai-new-app-template.md` which doesn't exist; the actual spec is `OS-APOW Implementation Specification v1.md` <br> - The Appendix A template has been moved to `.github/ISSUE_TEMPLATE/application-plan.md` — must resolve correct path <br> - Phase 1-3 decomposition is already well-defined in the Development Plan v4; the plan issue should reference and extend it rather than re-invent |
| **Events** | Pre-assignment-begin: `gather-context` <br> Post-assignment-complete: `validate-assignment-completion`, `report-progress` <br> On-assignment-failure: `recover-from-error` |

---

### 3.3 `create-project-structure`

| Field | Content |
|---|---|
| **Assignment** | `create-project-structure`: Create Project Structure |
| **Goal** | Create the actual project scaffolding: solution structure, Dockerfiles, docker-compose, CI/CD foundation, documentation, and repository summary. |
| **Key Acceptance Criteria** | - Solution/project structure created per application plan's tech stack <br> - All required project files and directories established <br> - Dockerfiles and docker-compose.yml created <br> - Basic CI/CD pipeline structure established <br> - Documentation structure created (README, docs/) <br> - Development environment validated (build succeeds) <br> - Initial commit made with complete scaffolding <br> - Repository summary document (`.ai-repository-summary.md`) created and linked from README <br> - All GitHub Actions pinned to specific commit SHAs |
| **Project-Specific Notes** | - **Tech stack:** Python 3.12+, FastAPI, Pydantic, HTTPX, uv <br> - **No .NET solution** — use `pyproject.toml` + `uv` pattern, NOT `.sln`/`.csproj` <br> - Use `.python-version` for Python version pinning (not `global.json`) <br> - Project structure defined in Implementation Specification v1: <br> ```text <br> workflow-orchestration-queue/ <br> ├── pyproject.toml <br> ├── uv.lock <br> ├── src/ <br> │   ├── notifier_service.py <br> │   ├── orchestrator_sentinel.py <br> │   ├── models/ <br> │   │   ├── work_item.py <br> │   │   └── github_events.py <br> │   └── interfaces/ <br> │       └── i_task_queue.py <br> ├── scripts/ <br> ├── local_ai_instruction_modules/ <br> └── docs/ <br> ``` <br> - Reference implementations already exist in `plan_docs/` — adapt them into `src/` <br> - Dockerfile: Use Python 3.12 base, install uv, COPY src before `uv pip install -e .` <br> - docker-compose: Use Python stdlib for healthcheck (no curl in base image) <br> - Existing scripts/ directory must be preserved (not overwritten) <br> - CI/CD workflows must pin actions to SHAs |
| **Prerequisites** | Application plan exists (issue + milestones) |
| **Dependencies** | `create-app-plan` — requires the plan issue and tech-stack/architecture docs for structure decisions |
| **Risks / Challenges** | - **Existing files:** The template already has scripts/, .github/workflows/, .devcontainer/ — must NOT overwrite these <br> - **Dockerfile placement:** Must follow the plan spec, not conflict with existing `.github/.devcontainer/Dockerfile` <br> - **CI/CD:** Template already has `validate`, `publish-docker`, `prebuild-devcontainer`, `orchestrator-agent` workflows — new CI/CD must complement, not replace <br> - **Build validation:** Since the project is Python-based, "build" = `uv sync` or `uv pip install` + lint checks, not `dotnet build` |
| **Events** | Post-assignment-complete: `validate-assignment-completion`, `report-progress` |

---

### 3.4 `create-agents-md-file`

| Field | Content |
|---|---|
| **Assignment** | `create-agents-md-file`: Create AGENTS.md File |
| **Goal** | Create a comprehensive `AGENTS.md` at the repository root that provides AI coding agents with the context and instructions they need to work effectively on the project. |
| **Key Acceptance Criteria** | - `AGENTS.md` exists at repository root <br> - Contains project overview, tech stack, setup/build/test commands <br> - Contains code style and conventions section <br> - Contains project structure / directory layout <br> - Contains testing instructions <br> - Contains PR/commit guidelines <br> - All commands validated by running them <br> - File committed and pushed to working branch |
| **Project-Specific Notes** | - The template repository already has an `AGENTS.md` at the root — this assignment should **update/replace** it with project-specific content <br> - Must document Python-specific commands: `uv sync`, `uv run pytest`, `ruff check`, `mypy`, etc. <br> - Must reference the existing devcontainer and scripts infrastructure <br> - Should complement, not duplicate, README.md and `.ai-repository-summary.md` <br> - The file follows the open [agents.md](https://agents.md/) specification <br> - Test commands: Shell tests in `test/` directory (`bash test/test-prompt-assembly.sh`, etc.) |
| **Prerequisites** | Repository initialized, application plan exists, project structure created |
| **Dependencies** | `create-project-structure` — requires the project to be scaffolded so commands can be validated |
| **Risks / Challenges** | - **Command validation:** Some commands may fail if the Python environment isn't fully set up yet (e.g., `uv sync` requires `pyproject.toml` to be correct) <br> - **Existing AGENTS.md:** Must carefully evaluate which parts of the template's AGENTS.md are still relevant vs. need replacement <br> - **Monorepo consideration:** The assignment mentions monorepo sub-AGENTS.md files, but this project is a single service — not applicable |
| **Events** | Post-assignment-complete: `validate-assignment-completion`, `report-progress` |

---

### 3.5 `debrief-and-document`

| Field | Content |
|---|---|
| **Assignment** | `debrief-and-document`: Debrief and Document Learnings |
| **Goal** | Perform a comprehensive debriefing session capturing key learnings, insights, deviations, and areas for improvement from the entire project-setup workflow execution. |
| **Key Acceptance Criteria** | - Detailed debrief report created using the 12-section structured template <br> - All deviations from assignments documented <br> - Report reviewed and approved by stakeholders <br> - Execution trace saved as `debrief-and-document/trace.md` <br> - Report committed and pushed to repo |
| **Project-Specific Notes** | - The debrief must cover ALL preceding assignments (init, plan, structure, agents-md) <br> - Must flag any plan-impacting findings as ACTION ITEMS with recommendations (new issue or plan update) <br> - Must include the execution trace of all commands run, files created/modified <br> - The 12 required sections: Executive Summary, Workflow Overview, Key Deliverables, Lessons Learned, What Worked Well, What Could Be Improved, Errors Encountered, Complex Steps, Suggested Changes, Metrics, Future Recommendations, Conclusion |
| **Prerequisites** | All main assignments completed |
| **Dependencies** | All prior main assignments (`init-existing-repository`, `create-app-plan`, `create-project-structure`, `create-agents-md-file`) |
| **Risks / Challenges** | - **Completeness risk:** Must capture ALL deviations, even minor ones <br> - **Action items must be filed as GitHub issues** — not left as text-only notes <br> - **Continuous improvement:** The debrief triggers a `continuous-improvement` assignment cycle |
| **Events** | Post-assignment-complete: `validate-assignment-completion`, `report-progress` |

---

### 3.6 `pr-approval-and-merge`

| Field | Content |
|---|---|
| **Assignment** | `pr-approval-and-merge`: Pull Request Approval and Merge |
| **Goal** | Complete the full PR approval and merge process: resolve all PR review comments, obtain approval, merge the setup PR, close associated issues, and clean up. |
| **Key Acceptance Criteria** | - All CI/CD checks pass (with up to 3 remediation attempts) <br> - Code review delegated to independent reviewer (NOT self-review) <br> - Auto-reviewer comments (Copilot, CodeQL, etc.) waited for <br> - `ai-pr-comment-protocol.md` workflow executed and logged <br> - All review threads resolved with replies and GraphQL verification <br> - Stakeholder approval obtained <br> - PR merged successfully <br> - Source branch deleted <br> - Related issues closed/updated |
| **Project-Specific Notes** | - This is the **self-approval** scenario for a setup PR — the assignment instructions mention special handling for project-setup <br> - The PR was created in `init-existing-repository` (assignment 3.1) and accumulates all subsequent commits <br> - Must follow `ai-pr-comment-protocol.md` and execute `pr-review-comments` assignment <br> - The PR contains: branch protection config, project board setup, labels, file renames, application plan, project scaffolding, AGENTS.md, debrief report |
| **Prerequisites** | All main assignments completed, debrief approved |
| **Dependencies** | All prior main assignments completed and validated |
| **Risks / Challenges** | - **Self-approval for setup PR:** The workflow description indicates "self-approval for setup PR" — must clarify if this means bypassing the code-reviewer delegation requirement <br> - **CI remediation loop:** If any CI checks fail, up to 3 fix attempts are allowed before escalation <br> - **Merge conflicts:** If `main` has received pushes since branch creation, conflicts may arise <br> - **Branch deletion:** After merge, the source branch should be deleted per policy |
| **Events** | Post-script-complete (after this assignment): Apply `orchestration:plan-approved` label |

---

### 3.7 `validate-assignment-completion` (Post-assignment-complete Event)

| Field | Content |
|---|---|
| **Assignment** | `validate-assignment-completion`: Validate Assignment Completion |
| **Goal** | Independently verify that each completed assignment has met all its acceptance criteria by checking file outputs, running verification commands, and producing a validation report. |
| **Key Acceptance Criteria** | - All required files from assignment exist <br> - All verification commands pass <br> - Validation report created with pass/fail status <br> - If failed, specific remediation steps provided <br> - **Must be delegated to independent QA agent** (not the same agent that performed the work) |
| **Project-Specific Notes** | - Runs after EACH main assignment (6 times total) <br> - For Python assignments: use `uv run pytest`, `ruff check`, `mypy` <br> - For GitHub operations: delegate `github-expert` to query live repo state <br> - Reports saved to `docs/validation/VALIDATION_REPORT_<assignment-name>_<timestamp>.md` <br> - Validation must be fast (< 5 minutes) |
| **Prerequisites** | A main assignment has just completed |
| **Dependencies** | The main assignment whose output is being validated |
| **Risks / Challenges** | - **Independent agent requirement:** Must use a different agent type (e.g., `qa-test-engineer`) than the implementer <br> - **GitHub state verification:** Some acceptance criteria involve GitHub API state (labels, projects, milestones) that require live queries |
| **Events** | None |

---

### 3.8 `report-progress` (Post-assignment-complete Event)

| Field | Content |
|---|---|
| **Assignment** | `report-progress`: Report Progress After Workflow Step Completion |
| **Goal** | Generate structured progress reports with step tracking, output capture, validation checkpoints, and state checkpointing after each workflow step. |
| **Key Acceptance Criteria** | - Structured progress report with step name, duration, status <br> - All step outputs captured and recorded <br> - Workflow state checkpointed for resume capability <br> - Action items filed as GitHub issues (mandatory) <br> - Deviations and plan-impacting discoveries documented |
| **Project-Specific Notes** | - Runs after EACH main assignment (6 times total) <br> - Must include "Deviations & Findings" and "Plan-Impacting Discoveries" sections <br> - Action items must be filed as GitHub issues with `priority:low` and `needs-triage` labels <br> - Format: `=== STEP COMPLETE: <step-name> ===` with progress percentage |
| **Prerequisites** | A main assignment has just completed |
| **Dependencies** | The main assignment that just finished |
| **Risks / Challenges** | - **Mandatory issue filing:** All identified items must become GitHub issues — no exceptions <br> - **Overhead:** Running this 6 times adds workflow time; must be fast |
| **Events** | None |

---

## 4. Sequencing Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                     project-setup Dynamic Workflow                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────────────────┐                                                   │
│  │ 0. create-workflow-  │  ← PRE-SCRIPT-BEGIN EVENT                        │
│  │    plan              │  (this document)                                   │
│  └──────────┬───────────┘                                                   │
│             │                                                               │
│             ▼                                                               │
│  ┌──────────────────────┐                                                   │
│  │ 1. init-existing-    │  → Branch creation, ruleset, project board,       │
│  │    repository         │    labels, file renames, PR creation              │
│  └──────────┬───────────┘                                                   │
│             │                                                               │
│             ├──── [validate-assignment-completion] ──┐                      │
│             ├──── [report-progress] ─────────────────┤                      │
│             │                                        │                      │
│             ▼                                        │                      │
│  ┌──────────────────────┐                            │                      │
│  │ 2. create-app-plan   │  → Analyze specs, create   │                      │
│  │    (PLANNING ONLY)   │    plan issue, milestones   │                      │
│  └──────────┬───────────┘                            │                      │
│             │                                        │                      │
│             ├──── [validate-assignment-completion] ──┤                      │
│             ├──── [report-progress] ─────────────────┤                      │
│             │                                        │                      │
│             ▼                                        │                      │
│  ┌──────────────────────┐                            │                      │
│  │ 3. create-project-   │  → Scaffold Python project,│                      │
│  │    structure         │    Dockerfiles, CI/CD, docs │                      │
│  └──────────┬───────────┘                            │                      │
│             │                                        │                      │
│             ├──── [validate-assignment-completion] ──┤                      │
│             ├──── [report-progress] ─────────────────┤                      │
│             │                                        │                      │
│             ▼                                        │                      │
│  ┌──────────────────────┐                            │                      │
│  │ 4. create-agents-md- │  → AI agent onboarding     │                      │
│  │    file              │    with validated commands  │                      │
│  └──────────┬───────────┘                            │                      │
│             │                                        │                      │
│             ├──── [validate-assignment-completion] ──┤                      │
│             ├──── [report-progress] ─────────────────┤                      │
│             │                                        │                      │
│             ▼                                        │                      │
│  ┌──────────────────────┐                            │                      │
│  │ 5. debrief-and-      │  → 12-section report,      │                      │
│  │    document          │    execution trace          │                      │
│  └──────────┬───────────┘                            │                      │
│             │                                        │                      │
│             ├──── [validate-assignment-completion] ──┤                      │
│             ├──── [report-progress] ─────────────────┤                      │
│             │                                        │                      │
│             ▼                                        │                      │
│  ┌──────────────────────┐                            │                      │
│  │ 6. pr-approval-and-  │  → CI check, code review,  │                      │
│  │    merge             │    resolve comments, merge  │                      │
│  └──────────┬───────────┘                            │                      │
│             │                                        │                      │
│             ├──── [validate-assignment-completion] ──┤                      │
│             ├──── [report-progress] ─────────────────┘                      │
│             │                                                               │
│             ▼                                                               │
│  ┌──────────────────────┐                                                   │
│  │ POST-SCRIPT-COMPLETE │  → Apply `orchestration:plan-approved` label      │
│  └──────────────────────┘                                                   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘

Dependency Chain (simplified):

  create-workflow-plan
         │
         ▼
  init-existing-repository ──── creates PR ──┐
         │                                   │
         ▼                                   │
  create-app-plan                            │
         │                                   │
         ▼                                   │
  create-project-structure ─── commits ──────┤
         │                                   │
         ▼                                   │
  create-agents-md-file ────── commits ──────┤
         │                                   │
         ▼                                   │
  debrief-and-document ────── commits ───────┤
         │                                   │
         ▼                                   │
  pr-approval-and-merge ───── merges ────────┘
```

---

## 5. Open Questions

### Q1: `create-agents-md-file` — Existing AGENTS.md Conflict

The template repository already has a comprehensive `AGENTS.md` at the root. The `create-agents-md-file` assignment says to create a new one, but the existing file contains critical template-specific instructions (repository map, coding conventions, tech stack, agent guardrails). **Question:** Should the existing `AGENTS.md` be completely replaced, or should it be updated in-place with project-specific content? **Recommendation:** Update in-place, preserving template infrastructure sections and replacing project-specific sections.

### Q2: PR Self-Approval for Setup PR

The workflow description mentions "self-approval for setup PR" for the `pr-approval-and-merge` assignment. However, the assignment itself mandates: "Code review delegated to `code-reviewer` subagent or designated reviewer (NOT self-review)." **Question:** Does the project-setup workflow override this constraint, allowing the orchestrator to self-approve? If so, what evidence is required?

### Q3: Application Plan Template Location

The `create-app-plan` assignment references "Appendix A" for the plan issue template, but notes it has been moved to `.github/ISSUE_TEMPLATE/application-plan.md`. **Question:** Does this file exist in the current repository, or does it need to be created?

### Q4: Workspace File Name

The `init-existing-repository` assignment says to rename `ai-new-app-template.code-workspace` to `<repo-name>.code-workspace`. **Question:** Does this file exist? A glob for `.code-workspace` files is needed to confirm.

### Q5: Branch Protection Ruleset — PAT Token Availability

Importing the branch protection ruleset requires `GH_ORCHESTRATION_AGENT_TOKEN` (a PAT with `administration: write` scope), not the standard `GITHUB_TOKEN`. **Question:** Is this secret available in the repository? If not, this step will fail and must be flagged.

### Q6: Python Tooling Not Yet in Devcontainer

The project requires Python 3.12+ with `uv`, `pytest`, `ruff`, `mypy`, etc. The current devcontainer includes Python but may not include the specific linting/formatting tools needed for command validation in `create-agents-md-file`. **Question:** Are these tools available, or will they need to be installed during the `create-project-structure` assignment?

### Q7: Existing Workflows — Preserve or Extend?

The template repository has existing GitHub Actions workflows (`validate`, `publish-docker`, `prebuild-devcontainer`, `orchestrator-agent`). The `create-project-structure` assignment says to create CI/CD workflows. **Question:** Should new Python-specific CI/CD workflows be created alongside the existing ones, or should the existing ones be modified to also handle Python validation?

### Q8: `create-app-plan` — Application Template File Name

The `create-app-plan` assignment looks for `plan_docs/ai-new-app-template.md`. The actual spec file is `plan_docs/OS-APOW Implementation Specification v1.md`. **Question:** Should the agent look for the standard filename first and fall back, or should it be told explicitly to use the Implementation Specification?

---

*End of Workflow Execution Plan*

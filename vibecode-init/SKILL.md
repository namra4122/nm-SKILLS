---
name: vibecode-init
description: Set up or repair a repository for plan-driven agentic coding. Use when starting a vibecoding project, adding AGENTS.md and project skills, establishing plans and progress tracking, configuring agent-safe verification, or removing contradictions between agent instructions and project documentation.
---

# Vibecode Project initializing

Build a repository control system that lets coding agents discover the product intent, constraints, architecture, active work, and proof commands without relying on chat history.

## Operating principles

- Treat repository files as the durable source of truth. Chat is temporary.
- Inspect before asking. Derive facts from source, manifests, lockfiles, CI, deployment files, and existing documentation.
- Ask only for product or policy decisions that the repository cannot answer.
- Separate stable rules, temporary plans, verified progress, and operational procedures.
- Keep each fact authoritative in one place. Link to it elsewhere instead of copying it.
- Configure the actual stack. Do not add tools, frameworks, services, or workflows merely because they are common.
- Preserve existing user work. Update compatible files in place and surface contradictions before replacing intent.
- Make every completion claim reproducible with a command or observable scenario.

## Phase 1: inspect the repository

Read the repository root, manifests, lockfiles, source layout, tests, CI, deployment configuration, environment examples, and existing agent instructions. Determine what is already true before interviewing the user.

Record the following facts:

- Project maturity: empty, prototype, active product, migration, or established system.
- Product shape: library, CLI, web app, mobile app, desktop app, API, service, monorepo, infrastructure project, data/ML system, embedded system, documentation project, or a combination.
- Technology stack: languages, runtimes, frameworks, package managers, build tools, test tools, linters, formatters, databases, queues, cloud providers, and deployment targets.
- Repository shape: applications, packages, shared libraries, adapters, generated code, migrations, fixtures, and ownership boundaries.
- Existing commands: install, develop, build, lint, typecheck, unit test, integration test, end-to-end test, package, deploy, and smoke test.
- Existing policies: security, privacy, compatibility, performance, accessibility, data retention, migrations, release process, and supported platforms.
- Existing drift: contradictory commands, stale architecture descriptions, completed plans marked active, undocumented environment variables, or progress claims without verification.

Completion criterion: every answer available from the repository is recorded, and the unresolved list contains decisions rather than lookup questions.

## Phase 2: interview the user

Ask one grouped round of concise questions. Offer concrete options when possible and recommend the conservative default. Skip questions already answered by the repository.

Cover these decision groups:

1. Product and outcome
   - What is being built, for whom, and what is the first useful end-to-end outcome?
   - Is this a prototype, production product, learning project, internal tool, or open-source project?
   - Which capabilities are explicitly out of scope?
2. Technology stack
   - Which language, runtime, framework, package manager, persistence layer, and deployment target are required?
   - Which choices are fixed, and which may the agent choose?
   - If the user said "tech stack" ambiguously, confirm the exact stack rather than inferring a fashionable default.
3. Quality and risk
   - What data is sensitive or irreversible?
   - Which security, privacy, compliance, availability, performance, accessibility, and compatibility constraints matter?
   - Which operations require explicit human approval?
4. Agent workflow
   - Should agents plan every change or only substantial cross-cutting work?
   - Should plans remain as living specifications, or be deleted/archive-marked after implementation?
   - Which verification command must pass before an agent reports completion?
   - May agents create migrations, install dependencies, access networks, modify CI, or deploy?
5. Delivery
   - What environments exist: local, preview, staging, production?
   - What proves the product works beyond tests: browser flow, CLI session, API request, device run, deployment smoke test, benchmark, or another scenario?
   - What documentation must stay public and current?

When answers materially conflict with existing files, show the conflict with exact paths and ask the user to choose the intended policy. Do not preserve two incompatible instructions.

Completion criterion: the project type, stack, first vertical slice, non-goals, invariants, agent permissions, verification gate, and deployment target are explicit.

## Phase 3: design the control plane

Choose the smallest useful document set. Prefer these roles:

| File                             | Authority                                                                                                           |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| `AGENTS.md`                      | Stable repository-wide rules, architectural boundaries, safety constraints, and pointers to active sources of truth |
| `.agents/plans/NNN-SLUG.md`      | Temporary or living implementation specification for substantial work                                               |
| `.agents/skills/<name>/SKILL.md` | Repeatable project-specific operational workflow with a distinct trigger                                            |
| `PROGRESS.md`                    | Verified implemented state and known limitations                                                                    |
| `CONTRIBUTING.md`                | Human contribution workflow and required checks                                                                     |
| `README.md`                      | Public product description, setup, and usage                                                                        |
| CI configuration                 | Machine-enforced verification gate                                                                                  |
| Manifest scripts or task runner  | Canonical executable commands                                                                                       |

Do not create every file automatically. Create a file only when it has a distinct owner and lifecycle. For a small repository, `AGENTS.md`, one active plan, manifest scripts, and CI may be sufficient.

Define one source for each fact:

- Commands belong in manifests or task-runner configuration. Documentation references their names.
- Product behavior belongs in the README or product specification.
- Stable engineering constraints belong in `AGENTS.md`.
- Feature decisions belong in the active numbered plan.
- Completed, verified state belongs in `PROGRESS.md`.
- Secrets belong in secret storage. Commit only sanitized `.env.example` keys and explanations.

Completion criterion: every proposed file has a distinct purpose, lifecycle, and non-overlapping authority.

## Phase 4: create or repair the files

### `AGENTS.md`

Keep it short enough to remain loaded on every task. Include:

- Current source-of-truth pointers and when to read them.
- Product and package map.
- Stable architecture and dependency-direction rules.
- Data-integrity and security invariants.
- Planning threshold and plan location.
- Canonical verification command.
- Documentation update rules.
- Explicit prohibitions only for high-risk actions.

Do not paste package scripts, directory listings, or temporary implementation steps into `AGENTS.md`.

### Numbered implementation plan

For substantial or multi-package work, create `.agents/plans/NNN-SLUG.md`. Use the next unused three-digit prefix. Include:

- Context and user outcome.
- Scope and non-goals.
- Domain terms and invariants.
- Current-state evidence from the repository.
- Target architecture and ownership boundaries.
- Interfaces, APIs, schemas, migrations, and compatibility effects.
- Ordered vertical phases, each ending in working behavior.
- Failure modes, security risks, rollback, and rollout.
- Exact verification commands and observable smoke scenarios.
- Completion checklist mapped to user outcomes.
- Lifecycle state: active living specification, delete after completion, or retain as an architecture record.

Plans must enable another agent to implement the work without inventing major product or architecture decisions.

### Project-specific skills

Create a skill only for a repeatable workflow that agents can execute, such as:

- Starting and reloading the local development environment.
- Seeding fixtures or sample data.
- Running a device, browser, CLI, or API smoke scenario.
- Building and installing an extension or plugin.
- Creating and verifying migrations.
- Releasing or deploying through an approved path.

Each skill needs YAML frontmatter with `name` and a trigger-rich `description`. Its body must state prerequisites, ordered actions, failure handling, cleanup, and an observable completion criterion. Read commands and configuration from the repository rather than duplicating values that can drift.

### `PROGRESS.md`

Create it only when the project needs durable implementation tracking beyond issues or the active plan. Record completed behavior only after verification. For each milestone, capture the user-visible result, proof command or scenario, known limitations, and the plan or change that established it. Never use progress tracking as a second backlog.

### Public and contributor documentation

Keep `README.md` user-facing. Keep `CONTRIBUTING.md` contributor-facing. Link to `AGENTS.md` and active plans instead of restating their policies. Update stale descriptions in the same change that changes behavior.

### Commands and CI

Add canonical commands for the detected stack. At minimum, provide an install path and one aggregate verification command that runs the applicable checks. Typical checks are format validation, lint, type checking, build, unit tests, integration tests, end-to-end tests, packaging, and smoke tests. Include only checks the repository genuinely supports.

Configure CI to run the same canonical commands from a clean checkout with the supported runtime versions. Add caching only after correctness. Keep secrets out of pull-request workflows and logs.

### Environment and ignore rules

Create or repair `.env.example`, ignore rules, editor settings, and generated-output exclusions when the stack needs them. Document every required environment variable without committing credentials. Mark generated artifacts clearly so agents edit their source instead.

Completion criterion: the selected control-plane files exist, point to one another correctly, and match the actual repository behavior.

## Phase 5: implement the first vertical slice

When the user requested full project setup rather than documentation-only setup, implement the smallest end-to-end slice from the active plan. A vertical slice must exercise the real boundaries of the product, such as UI to API to storage, CLI input to output, library API to consumer, or device event to persisted state.

Avoid broad scaffolding that does not run. Prefer one working path with real adapters over many placeholder modules. Add tests only for durable behavior, boundaries, invariants, transitions, precedence, and real errors.

Completion criterion: a user can exercise the first promised outcome through the real product surface.

## Phase 6: verify and reconcile

Run checks in increasing scope:

1. Validate changed configuration and focused behavior.
2. Run the canonical aggregate verification command.
3. Exercise the real product surface with the agreed smoke scenario.
4. Compare every agent-facing document for contradictions.

Reconcile at least these drift classes:

- `AGENTS.md` permits behavior that README or contributor docs prohibit.
- `PROGRESS.md` claims completion while an active plan remains entirely unchecked.
- A completed plan violates its declared archive or deletion lifecycle.
- CI runs different commands or runtime versions from local documentation.
- Environment examples omit variables required by code or deployment.
- Documentation names scripts, paths, APIs, or packages that no longer exist.
- Generated files appear editable while their source is elsewhere.

Treat drift as a failed setup. Resolve it or report the exact conflicting paths and the decision required.

Completion criterion: canonical checks pass, the smoke scenario works, and no agent-facing source contradicts another.

## Final response

Report:

- Files created or changed and the authority each owns.
- Product and stack decisions captured from the interview.
- Canonical install, development, verification, and smoke commands.
- The first vertical slice delivered, if requested.
- Remaining human decisions or external prerequisites.
- Verification actually performed, with exact outcomes.

Do not claim a command, test, deployment, or product flow passed unless it was executed and observed.

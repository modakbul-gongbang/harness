---
name: scaffold
description: |
  Design and optionally build a new project's architecture and AI work environment.
  Select a stack and dependencies, plan a working reference flow, and configure
  project instructions, verification, and useful skills/hooks.
  Use for "/scaffold", "new project setup", "프로젝트 세팅", or "초기 구조".
  Existing projects require an explicit adaptation scope, not automatic reinitialization.
allowed-tools:
  - Read
  - Grep
  - Glob
  - Task
  - Bash
  - Write
  - Edit
  - Agent
  - AskUserQuestion
  - WebSearch
  - WebFetch
---

# /scaffold - Architecture and Harness Scaffolding

Build a foundation that the next feature can extend consistently.
The highest-value output is one agreed, working reference flow, not an empty directory tree.

The workflow is L0 Goal -> L1 Environment -> L2 Architecture -> L3 Harness -> L4 Plan -> optional implementation and verification.
Planning and verification reports are Markdown.
Implementation produces actual source and configuration using the selected project tools; there is no separate scaffold CLI dependency.

Use the user's language.
Reuse answers and authorization already provided.
The layers are review checkpoints, not mandatory repeated permission questions.
Ask only about unresolved choices that materially affect the result.
For a small project, combine the proposed architecture, harness, and tasks into one review.
A request to plan only must end at the plan; a request to implement may proceed within its established scope.

## Before Writing

Confirm the target directory from the request and inspect it before creating output.
Read applicable project instructions, manifests, wrappers/lockfiles, existing harness files, and Git status without reading secret values.
Do not treat an existing repository as empty merely because it lacks application code.

- **New project:** create `scaffold-spec.md` after confirming the goal.
  Create a target directory only within the requested scope.
- **Existing project:** preserve the current stack, code, configuration, and unrelated user changes.
  For harness-only work, adapt the existing project without running an initializer or replacing its architecture.
  For restructuring, first record the current behavior, working verification commands, and explicitly agreed migration scope.
  If that scope is unclear, ask before planning a replacement.
- **Audit-only request:** use `/check-harness` if installed, or perform a read-only assessment.
  The sibling skill is optional and must not be assumed to exist.
- **Existing spec:** read and resume it when it represents the same goal.
  For a different goal, use a distinct output path rather than overwriting the previous work.

Keep all required references within this skill folder.
Direct skill installation does not install repository-root hooks, agents, permissions, MCP, or LSP.

## Output

Accumulate decisions in `{project_dir}/scaffold-spec.md`:

```markdown
# Scaffold Spec: {project}
Created: {date}
Goal: {one-line goal}
Status: {planning / ready / implementing / verified / blocked}

## L0: Goal
## L1: Environment
## L2: Architecture Decisions
## L3: Harness Setup
## L4: Plan
```

Use the actual chosen filename when resuming or keeping multiple specs.
After implementation, write `scaffold-verification.md` beside the spec, or a correspondingly distinct report name.
Do not produce a completion report merely because a plan was approved.

## L0: Goal

Confirm the product purpose, foundation scope, and the smallest complete reference flow.
Reflect existing answers instead of asking for confirmation again.
Ask about missing consequential requirements before deciding them.

Record:

- Confirmed goal and intended users.
- Reference flow and observable success/failure outcomes.
- Non-goals: product features beyond that flow, production deployment unless explicitly included, and other agreed exclusions.
- Existing-project adaptation or migration boundaries, if applicable.

Do not infer a separate frontend, API server, database, or deployment from a product name alone.
For a TODO app, establish persistence and UI needs first.
A server-rendered Spring app may fit without a separate Node app.
A CLI need not acquire a web server or database.

The reference flow may contain enough business behavior to prove the foundation.
Do not exclude all feature behavior and then require an untestable empty exemplar.

## L1: Environment

Inspect only tools relevant to the goal and existing project.

| Target | Evidence | Interpretation |
|--------|----------|----------------|
| Project and team choices | Instructions, manifests, build files, existing code | Existing constraints take priority over installed tooling |
| Runtime | Required version vs actual executable/version | For Java, check JDK and compiler; Node availability does not select Node |
| Build and dependency management | Gradle/Maven wrapper, BOM, lockfile, package scripts as applicable | Use the chosen ecosystem's conventions |
| Services, when needed | Actual availability of DB, Docker daemon, or other required service | An installed CLI alone does not prove usability |
| Git and platform | Worktree state, OS, paths, shell requirements | Preserve user work and account for actual platform constraints |
| Existing harness | Instructions, rules, skills, hooks, verification/CI commands | Extend what already works; do not create competing workflows |

Separate **observed**, **required but missing**, and **not checked**.
An unavailable required tool becomes a setup task or blocker; it is not permission to change stacks.
Record commands and relevant results without secret values.

## L2: Architecture Decisions

### Resolve Relevant Choices

Consider each dimension, but ask only about unresolved choices affecting the agreed scope.
There is no question quota or weighted score to fill.
A dimension may be N/A with a reason.

| Dimension | Relevant choices |
|-----------|------------------|
| Tech stack | Language/runtime, framework, build/package tool, supported versions |
| Communication | Actual consumers, transport, API/type contract and ownership |
| Data and state | Persistence, database, access library, migrations and test data |
| Testing | Observable outcomes, stable test boundaries, repeatable verification command |
| Environment | Local start, configuration, CI, required services and deployment constraints |

Prioritize questions by consequence: data loss and incompatible consumers matter more than optional formatting preferences.
Ask one or two concrete questions at a time, with a recommended option and its material tradeoff.
Offer agent-owned decisions when useful.
Record delegated decisions as `assumed: true`, not as user-confirmed facts.
Do not invent product policies such as retention, permissions, or payment behavior.

Use these decision states:

- **Confirmed:** explicitly supplied by the user.
- **Project-derived:** supported by a file path or existing behavior.
- **Delegated:** chosen within the user's delegation, with rationale.
- **N/A:** not applicable, with reason.
- **Deferred:** excluded optional work, or an unresolved required decision explicitly marked as blocking.

Carry settled choices forward.
A deferred optional feature stays outside this scaffold.
An unresolved choice needed by the reference flow blocks implementation readiness.

### Architecture and Dependency Selection

Use this order: explicit requirements and team constraints, existing project conventions/dependencies, then established framework solutions.
Choose the simplest structure that supports the actual behavior.
Do not add layers, services, repositories, code generation, or packages just for symmetry.

For a new project, prefer a suitable official initializer or maintained starter over inventing its build setup.
For an existing project, adapt its existing build instead of reinitializing it.

Before selecting or adding a dependency:

1. Name the requirement it satisfies and check whether the framework or an existing dependency already provides it.
2. Consult current official documentation or registry metadata for runtime/framework compatibility, maintenance/support, and relevant license constraints.
   Use available web or package tooling and record source URLs and the checked date.
   Do not claim compatibility from memory.
3. Explain the material tradeoff and why an additional package is needed.
   Avoid a catalog of alternatives when the team's existing choice already fits.
4. Follow the ecosystem's wrapper, dependency management/BOM, and lockfile conventions.
   Resolve dependencies and build during implementation; version selection alone is not proof of compatibility.

If sources cannot be reached, retain established versions where possible and mark new compatibility decisions unverified.
A required unverified choice remains an implementation-readiness blocker.
Do not replace an established stack with an easier-to-install one.

Prefer reusable framework behavior for logging, configuration, and errors.
Add custom shared modules only where there is actual behavior the framework does not supply.
Use observable test outcomes and real owned boundaries; fake genuinely external systems only when necessary.
Choose API contracts for actual consumer needs, not a universal preference for schema-first or code generation.

### Conditional Extensions

| Requirement | Extension |
|-------------|-----------|
| Explicit shared-contract need or multiple consumers needing synchronization | Type contracts using the chosen framework strategy |
| Persistent data | Data connection, schema/migrations, isolated test data and seed policy |
| Reproducible service environment needing containers | Docker/Compose; check availability separately |
| Long-running service | Appropriate health and shutdown behavior, preferably framework-native |

Record decisions, evidence, tradeoffs, assumptions, activated extensions, and known gaps.
Check the most consequential decision against a concrete failure scenario, such as restart losing required persistent data.
Ask a follow-up only if it exposes an unresolved requirement.
Present the resulting architecture for review when prior authorization does not already cover the decisions.

## L3: Harness Setup

### Context and Instructions

Use existing domain terms, business rules, and team conventions.
Ask only for missing ones that affect the agreed work.
Do not infer unknown business policies from the stack or create generic policy documents to fill a template.

Keep the root CLAUDE.md a concise map:

- Purpose, stack, and meaningful directory boundaries.
- Actual start and verification commands.
- Important project constraints and links with explicit reading conditions.
- Selected project workflows, skills, and hooks when useful.

Example reading conditions:

```markdown
- Read docs/guides/backend.md before changing Java behavior.
- Read docs/architecture.md before changing module boundaries.
```

Create only guides that have meaningful content and are needed by the work.
Reuse existing guides as the single source instead of duplicating their rules.
Length is a maintenance target, not a pass/fail threshold.
`@import` loads with its parent CLAUDE.md; it organizes files without deferring their context cost.

CLAUDE.md and Rules provide model context, not mechanical enforcement.
Use an appropriate Hook, permission setting, linter, or CI check for a constraint that needs an executable boundary.
Keep shared instructions in the root, directory context in local CLAUDE.md files when useful, and cross-directory file-type guidance in path-scoped Rules.
Do not create one file per constraint.

Example `.claude/rules/java.md`:

```markdown
---
paths:
  - "src/**/*.java"
---
Read docs/guides/backend.md before changing Java behavior.
```

Rules without `paths` load unconditionally.
Only reference documents that exist or are included in the implementation plan.

### Skills, Hooks, Tools, and Review

Start minimal.
Suggest a skill only when its recurring or rare critical purpose is clear, for example a project-specific migration or webhook test procedure.
A frontend framework does not by itself require a component-generator skill.
Docker does not imply deployment, and a CLI does not imply publication.
Do not add release/deploy workflows outside the agreed scope.

Before planning hooks or verification tasks, read [references/verification.md](references/verification.md).
Choose hooks for actual failure modes, not for every installed tool.

| Need | Candidate mechanism |
|------|---------------------|
| Format changed source | PostToolUse running the configured formatter on the affected supported path |
| Full build/typecheck/test | Common verification command used locally and in CI; optional bounded completion gate |
| Relevant guidance at a known failure point | Narrow path/tool-specific context reminder |
| Sensitive operations | Appropriate permissions/sandbox plus narrowly scoped checks when needed |

Do not run a full-project typecheck or autofix after every edit by default.
Do not block a placeholder-only `.env.example` together with secret-bearing files, or prevent legitimate package-manager lockfile updates.
An Edit/Write path check does not protect Bash or every other tool; state its actual scope.

Reuse the team's implementation and review workflow.
Offer independent verification when the team requires it or risk justifies it.
The verifier reads the same requirements/guides and performs checks, rather than endorsing the implementer's report.
A reviewer file alone is not proof of independent verification.

Consider LSP for Java definition/reference lookup and a browser CLI or MCP for actual UI verification when relevant.
Record the need, setup, and operation check; do not universally install agents, MCP, LSP, or Stop gates.
Keep setup project-scoped unless user/global changes were requested.
Distinguish configuration, startup, and successful tool operation.

Record the selected instructions, skills, hooks, tools, common verification command, and review responsibilities.
Explain meaningful omissions.
Combine these into the L3 summary rather than asking for approval on every artifact.
Do not expand existing authorization to publishing, production changes, or unrelated global setup.

## L4: Plan

Derive requirements and tasks from the selected architecture and harness.
Use framework-appropriate filenames, not a hard-coded TypeScript skeleton.

| Requirement | Outcome |
|-------------|---------|
| R1 | Foundation and one complete reference flow using the actual selected boundaries |
| R2 | Tests with expectations from the agreed behavior and a repeatable test environment |
| R3 | Concise instructions, appropriate quality checks, a common local/CI verification entrypoint, and configuration validation |
| R4, if selected | Shared type/API contracts |
| R5, if selected | Real data layer, schema/migrations, seed and reset policy |
| R6, if selected | Container/service environment |
| R7, if selected | Runtime health and shutdown behavior |
| R8, if selected | Scoped instructions without duplication |
| R9, if selected | Project-specific task skills |
| R10, if selected | Hooks with defined inputs, scope, failure behavior, and checks |

If environment variables are required, use the framework's configuration schema/registry and startup validation.
Keep required/optional keys, shapes, and missing-value behavior in code; required keys have no silent fallback.
Keep the example aligned with that source, with placeholders only, and prevent secret values from entering reports/logs.
Do not invent environment variables or custom configuration abstractions when the framework already provides the needed contract.

### Task Dependencies

| Task | Fulfills | Depends on |
|------|----------|------------|
| T1: Initialize selected framework, or adapt existing foundation | R1 | Scope and required decisions resolved |
| T2: Instructions, applicable quality tools, configuration, common verification command and CI wiring | R3, R8 | T1 |
| T4: Test framework and isolated test environment | R2 | T1; T6/T7 if the test environment needs them |
| T5: Type/API contract foundation, if selected | R4 | T1 |
| T7: Container/service setup, if selected | R6 | T1 |
| T6: Data layer and schema, if selected | R5 | T1; T7 if required to run the database |
| T3: Complete reference flow and behavioral tests | R1, R2 | T2, T4; T5 if R4; T6 if R5; T7 when needed to run |
| T8: Selected runtime behavior | R7 | T3 |
| T_SKILL: Useful task skills with real commands | R9 | T3 |
| T_HOOK: Selected hooks and fixtures | R10 | T2, T4; referenced commands/scripts must exist first |
| TF: Foundation and harness verification | All applicable requirements | All selected tasks |

Adapt dependencies to actual tool requirements.
A generated API client also needs its generation task before the reference flow uses it.
Do not build the exemplar against a disposable stub and replace it with the chosen data/contract foundation later.
For an existing project, satisfy requirements with verified existing capabilities where possible rather than recreating them.

### Reference Flow

Demonstrate an actual entry point, the selected processing/data boundary, an observable result, and a meaningful failure case.
Show naming, error propagation, tests, and reusable framework capabilities.
Use real persistence and contracts when those were selected.
Do not require a data layer for a CLI that does not need one.
The question is: "Can the next feature follow this working example?"

For each task record its requirement, dependencies, acceptance evidence, and status.
Present the plan and unresolved scope choices for review.
If implementation is already requested, proceed within that authorization.
If the user requested a plan only, finish the plan and stop without asking again about implementation.
Ask whether to implement now or save the plan only when that choice has not yet been supplied.

## Implementation and Verification

Execute selected tasks in dependency order.
Read [references/verification.md](references/verification.md) before TF and follow the applicable checks.
Do not mark tasks done merely because their files exist.

The report records:

- Each requirement and actual command or observation.
- Result: VERIFIED, FAILED, NOT_RUN, BLOCKED, or N/A with a reason.
- Evidence paths, relevant source revision/state, and independent reviewer involvement if any.
- Remaining issues and the concrete next step.

Required checks must pass before declaring the scaffold complete.
If blocked, preserve completed work, mark the spec blocked, and explain what remains; do not hide missing verification as N/A.
Optional unrun checks must remain visible.
Separate local execution, actual Hook events, remote CI, and published/deployed state.

After verified implementation, suggest `/check-harness --verify` if installed and useful.
It is an optional broader audit, not a replacement for TF or a mandatory sibling dependency.
Do not repeat checks already proven without a concrete reason.

## Before Finishing

- Preserve the user's plan-only vs implementation choice.
- Ensure the spec reflects actual decisions, selected extensions, omissions, and task dependencies.
- Ensure no existing project files or previous spec were silently replaced.
- Ensure instructions, skills, hooks, and documentation refer to real project commands and paths.
- For implementation, record actual reference-flow and harness checks with evidence and honest limitations.
- Do not claim installation, independent review, remote CI, or deployment that was not performed.

# Verify the Foundation and Harness

Read this when planning hooks, the shared verification command, or final scaffold verification.
Apply checks to the selected project and agreed reference flow; absence of optional tools is not a failure.

## One Repeatable Entry Point

Use an existing verification command if it provides the required checks.
Otherwise define a framework-appropriate entrypoint, such as a Gradle task, Maven verification, package script, or small wrapper.
Local execution and CI must invoke the same entrypoint for the same checks.
It must propagate failing exit codes and must not report success when required tests did not run.

Record prerequisites, start/stop commands, test data, and safe reset instructions.
Use isolated local test data and external service sandboxes when needed, without production credentials.
Containerization is one option, not a universal requirement.
Manage only processes and resources created for the verification.

The configuration contract belongs in the selected framework's schema/registry.
When environment variables are used, verify that missing required or malformed values fail visibly without logging values, and that the placeholder example matches the declared keys.
Use the existing test framework for meaningful configuration-contract checks instead of creating a parallel validation system.

## Prove the Reference Flow

Derive expected results from the agreed requirement, not from whatever the implementation currently returns.
Run dependency resolution, build, applicable static checks, and the behavioral tests.
Record actual exit codes and test counts; an empty required suite is not evidence of success.

- API/data project: exercise the real selected API and owned persistence boundary.
  If persistence across restarts is required, verify it; a mock repository result cannot prove it.
- UI project: start the app and exercise the reference user flow in a real browser using the available CLI or MCP.
  Capture relevant evidence and check the actual scripts/fonts, wrapping, and important error state.
  HTML existence or an HTTP 200 alone cannot prove the UI.
- CLI project: run the command with representative input and verify its observable output, exit code, and failure behavior.

Cover an important failure condition as well as the successful reference flow.
Favor assertions on observable results and stable boundaries over internal call wiring.
Do not adjust a failing expectation to match the implementation unless an independent requirement or test defect justifies the change.
Do not impose coverage percentages or large generated suites as a scaffold completion target.

## Hooks: Configuration and Behavior Are Separate

Use current official runtime documentation when generating event configuration and scripts.
Record the event, matcher, script path, input fields, output/exit contract, timeout, and expected behavior.
For file tools, match the tool name at configuration level and inspect the event's path in the script when needed.
Normalize paths for the target OS and quote arguments; do not interpolate untrusted tool input into shell code.
Keep scripts inside the generated project and resolve paths from the project, not this skill's source checkout.

For each generated hook, run isolated event-input fixtures:

| Case | Evidence required |
|------|-------------------|
| Targeted successful operation | Expected allowed result, formatting, or feedback |
| Targeted failing operation | Expected denial or visible failure, not swallowed exit status |
| Non-target operation | Unrelated action stays unaffected |
| Malformed input or missing command | Explicit, documented failure behavior, not accidental success |
| Completion gate, when selected | Failed/missing/stale evidence is handled; retry and BLOCKED exit are bounded |

For formatters, verify that only the intended supported file changes.
For secret-path checks, distinguish real secret files from a placeholder-only example and state the covered tools.
A successful Edit/Write fixture does not establish protection against Bash, other tools, or direct OS access.
Use permissions/sandbox for the relevant broader boundary instead of claiming a narrow hook provides one.

If using a Stop gate, define the explicit completion condition, the freshness of its evidence, the continuation limit, and how a blocked task ends.
Handle `stop_hook_active` or equivalent runtime state so the hook does not loop indefinitely or block ordinary conversation as unfinished work.
Do not create a Stop gate merely to fill the harness checklist.

When runtime access permits, exercise an actual event in the target project and verify that the active settings invoke the hook.
Fixture success alone verifies the script, not runtime activation.
If runtime execution is unavailable, mark activation NOT_RUN or BLOCKED and keep any required activation check unresolved.

## Tools and Review

Check only tools selected for this project.
For LSP, distinguish plugin/configuration, server/runtime availability, and a real definition/reference result.
For a browser or external tool, distinguish configuration, startup, and an actual safe operation.
If a required UI/tool check cannot run, report BLOCKED; do not substitute a static check and call it equivalent.

When independent verification is selected, give the verifier the same requirement, guides, source, and commands.
Have it run checks and record its own result, including what could not be checked.
Compare relevant source state before and after verification; a changed source requires rechecking affected evidence.
If no independent run occurred, say so; a role definition or the implementer's review is not independent evidence.

## Report and Completion

Record each check as VERIFIED, FAILED, NOT_RUN, BLOCKED, or N/A with an applicability reason.
Include the command/operation, expected and actual result, evidence path, and relevant source revision or worktree state.
Do not put real secrets or personal content in the report.

Required failures, unrun checks, and blockers prevent a completion claim.
Optional unrun checks stay visible without blocking unrelated completed scope.
CI configuration and a local passing command do not prove remote CI ran or that branch protection requires it.
Do not change remote branch protection, publish, or deploy without authorization for that action.

## Official References

- [Project memory and path-scoped Rules](https://code.claude.com/docs/en/memory)
- [Hook events, inputs, outputs, and continuation behavior](https://code.claude.com/docs/en/hooks)
- [Permissions and project settings](https://code.claude.com/docs/en/settings)
- [Skill installation and supporting files](https://code.claude.com/docs/en/skills)

Consult the selected framework's official documentation for build, testing, dependency, and configuration details.
Do not copy version pins from an unrelated example stack.

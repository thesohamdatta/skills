# UI Behavior Preservation During Refactoring — Mia Experiment

**Status:** proposed experiment; no upstream skill change approved  
**Owner:** Soham Datta  
**Upstream reference:** [mattpocock/skills#923](https://github.com/mattpocock/skills/issues/923)  
**Test platform:** [thesohamdatta/Mia](https://github.com/thesohamdatta/Mia), driven by local Gemini CLI  
**Harness candidate:** [adewale/skill-eval-harness](https://github.com/adewale/skill-eval-harness), reviewed commit `0cc7392473c3d8e2e7de7412239ba39229101604`

## Problem

Issue #923 reports a refactor that removed a conditional-render branch and prop passthrough while types, tests, and code review remained green. The upstream maintainer asks whether rendered-behavior verification belongs in `improve-codebase-architecture` or `implement`, or whether this is a project-test gap.

Do not assume a screenshot/accessibility gate is the answer. Test whether the smallest useful instruction/checkpoint catches observable regressions at acceptable context, time, and false-positive cost.

## Decision

Compare: **A** no change; **B** a concise implementation/TDD checkpoint; **C** an architecture-stage behavior contract plus post-change check; **D** a length-matched neutral reminder; optionally **E** a short on-demand reference. Select no intervention if benefits do not beat cost. Do not impose screenshots/a11y checks on every refactor by default.

## Platform and isolation

Mia is a local-first Bun/TypeScript engineering CLI, not a rendered app. Use Mia as the orchestrator/evidence recorder; test visible behavior in a UI-bearing companion target selected from repository evidence (e.g. Mia-Lens or a minimal fixture). Record exact repositories and commit SHAs.

Keep separate clean worktrees for Mia, UI target, and a scratch oracle fixture. This fork contains only the protocol/spec and tracking tickets until the evidence justifies a candidate change. No production edits, deployments, pushes, PRs, live user data, secrets in logs, or automatic upstream writes in the setup phase.

## Questions and hypotheses

- Can an independent UI oracle catch a lost conditional branch or prop passthrough that code-only checks miss?
- Does placement in implementation/TDD, architecture, or an on-demand reference best prevent the regression?
- Does the semantic instruction beat both baseline and a length-matched neutral control?
- Are DOM/ARIA assertions enough, or does a visually meaningful invariant require screenshots?
- What is the incremental cost in tokens, tool calls, latency, retries, human intervention, and diff size?

H1: A behavior contract plus post-change assertions catches a seeded regression missed by baseline checks. H2: A short conditional instruction is as effective as a universal gate at lower cost. H3: Architecture-stage value is distinct only if it preserves the contract before edits, with post-change verification still required. H4: If effects do not beat costs on held-out cases, recommend no upstream patch.

## Experimental arms

- **A Baseline:** current workflow and ordinary repo checks.
- **B Implementation:** minimal conditional behavior-preservation checkpoint for UI-affecting refactors.
- **C Architecture:** record the visible behavior contract before candidate selection and verify after implementation.
- **D Length control:** neutral reminder matched approximately to B's token count.
- **E On-demand reference:** optional short trigger pointer with details loaded only when relevant.

Keep model, Gemini CLI version/settings, prompts, target revision, tool permissions, budgets, and environment constant. Randomize run order where feasible; blind the evaluator and do not expose mutation/answer keys to the agent.

## Oracle qualification — mandatory before comparison

Create a minimal isolated UI fixture with (1) a meaningful conditional state, and (2) a prop passthrough that changes visible or accessible behavior. Define the behavior contract and assertions independently of agent outputs. Require all of:

1. Known-good baseline passes.
2. Removing the conditional branch fails the intended behavior assertion.
3. Dropping prop passthrough fails its own assertion.
4. Mutations fail for behavior, not merely because of compilation or startup errors.
5. Restoring the baseline passes.
6. Unchanged repeated captures/assertions are stable; distinct states produce distinct evidence.

Prefer browser/DOM and web-first accessibility assertions. Use ARIA snapshots when semantics matter. Use screenshots only for meaningful visual behavior not covered by semantic assertions. Record browser/runtime, viewport, theme, state, seed, and stabilization strategy. If any gate fails, stop and repair the oracle.

## Real-environment phase

Use Mia + local Gemini CLI for three task types: a controlled known-regression fixture; one bounded behavior-preserving refactor drawn from actual target source/history; and a negative-control refactor with no relevant UI effect. If a valid UI target cannot be justified, report a harness-only result and do not claim it resolves #923.

Pilot: 3 development cases, 2–3 repeats per arm; hold back at least two cases for blind validation. This is an engineering pilot, not a claim of statistical significance. Expand only if oracle and telemetry are sound.

## Roles

- Human coordinator: approves target, protocol, run/stop, and final decision.
- Gemini CLI driver: runs one approved task/arm; captures settings, trace, tools, no scope drift.
- Oracle author: defines assertions/mutations before seeing outcomes where possible.
- Evaluator: scores against a frozen rubric, blind to arm where practical.
- Auditor: reviews diffs, contamination, missing telemetry, invalid runs, and whether conclusions follow from evidence.

If one person performs multiple roles, report the lack of evaluator independence.

## Required per-run record

Write one JSONL record per run with experiment/task/arm/repeat/order; Mia/target/harness SHAs; Gemini CLI version/model/settings; prompt and skill hashes; clean-baseline status; start/end timestamps; classification (PASS/FAIL/BLOCKED/NOT RUN/INCONCLUSIVE); specific oracle assertion; escaped/caught/false-alarm/missed regression; code-check results separately from UI results; input/output/total token telemetry if provided; tools/subprocesses/time/interventions/retries; changed files/lines/reverts/collateral edits; artifact paths and hashes; deviations and limitations.

Do not invent token usage: mark unavailable when CLI telemetry does not expose it. Label estimates as estimates. Store raw traces outside the project by default; redact secrets, environment and personal data. Do not commit bulky traces/screenshots. Keep only scrubbed metadata and a compact report in version control.

## Metrics and analysis

**Primary:** seeded regression recall/escape rate and oracle qualification result. **Secondary:** false alarms on negative controls; task completion and preservation of unaffected behavior; extra context and total tokens; tools, latency, reruns, human interventions, diff size, repeatability, reviewer agreement.

Separate compilation/type/test failures from behavior failures. Missing decisive evidence is invalid/inconclusive, not PASS. Do not credit an instruction that the agent never used. Distinguish detection, prevention, and task refusal. Compare B against D; inspect every negative-control diff; report raw per-task counts and uncertainty, not one composite score.

## Go/no-go

Proceed to a recommendation only when the oracle passes all qualification gates, cases/arms/prompts/revisions/budgets are frozen, raw data and analysis are reproducible, and claims trace to run evidence. Recommend an upstream patch only if a minimal intervention improves held-out outcomes without material false-positive/context/tool costs and fits the upstream scope. Otherwise, record no change or a Mia-local finding. No upstream PR without a separate human decision.

## Non-goals

No generic screenshot gate before evidence; no production Mia changes to fit a benchmark; no automatic deploy/commit/push/PR; no live-model spend before oracle qualification and human approval; no whole-book prompts; no synthetic-only claim that #923 is resolved.

## References

- [Upstream issue #923](https://github.com/mattpocock/skills/issues/923) · [Upstream SCOPE.md](https://github.com/mattpocock/skills/blob/main/SCOPE.md)
- [Mia benchmark plan](https://github.com/thesohamdatta/Mia/blob/master/docs/mia-benchmark/benchmark-plan.md) · [test cases](https://github.com/thesohamdatta/Mia/blob/master/docs/mia-benchmark/test-cases.md) · [metrics](https://github.com/thesohamdatta/Mia/blob/master/docs/mia-benchmark/metrics.md)
- [Gemini CLI headless JSON/stream-json](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/headless.md)
- [Pinned harness demo](https://github.com/adewale/skill-eval-harness/tree/0cc7392473c3d8e2e7de7412239ba39229101604/examples/demo-skill)
- [Software engineering books repo](https://github.com/thesohamdatta/AI-books-for-your-agents)

## First local action

Read Mia's `GEMINI.md` → `AGENTS.md`, its Mia benchmark README/plan/test cases/metrics, and only source paths needed for evidence. Do not edit code. Verify clean trees and record SHAs; identify and justify a UI-bearing target; inventory existing tools without installing; propose the fixture/oracle in <=1,200 tokens; wait for human approval before code edits, target mutations, or live-model spend.

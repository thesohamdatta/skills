# Agent handoff — Mia UI behavior preservation study

Read first:
- Experiment spec: https://github.com/thesohamdatta/skills/blob/research/mia-ui-behavior-preservation/docs/experiments/ui-behavior-preservation-mia.md
- Parent issue: https://github.com/thesohamdatta/skills/issues/1
- Upstream motivation: https://github.com/mattpocock/skills/issues/923

## Your job now

Use the local Gemini CLI only for read-only discovery and oracle planning. Mia is the local-first workflow orchestrator/evidence recorder; the UI-bearing target must be chosen from actual repository evidence.

1. In the Mia checkout, read `GEMINI.md` then `AGENTS.md`, the Mia benchmark README/plan/test cases/metrics, and only source paths needed for evidence capture.
2. Run `git status --short --branch` and `git rev-parse HEAD` in Mia and each candidate UI target. Preserve uncommitted work.
3. Inventory existing versions/tooling (Gemini CLI, browser test runner, available oracle tools). Do not install tools or dependencies yet.
4. Select one UI-bearing target and justify it with source/test paths. If none fits, stop and report BLOCKED; Mia itself is a CLI.
5. Draft a minimal oracle: known baseline, conditional-render assertion, prop-passthrough assertion, deliberate mutations, restore checks, stability and distinct-state checks.
6. Return a proposal of at most 1,200 tokens with evidence paths and unknowns. Wait for human approval before edits, real-product mutations, or model-spending comparisons.

## Evidence contract

Never call an unexecuted test PASS. Use PASS / FAIL / BLOCKED / NOT RUN / INCONCLUSIVE. Separate compile/type/test from observable UI behavior. Save one JSONL record per run plus raw CLI trace, exact command, commit SHA, tool calls, elapsed time, human interventions/retries, actual token telemetry if available, and artifact hashes. Label unavailable metrics NOT AVAILABLE. Redact secrets, environment and user data.

## Guardrails

No deployment, push, PR, or change to Matt Pocock's upstream repository. No production Mia edits during discovery. No live-model comparison before oracle qualification and human approval. Do not read whole books into the prompt; consult targeted concepts only. Keep always-loaded prompt text minimal and the detailed protocol in the spec.
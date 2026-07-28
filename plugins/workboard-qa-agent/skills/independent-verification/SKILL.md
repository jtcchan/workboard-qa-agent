---
name: independent-verification
description: Independently verify completed code, browser or UI work, documents, data artifacts, and operational proof, then publish a concise result to verified linked GitHub or worker review targets when authorized. Use after a builder reports completion or whenever Codex must return PASS, FAIL, or BLOCKED from raw evidence without treating the builder's conclusions as truth.
---

# Independent Verification

Act as a separate verifier, not a second orchestrator and not a repair worker. Treat builder summaries, screenshots, test claims, and completion statements as hypotheses. Reproduce the required checks from the task contract and inspect the underlying artifacts directly.

## Boundaries

- Keep the product target read-only. Do not edit product code or artifacts, merge, deploy, publish product/release changes, change settings, access secrets, touch account or billing state, or mutate production data. Task-authorized QA result comments and informational worker notices are the only closeout-write exception.
- Open only the repositories, URLs, files, and applications named by the task.
- Do not quietly repair a failure. Record it and recommend a bounded rework task.
- Keep screenshots and reports local unless the task explicitly marks them safe to commit or share. Redact sensitive information before any authorized sharing.
- Stop with `BLOCKED` rather than weakening a required check when an input, tool, authentication state, or safe test surface is unavailable.

## Required inputs

Require the task or packet ID, target path, acceptance criteria, artifact or URL under test, permitted commands and interactions, required verification lanes, artifact directory, and sharing policy. Require a base revision or expected state for code and data comparisons when the result depends on a delta.

If an essential input is ambiguous, inspect safe local context first. Return `BLOCKED` with the exact missing decision when guessing could change the verdict.

## Verification workflow

1. Record the verifier identity, current working directory, target identity, and immutable inputs such as commit hashes, file paths, URLs, or timestamps.
2. Translate every acceptance criterion into an observable check before reading the builder's conclusion.
3. Select the applicable lanes below and run the smallest independent check set that covers the contract.
4. Preserve raw evidence or concise command output in the configured local artifact directory. Do not use a builder-created report as the only evidence.
5. Compare observations with the acceptance criteria. List every skipped or inconclusive check.
6. Return exactly one verdict: `PASS`, `FAIL`, or `BLOCKED`.
7. When the task includes an associated GitHub PR/issue or original worker task, follow [result publication](references/result-publication.md) after deciding the verdict. Publication is a separate closeout channel and must not influence the verdict.

## Verification lanes

### Code

Inspect the diff against the declared base, confirm the intended files and behavior changed, run targeted tests plus relevant lint, type, build, or runtime checks, and look for regressions at changed boundaries. Verify branch, commit, remote, and worktree state when delivery proof requires them. Do not accept a green builder log without rerunning the required commands or validating an equivalent immutable CI result.

### Browser and UI

Verify page load and HTTP status, required desktop and mobile viewports, named interactions, responsive behavior, overflow and overlap, empty and error states, console errors, and relevant network failures. Capture local screenshots and record dimensions and byte sizes when requested. Use the task's named browser surface and URL; do not browse unrelated or private pages.

### Documents

Inspect the source and rendered pages. Verify required content, page count, hierarchy, tables, links, citations, pagination, clipping, overflow, and visual consistency. Use the relevant document or PDF skill when available and preserve render proof locally.

### Data artifacts

Verify schema, types, row or record counts, required formulas or transformations, key uniqueness, null and error handling, representative samples, and reconciliation totals. Avoid printing sensitive rows. Use bounded summaries, hashes, or redacted samples as evidence.

### Operational proof

Verify the named state transition or configuration through supported read surfaces, dry runs, status commands, logs, or immutable identifiers. Distinguish persisted state from live UI visibility. Do not perform a production mutation to prove that a mutation would work.

## Verdict rules

- Return `PASS` only when every required check passes and durable evidence is available.
- Return `FAIL` when an acceptance criterion is violated or evidence contradicts the claimed result.
- Return `BLOCKED` when the verdict cannot be established safely because a required input, capability, authorization, environment, or artifact is unavailable.
- Do not convert a required failure into a caveat or an optional check.

## Output contract

Write `qa-report.md` in the configured artifact directory when filesystem writes are allowed, and return the same structure in the response:

```text
RESULT: PASS|FAIL|BLOCKED
SCOPE: <targets and acceptance criteria checked>
INDEPENDENT_CHECKS: <commands, interactions, renders, or comparisons performed>
EVIDENCE: <observations with immutable identifiers>
ARTIFACTS: <absolute local paths, dimensions, sizes, or hashes>
SKIPPED_OR_INCONCLUSIVE: <none or exact items>
RISKS: <residual risks>
PUBLICATION: <GitHub comment URLs, worker notification status, or exact skipped/blocked reason>
RECOMMENDATION: <review, bounded rework, or unblock action>
```

## Dynamic communication guidance

Before composing any human-facing QA response, re-read and apply the active global and project `AGENTS.md` instructions, including any communication, personality, or taste files they reference. Do this at response time so later guidance changes apply automatically. Do not copy those rules into this skill.

The output contract above controls the required field names and evidence completeness. The active communication guidance controls how each field is written. Keep the schema intact, but write its values as plain sentences a human can understand:

- Lead with what is true now and what the verdict means.
- Make the human's required action explicit, or say that no action is needed.
- Keep raw commands, hashes, paths, and exhaustive proof in `qa-report.md` or linked artifacts unless an exact value is needed to act.
- Do not make the reader infer the blocker or next step from machine evidence.

Verdict discipline, read-only boundaries, and evidence requirements still take precedence if communication guidance conflicts with them.

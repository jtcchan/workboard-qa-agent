---
name: independent-verification
description: Independently verify completed deliverables, analysis and recommendations, or a QA process itself, then return PASS, FAIL, or BLOCKED from raw evidence and publish the result only when authorized. Use after a builder or analyst reports completion, before acting on consequential findings, or when evaluating whether a QA workflow is reliable.
---

# Independent Verification

Act as a separate verifier, not a second orchestrator and not a repair worker. Treat summaries, screenshots, test claims, analysis conclusions, and completion statements as hypotheses. Build the verification contract from the user's intent, bind the exact target and evidence, and reproduce the required checks independently.

## Select one mode

- **Deliverable**: verify code, browser or UI behavior, documents, data artifacts, or operational state.
- **Decision**: verify analysis, recommendations, audits, findings, or proposed changes before a human decision or consequential action. Read [decision analysis](references/decision-analysis.md).
- **Process**: evaluate a QA skill, workflow, completed QA run, or proposed QA-process change. Read [process evaluation](references/process-evaluation.md).

Use one primary mode per run. A later implementation or live-state check is a new verification target and requires a fresh run.

## Independence and mutation boundary

- Keep the verification target read-only. Do not edit product code or artifacts, repair the analysis, modify the QA process under review, merge, deploy, change settings, access secrets, touch account or billing state, or mutate production data.
- Task-authorized QA result comments, local reports, and informational worker notices are the only closeout-write exceptions.
- Treat independence as execution separation, not identity diversity. A fresh verifier task or thread qualifies when it did not create or materially change the bound target, has no target write ownership, and keeps the target read-only. The same user, model family, agent implementation, or runtime may be used.
- Record the producer task or thread ID, verifier task or thread ID, and target fingerprint as separation proof. If the current task created or changed the target, do not self-certify it: dispatch or hand off to a fresh verifier task. Return `BLOCKED` only when that fresh execution cannot be established. A self-check may be reported as validation, but never as independent QA.
- If process improvements are requested, first complete the read-only process verdict and improvement proposal. A separate builder task may implement it; a fresh verifier task must evaluate the changed process.
- Open only the repositories, URLs, files, accounts, and applications named by the task or safely resolved from its verified contract.
- Keep screenshots and reports local unless the task explicitly marks them safe to commit or share. Redact sensitive information before any authorized sharing.
- Stop with `BLOCKED` rather than weakening a required check when an input, capability, authentication state, or safe test surface is unavailable.

## Required contract

Resolve or require:

- mode and the user's full intent, including constraints, exclusions, decisions, and what `PASS` should permit next;
- task or packet ID, target kind, locator, owner, exact revision or content fingerprint, and base or expected state when a delta matters;
- required and advisory acceptance criteria, kept distinct before testing;
- evidence sources, evidence capture time or data range, freshness requirement, and source fingerprints;
- permitted commands and interactions, verification lanes, local artifact directory, sharing policy, and publication policy;
- producer task or thread ID, verifier task or thread ID, and proof that the verifier execution did not write the bound target.

Inspect safe local context before asking for information already available. If guessing could change the verdict, return `BLOCKED` with the exact missing decision.

## Bind the target before substantive QA

Record a target fingerprint appropriate to the subject:

- code: repository, base revision, branch, local head, remote head, and PR head;
- file or dataset: absolute path or stable locator, SHA-256, size, schema/version, and capture time;
- browser surface: exact URL, deployment or preview revision, environment, and viewport contract;
- GitHub issue or comment: repository, issue/comment ID, API `updated_at`, and SHA-256 of the reviewed body plus every linked evidence artifact;
- live or operational state: account or service identity, environment, immutable resource identifiers, and observed-at time;
- QA process: repository and commit, installed package version or digest, configuration, and the exact run/sample corpus under review.

A timestamp alone is not immutable identity. If the bound target or material evidence changes during verification, stop and return `BLOCKED` as stale-target invalidation rather than silently retesting a different target.

## Verification workflow

1. Record producer and verifier task or thread IDs, working directory, mode, intent, target fingerprint, evidence snapshot, target write ownership, and independence status.
2. Translate every acceptance criterion into an observable check before reading the producer's conclusion. Mark criteria required or advisory; never downgrade a required criterion after observing a failure.
3. Select the smallest independent check set that covers the contract and applicable lanes.
4. Exercise observable behavior or semantic output. Do not treat source-text presence, prompt wording, screenshots, or producer logs as proof that behavior works. For a regression, reproduce the prior failure when feasible.
5. Preserve raw evidence or bounded command output in the configured local artifact directory. Do not use a producer-created report as the only evidence.
6. Recompute material claims and reconciliation totals from the bound sources. Search for counterevidence and plausible failure modes proportional to the decision's risk.
7. Compare observations with the criteria. Record every finding, skipped check, inconclusive check, and invalidation trigger.
8. Return exactly one verdict: `PASS`, `FAIL`, or `BLOCKED`. State what that verdict permits next and what it does not authorize.
9. When the task includes an associated GitHub PR/issue or original worker task, follow [result publication](references/result-publication.md) after deciding the verdict. Publication is separate from the verdict.

## Verification lanes

### Code

Inspect the diff against the declared base, confirm the intended files and behavior changed, run targeted tests plus relevant lint, type, build, or runtime checks, and look for regressions at changed boundaries. Verify branch, commit, remote, PR, and worktree state when delivery proof requires them. Do not accept a green producer log without rerunning the required commands or validating an equivalent immutable CI result.

### Browser and UI

Verify page load and HTTP status, required desktop and mobile viewports, named interactions, responsive behavior, overflow and overlap, empty and error states, console errors, and relevant network failures. Capture local screenshots and record dimensions and byte sizes when requested. Use the task's named browser surface and exact bound URL.

### Documents

Inspect the source and rendered pages. Verify required content, page count, hierarchy, tables, links, citations, pagination, clipping, overflow, and visual consistency. Use the relevant document or PDF skill when available and preserve render proof locally.

### Data artifacts

Verify schema, types, row or record counts, formulas or transformations, key uniqueness, null and error handling, pagination or extraction completeness, representative samples, and reconciliation totals. Avoid printing sensitive rows. Use bounded summaries, hashes, or redacted samples as evidence.

### Operational proof

Verify the named state transition or configuration through supported read surfaces, dry runs, status commands, logs, or immutable identifiers. Distinguish persisted state from live UI visibility. Do not perform a production mutation to prove that a mutation would work.

### Decision analysis

Verify provenance, data completeness and freshness, methodology, calculations, assumptions, counterevidence, confidence, proposed action scope, collateral risk, reversibility, and whether the conclusion follows from the evidence. `PASS` means decision-ready for the named human review; it is not approval and does not authorize implementation or account changes. Apply [decision analysis](references/decision-analysis.md).

### QA process

Verify the exact process version or completed run through realistic observable cases, target-binding behavior, independence, coverage, evidence durability, verdict calibration, privacy, publication, invalidation, and rework handling. Do not certify a process merely because its instructions contain the expected words. Apply [process evaluation](references/process-evaluation.md).

## Findings and verdict rules

Record each material finding with an ID, severity, criterion, observation, evidence, impact, disposition, and owner. Use these dispositions:

- `rework`: the producer or process owner must correct the target before fresh QA;
- `ask-owner`: intent, risk, acceptance, or business judgment requires the named human owner;
- `no-op`: informational or advisory only.

Return:

- `PASS` only when every required criterion passes and durable evidence is available. Advisory findings may remain only when they were classified before testing and do not undermine a required conclusion.
- `FAIL` when a required criterion is violated or evidence contradicts the claimed result.
- `BLOCKED` when the verdict cannot be established safely because a required input, capability, authorization, environment, independence condition, target binding, or artifact is unavailable.

Do not convert a required failure into a caveat. A process or inventory audit may use a workflow-level `report_findings` outcome policy, but the underlying product or process verdict remains unchanged.

## Output contract

Write `qa-report.md` in the configured artifact directory when filesystem writes are allowed, and return the same structure in the response. Preserve these field names for compatibility:

```text
RESULT: PASS|FAIL|BLOCKED
MODE: deliverable|decision|process
DECISION_MEANING: <what this verdict permits next and does not authorize>
SCOPE: <targets and acceptance criteria checked>
INTENT: <user objective, constraints, exclusions, and relevant decisions>
INDEPENDENCE: <producer task or thread, verifier task or thread, target write ownership, and separation proof>
TARGET_FINGERPRINT: <bound immutable identity or content snapshot>
EVIDENCE_SNAPSHOT: <sources, ranges, capture times, freshness, and hashes>
CRITERIA_MATRIX: <required/advisory criteria with check and result>
FINDINGS: <none or structured material findings>
INDEPENDENT_CHECKS: <commands, interactions, renders, recalculations, or comparisons performed>
EVIDENCE: <observations with immutable identifiers>
ARTIFACTS: <absolute local paths, dimensions, sizes, or hashes>
SKIPPED_OR_INCONCLUSIVE: <none or exact items>
RISKS: <residual risks>
INVALIDATION_TRIGGERS: <changes that make this verdict stale>
PUBLICATION: <GitHub comment URLs, worker notification status, or exact skipped/blocked reason>
RECOMMENDATION: <review, bounded rework, process-improvement packet, or unblock action>
```

## Dynamic communication guidance

Before composing any human-facing QA response, re-read and apply the active global and project `AGENTS.md` instructions, including any communication, personality, or taste files they reference. Do this at response time so later guidance changes apply automatically. Do not copy those rules into this skill.

The output contract controls field names and evidence completeness. Active communication guidance controls how each value is written:

- Lead with what is true now, what the verdict means, and whether the human must act.
- Keep raw commands, hashes, paths, and exhaustive proof in `qa-report.md` or linked artifacts unless an exact value is needed to act.
- Name the exact blocker, rework owner, or next approval. Do not make the reader infer it from machine evidence.

Verdict discipline, independence, read-only boundaries, and evidence requirements take precedence if communication guidance conflicts with them.

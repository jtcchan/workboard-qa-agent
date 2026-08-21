# QA Process Evaluation

Use this reference when verifying a QA skill, workflow, completed QA run, or proposed QA-process change.

## Define the process target

Bind:

- subject kind: `process_definition`, `completed_run`, or `proposed_process_change`;
- repository and commit, installed package version or digest, configuration, prompts/instructions, tools, and runtime assumptions;
- the exact reports, evidence manifests, recent run sample, or evaluation corpus under review;
- the quality standard, required behaviors, known failure being addressed, and acceptable residual risk.

Do not generalize a verdict beyond the bound version and evaluated cases. A new process commit, configuration change, model/tool change, or material workflow change invalidates the verdict.

## Evaluation dimensions

Evaluate observable behavior across:

1. **Admission and independence**: required inputs, producer/verifier task separation, target write ownership, handoff proof, authority boundaries, dirty/shared-state handling, and fail-closed behavior. Do not require a different human, model family, or agent implementation when a fresh read-only verifier execution is proven.
2. **Target and evidence binding**: immutable identity, mutable-content fingerprinting, source provenance, freshness, exact-environment proof, and stale-result invalidation.
3. **Coverage and reasoning**: criterion-to-check mapping, risk-based lanes, recomputation, counterevidence, boundary and failure cases, and domain-specific acceptance.
4. **Verdict calibration**: no false pass from missing evidence, no failure hidden as a caveat, bounded blockers, and decision meaning appropriate to the mode.
5. **Evidence and publication**: reproducibility, artifact durability, redaction, privacy, idempotent publication, and separation of publication status from the verdict.
6. **Rework loop**: structured findings, clear owner/action, no quiet repair, target invalidation after changes, and fresh verification after rework.
7. **Efficiency and operability**: smallest sufficient check set, useful progress/liveness signals, resumability, and avoidance of unnecessary blockers or duplicated work.

## Behavior-based evaluation

Instruction text is not behavioral proof. Exercise the process through its public invocation or realistic simulated packets and inspect the resulting verdict, evidence, and side effects.

Use a bounded corpus that includes, when relevant:

- correct work with complete evidence: expected `PASS`;
- a required behavioral defect with convincing producer claims: expected `FAIL`;
- missing authentication, target binding, producer/verifier task separation, or evidence: expected `BLOCKED`;
- a changed commit, edited issue/comment, refreshed dataset, or changed configuration after dispatch: expected stale-target `BLOCKED`;
- a producer screenshot or source-text match without behavioral proof: must not pass;
- a mutable issue backed by fingerprinted analysis and data artifacts: may pass decision mode;
- an unsupported campaign recommendation, truncated export, wrong account, unreconciled totals, or protected converting term proposed for exclusion: expected `FAIL` or `BLOCKED` according to available evidence;
- an advisory limitation disclosed before testing that does not undermine required criteria: may pass with residual risk;
- a publication failure after a conclusive verdict: verdict stays unchanged while publication reports partial or blocked.

For a regression, preserve a case that fails under the previous process and passes only after the improvement. Keep live-model interpretation in development evaluation rather than flaky deterministic CI. Deterministic tests should validate machine contracts, normalized semantic outputs, fingerprints, manifests, and failure modes—not grep instruction source for expected phrases.

## Improvement packet

When defects are found, produce a bounded process-improvement packet containing:

```text
PROCESS_TARGET: <version or run>
FAILURE_MODE: <observable failure>
IMPACT: <escape, false block, unsafe action, or evidence loss>
ROOT_CAUSE: <process-level cause supported by evidence>
PROPOSED_CHANGE: <bounded change and owner>
REGRESSION_CASE: <input, expected behavior, and observable assertion>
MIGRATION_OR_COMPATIBILITY: <consumer or packet impact>
FRESH_QA_REQUIRED: yes
```

The verifier may write this proposal as a local QA artifact but must not edit the process under review. A separate builder implements accepted changes. A fresh verifier then evaluates the new commit and the preserved regression case.

For inventory-style audits, a workflow may route a conclusive `FAIL` report to human review instead of immediate rerun. This changes the workflow destination, not the QA verdict.

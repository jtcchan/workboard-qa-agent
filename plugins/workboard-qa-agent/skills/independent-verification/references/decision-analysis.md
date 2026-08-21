# Decision Analysis Verification

Use this reference when the target is an analysis, audit, findings package, recommendation, or proposed change rather than an already-implemented deliverable.

## Bind three distinct things

Do not collapse these into one target:

1. **Review surface**: the GitHub issue, comment, document, or ticket where the analysis and verdict are discussed.
2. **Evidence snapshot**: the raw exports, queries, source documents, account state, and calculation artifacts supporting the analysis.
3. **Proposed action**: the exact future change, its destination, scope, risk, reversibility, dependencies, and approval owner.

The review surface may be mutable. Fingerprint its reviewed content and every material evidence artifact. A later edit to the analysis, evidence, or proposed action invalidates the decision verdict.

## Verification contract

Verify:

- the user objective, decision to be made, excluded actions, and acceptance threshold;
- source identity, provenance, freshness, completeness, data range, timezone, currency, filters, sampling, pagination, and known omissions;
- methodology and whether each material claim is reproducible from named sources;
- calculations, joins, transformations, units, denominators, comparison periods, reconciliation totals, and uncertainty;
- assumptions, alternative explanations, counterevidence, and facts that would reverse the recommendation;
- confidence calibration: stronger conclusions require stronger and more complete evidence;
- each proposed action's target, scope, expected effect, collateral risk, reversibility, dependencies, protected cases, and verification plan;
- separation between observation, inference, recommendation, human approval, implementation, and post-change verification.

Create a claim ledger for every material conclusion:

```text
CLAIM_ID | CLAIM | SOURCE | INDEPENDENT_CHECK | RESULT | COUNTEREVIDENCE | REQUIRED_ACTION
```

Do not pass polished prose whose claims cannot be traced and recomputed. Do not fail an analysis merely for honestly bounded uncertainty when the contract permits a decision-ready recommendation with disclosed residual risk.

## Campaign and advertising analysis

When the recommendation could change an advertising account, also verify:

- exact brand, customer/account, campaign, ad group or asset group, network, market, and current enabled state;
- data mode, retrieval time, date range, attribution basis, conversion definitions, primary versus secondary goals, and tracking confidence;
- search-term or entity coverage, pagination, match semantics, existing exclusions, duplicates, and account/campaign/ad-group scope;
- independent recomputation of spend, clicks, conversions, conversion value, return on ad spend, cost per acquisition, and any threshold used;
- separation of branded, prospecting, competitor, informational, navigational, and product/category intent where it affects routing;
- converting or high-value terms, protected catalog/brand demand, valid receiving campaigns, product eligibility, and cross-campaign collateral effects;
- whether a negative, budget, bid, targeting, creative, or structure recommendation actually addresses the stated problem;
- whether low or zero reported conversions could instead reflect tracking, attribution delay, insufficient volume, feed/landing-page issues, or a mismatched comparison period;
- an explicit dry-run/change manifest and post-change readback plan for every proposed mutation.

Apply the active account or domain skill's stricter safety and scoping rules. Independent verification never creates authority to mutate an account.

## Verdict meaning

- `PASS`: the analysis is decision-ready for the named human reviewer. It does not approve the recommendation or authorize the proposed action.
- `FAIL`: a required claim, calculation, scope, risk control, or conclusion is wrong or unsupported and must be reworked.
- `BLOCKED`: the verifier cannot safely establish the conclusion because identity, evidence, freshness, completeness, capability, or required owner intent is missing.

After human approval and implementation, use a new deliverable or operational verification run against the exact applied change and live-state receipt.

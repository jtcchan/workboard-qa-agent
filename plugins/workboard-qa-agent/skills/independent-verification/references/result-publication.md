# QA Result Publication

Use this closeout only after the independent verdict and local report are complete.

## Inputs and policy

- Prefer explicit `github_pr` and `github_issue` URLs. Normalize legacy `pr_url` or repo-scoped issue numbers only when the repository is unambiguous.
- If no PR is recorded, discover an open PR only when repository plus head branch or head commit match the immutable target. Never guess between candidates.
- `qa_publish_to_github` accepts `auto`, `required`, or `never`.
- `qa_worker_notification_policy` accepts `always`, `on_failure_or_no_github`, or `never`.
- The packet's publication fields authorize only result comments and informational task notifications. They do not authorize code changes, reviews, approvals, merges, labels, issue closure, or artifact uploads.

## GitHub publication

1. Confirm each target belongs to the packet repository and inspect its current state. For a PR, verify the tested commit is the PR head or an explicitly declared immutable target. For an issue or comment used as the verification target, re-fetch its `updated_at` value and body hash and confirm they still match the bound fingerprint. A content mismatch invalidates the verdict and requires fresh verification; it is not merely a publication failure and is never a reason to retest another target silently.
2. Read existing comments for this marker:

   `<!-- workboard-qa-result:PACKET_ID:TARGET_ID -->`

3. Create a concise Markdown summary using the template below. Update or skip the matching comment rather than duplicating it. If editing is unavailable and the verdict changed, add one new comment that clearly says it supersedes the prior result.
4. Use a connected GitHub comment tool when available; `gh` is an acceptable authenticated fallback. Record the resulting comment URL. If no authorized write surface exists, return the exact target and prepared body for root reconciliation.
5. Never paste secrets, cookies, private logs, customer data, absolute local paths, or raw local screenshots. Do not upload local artifacts unless the packet explicitly marks them safe to share after redaction review. When no approved external report link exists, say `full report retained in local Workboard artifacts`.

```markdown
<!-- workboard-qa-result:PACKET_ID:TARGET_ID -->
## Independent QA: PASS|FAIL|BLOCKED

- Mode: `deliverable|decision|process`
- Target tested: `IMMUTABLE_TARGET`
- Verdict meaning: `WHAT_THIS_PERMITS_NEXT_AND_DOES_NOT_AUTHORIZE`
- Packet: `PACKET_ID`
- Criteria: SHORT_PASS_FAIL_SUMMARY
- Material findings: NONE_OR_BOUNDED_FINDINGS
- Residual risks / unsupported checks: NONE_OR_DETAILS
- Recommendation: REVIEW_OR_REWORK_OR_UNBLOCK_ACTION
- Full report: APPROVED_EXTERNAL_LINK_OR_LOCAL_REPORT_RETAINED

This comment reports independent verification only; it does not approve or merge the change.
```

For decision mode, use `This PASS means the analysis is decision-ready for human review; it is not approval and does not authorize the proposed change.` only for a pass. For `FAIL` or `BLOCKED`, state that the analysis is not decision-ready and that no proposed change is authorized. For process mode, state that the verdict applies only to the named process version and evaluated cases.

Post to both a verified PR and issue when both are explicitly associated and distinct. The PR is the implementation review record; the issue is the durable work contract.

## Original worker notification

Notify `worker_thread_id` when policy is `always`, or when policy is `on_failure_or_no_github` and the verdict is `FAIL`/`BLOCKED` or no GitHub comment was published. Use the app-native task-message surface when available.

The message must be informational:

```text
QA RESULT: PASS|FAIL|BLOCKED
MODE: deliverable|decision|process
PACKET: PACKET_ID
TARGET: IMMUTABLE_TARGET
MEANING: WHAT_THIS_PERMITS_NEXT_AND_DOES_NOT_AUTHORIZE
SUMMARY: BOUNDED_FINDINGS
REPORT: SAFE_LINK_OR_LOCAL_ONLY_PATH
NEXT LANE: review|ready|blocked
Do not implement changes from this notification. Wait until the Workboard root requeues or explicitly resumes this worker task.
```

Always send the ordinary completion callback to the root/source task. If direct worker notification is unavailable, include the intended worker task ID and prepared message in the root callback.

## Durable status

Keep publication state separate from the verdict:

- `qa_publication_status`: `not_required` or `pending` before closeout; then `published`, `partial`, `skipped`, or `blocked`
- `qa_github_comment_urls`: every created or updated result comment
- `qa_worker_notification_status`: `not_required` or `pending` before closeout; then `sent`, `skipped`, or `blocked`

A publication failure never changes `qa_result`. With `required`, root must satisfy publication before Donna review. With `auto`, record the exact safe reason when no destination or write tool exists.

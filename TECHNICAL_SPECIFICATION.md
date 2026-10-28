# Technical specification and implementation boundaries

**Cyrus Strategic Prediction Model · October 28, 2026**

This is a retrospective specification, not a claim that every described component was deployed. “Recorded” means documented in retained project evidence. “Requirement” describes a control or desired behavior, not proof of its implementation. The next architecture is unreleased and remains subject to the owner's approval.

## 1. Purpose and system boundaries

The intended system combined probabilistic forecasting with traceable decision support. It separated forecasting, external reference data, uncertainty, validation, display, and human decision authority. Multi-model assistance was intended to supply complementary implementation and review work without making any model the final authority on its own output.

The historical integration used a Python coordinator, provider-specific text adapters, a persistent local accounting ledger, structured reports, restricted project-control records, and a separate execution/review environment. Google Cloud Shell was used in owner-observed connection and coordinator runs. Credentials were stored through Secret Manager. Later checkpoint entries record cloud-ledger handoff and a worker/bridge arrangement; these do not establish a complete unattended production service.

The final private presentation was an intended product layer. A task tracker, a generated narrative, a saved result, and a successfully delivered private dashboard are different outputs and require different evidence. This package does not certify a live dashboard.

## 2. Component responsibilities

| Component | Responsibility | Historical evidence / limitation |
|---|---|---|
| Owner and coordinator interface | Set scope, authorize work, inspect results, make phase decisions | Owner-approved assignments and later stop instruction are recorded |
| Python coordinator | Build task prompts, dispatch initial independent calls, enforce handoffs | Standard-library prototype, offline tests, owner-observed live cycles recorded |
| Provider adapters | Translate bounded requests into provider API calls and normalize responses | Four text-generation adapter lanes recorded; provider capability is not inferred from the role name |
| Response validator | Check completion, report schema, byte limits, role/task declarations, and unsupported claims | Several actual rejections recorded, including defects in the integration itself |
| Ledger and cache | Reserve planning allowance, preserve uncertain costs, reuse completed responses | Persistent SQLite ledger and preserved recovery history documented; not provider billing enforcement |
| Artifact/execution layer | Inspect, materialize, and test returned source separately | Local execution recorded; external drafting calls lacked execution tools |
| Review layer | Examine correctness, edge cases, evidence, and acceptance boundary | Both useful findings and incorrect AI review findings are recorded |
| Control records | Track task lifecycle, work intervals, issues, dependencies, owner decisions | Restricted checkpoint, runner record, and control workbook were used |
| Presentation layer | Translate validated information into a readable private overview | Intended outcome; not certified as deployed by this documentation |

## 3. Historical request and handoff sequence

1. The owner or coordinator defined a bounded task with a shared contract and declared capabilities.
2. The coordinator checked local controls and reserved a conservative planning allowance before dispatch.
3. Four initial drafting lanes could run concurrently: data/provenance, implementation, evaluation, and display.
4. Each completed response was checked for provider completion and local schema compliance. Returned source remained inert text at this point.
5. Complete validated reports, not merely status summaries, were handed to the review stage.
6. A validated review unlocked the advisory integration stage. A missing or invalid prerequisite prevented the downstream call.
7. Generated implementation required separate local inspection, execution, meaningful tests, and review.
8. An authorized decision was required for acceptance and any phase advancement.

The pipeline supported explicit context passing, not automatic shared memory. Model-generated claims about what another participant did were not sufficient execution evidence. Recovery reused verified prior artifacts instead of paying to regenerate unchanged work.

## 4. Historical limits and revision behavior

The early four-provider pilot recorded a maximum of six requests per cycle, no automatic retries, and bounded prompt/output/report sizes. One revision used a 2,048-token output cap and a 6,000-byte report cap. A later corrective revision used larger request caps, a 10,000-byte report limit, and a prompt limit of 80,000 bytes.

These were revision-specific controls, not current defaults. Claude's small-task thinking/output configuration was adjusted after truncated results. An OpenAI response completed at the provider but exceeded the local report-size cap. A narrowly reviewed saved-response exception permitted reuse of the complete response; it did not silently truncate the evidence or erase the original rejection.

The reviewed system intentionally avoided blind automatic retry. Any future retry policy must preserve cost uncertainty, idempotency, limits, and provider-specific semantics. General advice to retry a transient service error is not permission to replay a paid request with an unknown outcome.

## 5. Report contract

The historical diagnostic contract used a bounded JSON report with these concepts:

| Field | Meaning |
|---|---|
| `task_id` | The assigned task, checked against the request |
| `status` | Draft/report state; not independent acceptance |
| `summary` | Bounded account of what the response contains |
| `deliverables` | Objects containing the required `name` and `content` fields |
| `limitations` | Missing capabilities, evidence, execution, or other relevant boundaries |
| `next_action` | Suggested next step, not authorization |
| `role_acknowledgment` | Declared role, requested model/provider, task, role version, and tool availability |
| `tests_executed` | Actual tests within that call's capability; empty when no execution occurred |
| `tests_proposed` | Tests supplied or suggested but not yet run |
| `sources_verified` | Actual source checks; empty when source access was unavailable |

In the diagnostic revision, each deliverable had to contain exactly the permitted name/content pair. A provider returning an HTTP-success response could still fail this application contract. The precise offending field in one rejected Claude response was not established because the raw failed response was unavailable.

Role acknowledgment is not proof of capability. Test source is not a test result. A draft's claim of verification is not a substitute for external evidence.

## 6. Lifecycle and evidence model

The following is a normalized documentation vocabulary, not an assertion that every historical module used identical enum names:

| State | Minimum meaning |
|---|---|
| PLANNED | Assignment exists; dispatch is not established |
| READY | Prerequisites and authority have been checked; execution has not necessarily started |
| ACTIVE | Real executor, unique job identity, and recorded start exist |
| DRAFT_RECEIVED | Provider returned a usable draft under the response contract |
| TESTED_LOCAL | The specified artifact was tested in a real local runtime |
| REVIEWED | Review evidence and findings exist; author/reviewer separation is recorded |
| ACCEPTED_LOCAL | Bounded local acceptance criteria were met; not a deployment claim |
| ACCEPTED_MANUAL | Owner approved a specifically bounded result under a manual policy |
| BLOCKED / REJECTED | A named prerequisite or acceptance condition is unmet |
| PAUSED_BY_OWNER | No production restart is implied by a schedule, old assignment, or documentation update |

Required evidence should bind the task, actual executor, execution context, request/job identity, artifact version, start/finish times, test command and result, review disposition, and acceptance authority. Artifact hashes help detect changes; they do not by themselves prove the truth or authority of the content.

Elapsed time requires a recorded start and finish. Waiting, idle, polling, blocked time, and owner review must be identified separately. Parallel intervals cannot be added and reported as human labor. A request assigned to one model but executed by a fallback must be credited to the fallback.

## 7. Validation layers

| Layer | Acceptance question | Example failure seen in the project |
|---|---|---|
| Connectivity | Can this environment authenticate and obtain a response? | Temporary service error; unavailable authenticated transport in a later runtime |
| Completion | Did generation finish with complete output? | Claude `max_tokens` stop |
| Schema | Does the report satisfy the application contract? | Invalid deliverable fields; report exceeds byte limit |
| Capability honesty | Does the report claim only operations actually available? | Unsupported execution/source claims or missing limitations |
| Implementation | Is the returned code correct under meaningful tests? | Invalid numeric inputs accepted; non-finite serialization |
| Review | Is criticism itself correct and independent? | An incorrect review finding repeated in an integration note |
| Delivery | Was the exact intended result delivered and visible? | Saved result lacked sufficient delivery/visibility evidence |
| Authority | Is acceptance based on trusted evidence and the right decision-maker? | Caller-controlled verifier and unbound readiness inputs |

A passing earlier layer never automatically passes a later one. A failed later layer does not retroactively prove authentication failed.

## 8. Data and decision-support requirements

- Maintain typed provenance and distinguish availability time, retrieval time, and valid time.
- Preserve the decision cutoff and reject future evidence in historical validation.
- Keep synthetic fixtures visibly distinct from verified historical observations.
- Validate probability bounds, finite numeric values, and strict JSON serialization.
- Keep an unavailable or stale external reference distinguishable from a usable reference.
- Do not silently convert missing evidence into a confident decision.
- Keep forecast estimates separate from decision policy; a single computed metric cannot bypass the policy gates.
- Require measured calibration and out-of-sample evaluation before making predictive-performance claims. No such claim is established by this documentation.

## 9. Cost, idempotency, and secrets

The approved combined initial allowance was $100 without renewal. The $5 cloud / $95 AI-and-other-API division was a planning allocation. Estimates and reservations remained separate from invoices. Failed or uncertain requests retained conservative reservations until evidence supported reconciliation.

Historical controls included cached completed work, no automatic retries in the reviewed coordinator, preserved failed rows, and recorded recovery relationships. A new job identity was not permission to forget earlier spending. The ledger did not cover every independent program, account, cloud service, or tax.

Credentials belonged in managed secret storage, not source files, prompts, screenshots, or public logs. Public documentation must remove account/project identifiers and private endpoints. A historical successful credential test is not proof that a different runtime has the same authenticated transport.

## 10. Reassignment and repair policy

The owner-approved recovery plan removed unavailable or repeatedly nonproductive lanes from the critical path. Gemini's executable provenance work moved to an OpenAI worker. Claude's executable delivery-acceptance work moved to ChatGPT/Codex. Muse's later missing dispatch evidence triggered reciprocal review between the two executable lanes.

Controls required one active claim per executor, a concrete artifact and same-day target, a recorded fallback, and exact attribution. The repair policy escalated after repeated unsuccessful passes; a third failure required an owner decision instead of indefinite repair loops.

This policy produced local artifacts but did not solve every system-level trust or delivery problem. Automated delivery acceptance and automated gate-readiness candidates subsequently failed additional review. Manual acceptance was approved for a limited result; the rejected automated mechanisms remained rejected.

## 11. Requirements to consider for the replacement plan

These are lessons for the owner to evaluate, **not the unreleased replacement architecture or approved implementation work**:

1. Qualify each model on a representative complete task, not just a readiness reply.
2. Separate drafting, execution, review, and acceptance in both UI and logs.
3. Give an executable task only to an environment with the required tools and authority.
4. Define a small end-to-end demonstration with a visible result before expanding the team.
5. Preserve complete failure evidence, including stop reason and validator class, while redacting secrets.
6. Keep deterministic orchestration in code; use models for bounded reasoning and artifact contributions.
7. Test reviewers with known defects and known-correct cases, and prohibit self-acceptance.
8. Bind readiness claims to authoritative task and job-state snapshots rather than caller-supplied assertions.
9. Make fallback measurable and timely without disguising blocked work as activity.
10. Release a revised plan only after the owner's other two projects are tested and completed, then obtain explicit restart authority.

## 12. Release boundary

There are no runtime commands in this package because runtime code and configuration are not included. Reconstructing an installer from this narrative would not reproduce the verified historical environment. Before a later code release, the owner must confirm source/version completeness, licensing, sanitized fixtures, reproducible tests, current provider configuration, and destination visibility.

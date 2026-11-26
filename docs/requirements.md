# Project definition and requirements

[Documentation index](index.md) · [Architecture and models](architecture.md) · [Case study](case-study.md)

## 1. Project introduction and background

I built Sapotoi as a personal initiative during my previous employment. It is a web console for finding operational records, managing messaging channels, and following jovbs that provision or register channels. My colleagues and I used the original application. That use helped simplify repetitive work, but I have no quantified Sapotoi outcome to cite.

A **premise** is a site record in the directory. A **zone** groups premises under a server record. A **channel** is managed on Telegram, not in the directory database. **Provisioning** creates a channel; **registration** adds the channel ID to a separate administrative web application. Those are distinct actions.

## 2. Problem statement

A support task could start with a directory lookup, continue in a messaging service, and end in an administrative web interface. Operators had to change tools and keep track of matching identifiers. A batch could apply some changes even if others failed. Launching a job was therefore not enough: the operator also needed to see which steps had run and what was reported.

## 3. Project objectives and scope

| Objective inferred from the application | Scope of the original design |
| --- | --- |
| Bring related work into one console | Directory search and supported maintenance, Telegram administration, and a dashboard for channel counts, account status and creation activity. |
| Reduce repeated entry | Start a batch from a zone-to-channel mapping and write successful creation results to a worksheet. |
| Make registration a visible operator action | Start registration separately for selected zones; monitor progress in the console while Selenium interacts with a separate web application. |
| Preserve useful operational context | Record selected directory and channel actions and show job messages and individual outcomes where available. |

The scope is the reviewed original application, not a complete infrastructure monitoring platform. The application did not establish managed user roles, durable job history, or exactly-once processing. Decorative dashboard elements were not operational telemetry. The software shown in these documents is not bundled here. The separate [standalone proposal](portfolio-edition-plan.md) is not implemented.

## 4. Functional requirements

These nine statements are reconstructed from the reviewed implementation, based on the IDs in the diagram pack. They describe what the product was built to do. They are not evidence of a historical acceptance sign-off.

| ID | Retrospective functional requirement | Feature and original behavior |
| --- | --- | --- |
| FR-01 | Browse and search the server, zone and premise directory. | Directory view loads the hierarchy, supports selection and filtering, and excludes soft-deleted children in the normal collection. |
| FR-02 | Maintain supported directory records and view audit activity. | Selected operations create directory records, update a zone link or soft-delete a zone and its premises. Recent changes use database audit records. No restore flow is shown. |
| FR-03 | Browse channels and perform supported Telegram administration actions. | Channel workspace lists and searches channels, shows administrators, and supports creation, rename, bot changes and leaving a channel. Cache refresh has limits. |
| FR-04 | View channel counts, account status and creation activity. | Dashboard shows those three views. Creation counts come from audit data, not a complete reconciliation with Telegram. |
| FR-05 | Submit a batch of channel requests and see item outcomes. | The pipeline accepts a mapping and attempts channel creation and bot promotion, including account fallback for selected conditions. |
| FR-06 | Append successful provisioning results to a worksheet. | The pipeline attempts a separate Google Sheets write after creation. This neither rolls back existing channels nor guarantees all requested rows were written. |
| FR-07 | Start a separate browser registration run for selected zones. | Live Monitor loads zones, starts a separate Selenium task and attempts registration of matching channel IDs in a web application. |
| FR-08 | Observe job activity through live log streams. | The job views use server-sent events (SSE). Retention, isolation, reconnect behavior and status handling are incomplete. |
| FR-09 | Unlock selected administrative UI actions. | A shared administrator unlock enables selected controls; the backend checks a bearer token on certain operations. This is not a per-user role model or a complete API policy. |

### Requirements traceability

The views show where a requirement is represented, not that it passed a test. The last column is proposed acceptance evidence for a future isolated environment. **None of FR-01–FR-09 was runtime-tested in the offline review.** See the [verification record](case-study.md#14-testing-and-verification) for the checks that were run.

| ID | Feature path | Relevant diagrams | Suggested acceptance check; not run |
| --- | --- | --- | --- |
| FR-01 | Directory → hierarchy query and search | [D01](architecture.md#d01-operator-use-cases), [D03](architecture.md#d03-browser-and-backend-components), [D08](architecture.md#d08-directory-and-audit-model) | Select a seeded premise and confirm its parent server and zone; soft-deleted rows stay out of the list. |
| FR-02 | Directory forms and audit panel → directory handler and audit table | [D01](architecture.md#d01-operator-use-cases), [D07](architecture.md#d07-regular-http-and-job-log-streams), [D08](architecture.md#d08-directory-and-audit-model) | Submit a supported update; verify the resulting row and audit entry separately. |
| FR-03 | Channel workspace → channel service → Telegram | [D01](architecture.md#d01-operator-use-cases), [D02](architecture.md#d02-sapotoi-in-context), [D03](architecture.md#d03-browser-and-backend-components) | Use a mock channel account and confirm the list reflects an applied change after refresh. |
| FR-04 | Dashboard → account checks, counts and audit aggregation | [D01](architecture.md#d01-operator-use-cases), [D03](architecture.md#d03-browser-and-backend-components), [D08](architecture.md#d08-directory-and-audit-model) | Compare dashboard values with controlled account and audit fixtures. |
| FR-05 | Pipeline editor → batch background task | [D04](architecture.md#d04-batch-provisioning-activity), [D06](architecture.md#d06-batch-provisioning-sequence) | Submit a valid batch with a simulated partial failure; distinguish outcomes for each item. |
| FR-06 | Batch task → Sheets write of successful results | [D02](architecture.md#d02-sapotoi-in-context), [D04](architecture.md#d04-batch-provisioning-activity), [D06](architecture.md#d06-batch-provisioning-sequence) | Inspect mock worksheet rows; simulate failure and confirm it is not reported as successful. |
| FR-07 | Live Monitor → worksheet → Selenium and portal | [D01](architecture.md#d01-operator-use-cases), [D02](architecture.md#d02-sapotoi-in-context), [D05](architecture.md#d05-browser-registration-activity) | Start registration explicitly and verify the mock processes the selected zones only. |
| FR-08 | Job views → SSE stream → process-local log data | [D03](architecture.md#d03-browser-and-backend-components), [D06](architecture.md#d06-batch-provisioning-sequence), [D07](architecture.md#d07-regular-http-and-job-log-streams) | Observe progress and a terminal event without treating the terminal event as proof of every side effect. |
| FR-09 | Unlock modal → selected UI controls and protected requests | [D01](architecture.md#d01-operator-use-cases), [D07](architecture.md#d07-regular-http-and-job-log-streams) | Confirm selected controls change state and guarded API actions reject missing or invalid tokens; check policy gaps separately. |

## 5. Non-functional requirements and quality considerations

No original signed-off quality targets were established by the review. The table describes observable design choices, trade-offs and possible future verification rather than measured compliance.

| Quality concern | Original design and known limits | Future verification or improvement |
| --- | --- | --- |
| Security and privacy | Selected operations check a token and normal directory views exclude credential fields. UI unlock and API protection do not align on all operations; no per-user identity was shown. | Define and test a consistent API policy; use synthetic data and never return operational credentials in a portfolio demo. |
| Reliability and recoverability | Jobs report outcomes and selected account failures can trigger fallback. External side effects are separate; job logs and state are process-local. | Test partial failures, reconciliation and restart behavior with persistent run records and safe retries. |
| Maintainability | Typed frontend and backend models and feature-specific modules help separate concerns. Legacy files still fail full TypeScript checking. | Gate releases on type checking and targeted automated tests; remove or repair inactive paths. |
| Usability and accessibility | Search, loading states, confirmation dialogs and live logs are present in source. Keyboard and mobile behavior were not runtime-verified in the review. | Check focus, keyboard navigation, responsive layouts and visible error/retry states. |
| Performance | Directory records are loaded as a hierarchy and channel data has a process-local cache. The offline build emitted a large-chunk warning. No runtime latency or throughput was measured. | Measure representative load, test cache freshness, and consider route splitting based on the measurements. |
| Deployability | Static frontend artifacts and a container API have separate delivery paths; Compose also describes a Selenium container. | Validate a reproducible isolated release and rollback with synthetic data; do not infer live routing or availability from config. |
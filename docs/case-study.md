# Sapotoi case study

[Documentation index](index.md) · [Architecture and workflows](architecture.md) · [Standalone edition proposal](portfolio-edition-plan.md)

I built Sapotoi independently as a personal initiative during my previous employment. For the operational problem and the original scope, see the [project definition](requirements.md).

## 14. Testing and verification

My colleagues and I used the application during my previous employment. That is my firsthand account of historical use, not a claim that every feature worked or that the original deployment is currently available.

On November 26, 2026, an offline review checked a local snapshot of the original source. It did not connect to operational systems or rerun the workflows my colleagues and I used. The results below are about the original snapshot, not about the future standalone edition.

| Check | Result |
| --- | --- |
| Frontend production build | Passed in a scratch copy with installed dependencies and no original environment files. Vite reported a large JavaScript chunk. |
| Full frontend typecheck | Failed with 13 diagnostics in inactive frontend code. A successful bundler build did not establish a clean typecheck. |
| Backend syntax | Passed for 27 Python files. No application modules were imported and no database was contacted. |
| Docker Compose configuration | Passed validation with synthetic settings. No images were built and no containers started. |
| Tests, lint, and end-to-end workflows | No project test suite or lint command was found in the reviewed snapshot. No UI workflow or external integration was runtime-tested. |

The [traceability matrix](requirements.md#requirements-traceability) lists proposed acceptance checks in an isolated environment. They were not part of the offline review.

**Documentation verification:** This portfolio was checked locally for relative links and fragments, diagram rendering and sensitive information. The UML use-case figure is a supplied SVG; its editable PlantUML source was checked for structure and matching labels, not recompiled. These checks do not exercise the original application.

## 15. Technical challenges and engineering decisions

| Challenge | What I built and how I would improve it |
| --- | --- |
| The same channel could appear under multiple accounts. | I merged channel listings by channel ID and retained account associations. That made navigation simpler, but membership changes can leave the process cache stale. I would make refresh sequencing explicit and test the actual list returned after a mutation. |
| A batch could hit account limits or partly succeed. | I handled selected Telegram rate and capacity conditions by moving to another configured account and kept item-level outcomes. Channel creation, audit writes and spreadsheet writes were separate effects. I would use durable run records and retry only the failed or unconfirmed step. |
| Creation and registration had different interfaces. | I used Telethon for channel provisioning and Selenium for portal interaction. The sheet was a handoff between separate operator actions, not an automatic trigger. I would verify the saved result before calling a registration successful and make browser cleanup unconditional. |
| A job could outlast the initiating HTTP request. | I returned from the trigger request and sent progress over a separate SSE connection. This fit one-way log delivery, but the original log buffers were process-local. I would introduce per-run event IDs, ownership and replay before adding more workers. |
| The frontend and backend shipped as different artifacts. | I used a tagged static build for the SPA and an image build for the API. I would add independent typecheck and test gates and use immutable release references with a documented rollback. |

## 16. Limitations and future improvements

I would first put a consistent authorization policy around sensitive actions and job streams, and stop delivering operational credentials to the browser. I would then introduce per-run state, typed progress events and an idempotent retry path. On the frontend I'd remove inactive modules from the build or fix them, gate releases on type checking and tests, split heavy views, and verify keyboard and mobile use. The dashboard's sample visuals should stay clearly separate from live activity metrics.

My next portfolio step is a standalone demonstration with fresh code and fictional data. It should prove the workflow and its failure handling without exposing the systems the original application worked with. See the [proposed durable job lifecycle](portfolio-edition-plan.md#d10-proposed-durable-job-lifecycle).

## 17. Personal contributions and AI-assisted development

I independently developed the original application and own its code. My work covered the interface, backend, integrations and release configuration.

- I built a React and TypeScript single-page application with a searchable directory, a channel workspace, and views for starting and monitoring jobs. Feature hooks handled selections, forms, and workflow state; RTK Query handled API requests and cached server data.
- I built the FastAPI backend and modeled the server–zone–premise hierarchy in SQLAlchemy. I separated normal directory responses from credential reads, recorded changes in audit tables, and used soft deletion so removing a zone from the normal view did not immediately erase its premises.
- I integrated Telegram through Telethon. The backend merged channel lists across accounts, supported administrative actions, and reported individual bot-operation results. I also built dashboard views for channel counts, account health, and recorded channel creations.
- I built two automation paths. Batch provisioning created Telegram channels and wrote successful results to Google Sheets. The browser registration workflow consumed the worksheet separately and used Selenium to interact with an administrative UI. I streamed logs to the SPA so the operator could follow a run without relying on a browser viewer.
- I configured GitLab CI delivery for a static frontend artifact and a Dockerized API with a Selenium sidecar. The frontend pipeline checked version tags against the package version. The backend image and Compose configuration kept the API runtime separate from the browser process.

### How I used AI assistance

I used OpenAI Codex and Claude extensions as coding aids while developing Sapotoi. I was the sole developer and responsible for the application's design and the code I chose to use. I don't attribute specific modules to either assistant, and I don't have figures to cite about their effect on productivity or quality. Neither tool was part of the application's runtime.

For the proposed standalone edition, I would start with acceptance tests and synthetic examples. Any AI-generated change would be reviewed against those tests and the integration boundaries before I accepted it. That is my proposed practice for the demo, not a claim about historical test coverage.

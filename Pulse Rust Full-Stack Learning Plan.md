# Pulse: Rust Full-Stack Learning Plan

Oct 18, 2026 · @Mukesh Kumar

Pulse is an uptime and log monitor with AI incident summaries, and building it teaches full-stack Rust (UI, backend, Lambda, data logging, CI/CD) in about 19-22 weeks of evenings and weekends, plus an optional Leptos phase.

## Overview

- **Why Pulse:** scheduled Lambda checks ping URLs, results become time-series data, an Axum API serves a Dioxus dashboard, and an LLM explains why a service went down.
- **How to use this plan:** each phase lists only the concepts you need, each with its best doc link. Tick them off as you go, and ask Claude to dig deeper whenever a concept feels shaky.
- **No generated code:** use AI as tutor, reviewer and error translator, not as the author.
- **Links:** written from memory. Some may have moved, and Dioxus docs are versioned, so use the version matching your install.

## Stack

| Layer | Choice | Note |
| --- | --- | --- |
| Frontend | Dioxus (Leptos optional later) | React-like model, so your 5 years transfer |
| Backend | Axum on Tokio | Separate API, not hidden behind server functions |
| Database | Postgres via SQLx | Compile-time-checked queries |
| Scheduled work | cargo-lambda + AWS SDK for Rust | EventBridge triggers, SQS fan-out |
| Observability | `tracing` + OpenTelemetry | Instrument Pulse itself |
| Infra and CI | Terraform/OpenTofu, GitHub Actions | Config languages, not Rust; learn them as concepts |

## Architecture decision

Use Dioxus only as the frontend and keep a separate Axum API. Dioxus fullstack is built on Axum, but hiding the backend behind server functions would skip the backend learning you want.

## Phase 0: Just-in-time Rust (2 weeks)

Learn only the Rust you will use in Pulse, starting with ownership and ending with async.

- [ ] Ownership, borrowing, slices: [Rust Book ch4](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html)
- [ ] Structs, enums, pattern matching: [Rust Book ch5-6](https://doc.rust-lang.org/book/ch05-00-structs.html)
- [ ] Modules, crates, Cargo workspaces: [ch7](https://doc.rust-lang.org/book/ch07-00-managing-growing-projects-with-packages-crates-and-modules.html), [workspaces](https://doc.rust-lang.org/book/ch14-03-cargo-workspaces.html)
- [ ] Error handling (`Result`, `?`): [ch9](https://doc.rust-lang.org/book/ch09-00-error-handling.html)
- [ ] Generics, traits, lifetimes: [ch10](https://doc.rust-lang.org/book/ch10-00-generics.html)
- [ ] Smart pointers (`Box`, `Rc`, `Arc`): [ch15](https://doc.rust-lang.org/book/ch15-00-smart-pointers.html)
- [ ] Threads, `Send`/`Sync`: [ch16](https://doc.rust-lang.org/book/ch16-00-concurrency.html)
- [ ] Async: futures, polling, executors: [Async Book](https://rust-lang.github.io/async-book/), [Tokio tutorial](https://tokio.rs/tokio/tutorial)
- [ ] Serialization: [serde.rs](https://serde.rs/)

**Video:** Jon Gjengset's [Crust of Rust playlist](https://www.youtube.com/@jonhoo), especially the lifetimes and `Send`/`Sync` episodes.

**Build:** a CLI that checks one URL and prints status and latency.

## Phase 1: Networking fundamentals (1-2 weeks)

Understand what happens between your code and a remote server before you build anything on top of it.

- [ ] DNS resolution: [Cloudflare: What is DNS](https://www.cloudflare.com/learning/dns/what-is-dns/)
- [ ] TCP and sockets: [Beej's Guide](https://beej.us/guide/bgnet/)
- [ ] TLS handshake: [MDN: TLS](https://developer.mozilla.org/en-US/docs/Web/Security/Transport_Layer_Security)
- [ ] HTTP semantics, status codes, headers: [MDN: HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- [ ] Timeouts, retries, backoff with jitter: [AWS Builders' Library](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/)
- [ ] Tokio tasks and channels: [Tokio tutorial](https://tokio.rs/tokio/tutorial) (the mini-redis project)
- [ ] HTTP client in Rust: [reqwest docs](https://docs.rs/reqwest)

**Build:** a concurrent multi-URL checker with timeouts and retries.

## Phase 2: Backend with Axum (3 weeks)

Build the Pulse API, learning how a Rust web service is layered from routing down to the `Service` trait.

- [ ] Routing, extractors, handlers: [Axum docs](https://docs.rs/axum), [examples](https://github.com/tokio-rs/axum/tree/main/examples)
- [ ] The `Service` trait and middleware layers: [Tower](https://docs.rs/tower), [Inventing the Service trait](https://tokio.rs/blog/2021-05-14-inventing-the-service-trait)
- [ ] Shared state: Axum docs ("State")
- [ ] REST API design: [Microsoft API design guide](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)
- [ ] Password hashing: [OWASP Password Storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- [ ] Sessions vs JWT: [OWASP Sessions](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html), [jwt.io intro](https://jwt.io/introduction)
- [ ] Input validation, error-to-response mapping: [validator crate](https://docs.rs/validator)
- [ ] Twelve-factor config: [12factor.net](https://12factor.net/)

**Book:** [Zero To Production in Rust](https://www.zero2prod.com/) is the best single resource for this phase.

**Build:** an API for users, monitors, and check results.

## Phase 3: Database and data modeling (2 weeks)

Store check history so it can be queried fast, which forces you to learn indexes, query plans and time-series patterns.

- [ ] Indexes (B-trees, composite indexes): [Use The Index, Luke](https://use-the-index-luke.com/)
- [ ] Transactions, isolation, MVCC: [Postgres tutorial](https://www.postgresql.org/docs/current/tutorial-transactions.html), [MVCC](https://www.postgresql.org/docs/current/mvcc-intro.html)
- [ ] Reading query plans: [EXPLAIN](https://www.postgresql.org/docs/current/using-explain.html)
- [ ] Time-series: partitioning, retention, rollups: [Partitioning](https://www.postgresql.org/docs/current/ddl-partitioning.html)
- [ ] Connection pooling: [Postgres wiki: connections](https://wiki.postgresql.org/wiki/Number_Of_Database_Connections)
- [ ] Migrations, compile-time-checked queries: [SQLx](https://github.com/launchbadge/sqlx), [docs.rs](https://docs.rs/sqlx)

**Build:** persist check history and query uptime % over time windows.

## Phase 4: Observability (1-2 weeks)

This is the "data log" phase: instrument your own backend so you can debug Pulse with the same kind of data Pulse collects.

- [ ] Logs vs metrics vs traces: [OpenTelemetry concepts](https://opentelemetry.io/docs/concepts/)
- [ ] Spans, structured events: [tracing crate](https://docs.rs/tracing), [Tokio tracing guide](https://tokio.rs/tokio/topics/tracing)
- [ ] What to alert on: [Google SRE: Monitoring](https://sre.google/sre-book/monitoring-distributed-systems/)

**Build:** instrument your backend with request IDs, spans, and structured logs.

## Phase 5: Frontend with Dioxus (3 weeks)

Dioxus mirrors React's render model, so start from how React renders and then learn what changes in Rust and WebAssembly.

- [ ] How React renders (the model Dioxus mirrors): [React: Render and Commit](https://react.dev/learn/render-and-commit)
- [ ] Components, `rsx!`, signals, hooks: [Dioxus docs](https://dioxuslabs.com/learn/), [examples](https://github.com/DioxusLabs/dioxus/tree/main/examples)
- [ ] Virtual DOM diffing: Dioxus docs (internals/architecture section)
- [ ] WebAssembly: what it is, how Rust compiles to it: [MDN: Wasm concepts](https://developer.mozilla.org/en-US/docs/WebAssembly/Guides/Concepts), [wasm-bindgen guide](https://rustwasm.github.io/docs/wasm-bindgen/)
- [ ] Routing, async data fetching: Dioxus docs
- [ ] Rendering strategies (CSR/SSR/hydration): [web.dev: Rendering on the Web](https://web.dev/articles/rendering-on-the-web)

**Build:** a dashboard, monitor list, uptime chart, and public status page talking to your Axum API.

## Phase 6: AWS and Lambda (3 weeks)

Move the checker to scheduled Lambdas, learning the execution model, permissions and cost before you deploy anything. Set up a billing alarm first.

- [ ] Lambda execution model, cold starts: [Lambda runtime environment](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html)
- [ ] Runtime API (what a "custom runtime" is): [Runtime API](https://docs.aws.amazon.com/lambda/latest/dg/runtimes-api.html), [aws-lambda-rust-runtime](https://github.com/awslabs/aws-lambda-rust-runtime)
- [ ] Building and deploying Rust Lambdas: [cargo-lambda](https://www.cargo-lambda.info/)
- [ ] IAM least privilege: [IAM best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [ ] Scheduled triggers: [EventBridge Scheduler](https://docs.aws.amazon.com/scheduler/latest/UserGuide/what-is-scheduler.html)
- [ ] Queues and fan-out: [SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)
- [ ] Function URLs vs API Gateway: [Function URLs](https://docs.aws.amazon.com/lambda/latest/dg/urls-configuration.html)
- [ ] Lambda + Postgres connection limits: [RDS Proxy](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.html)
- [ ] AWS SDK in Rust: [SDK for Rust guide](https://docs.aws.amazon.com/sdk-for-rust/latest/dg/welcome.html)
- [ ] Cost modelling: [Lambda pricing](https://aws.amazon.com/lambda/pricing/), [Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html)
- [ ] Architecture trade-offs: [Well-Architected](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html)

**Video:** search YouTube for the AWS re:Invent talk "A closer look at AWS Lambda".

**Build:** scheduled checker Lambdas writing results through SQS.

## Phase 7: Production and CI/CD (2-3 weeks)

Turn the project into something you can ship and roll back safely, with a pipeline that gates production behind staging and manual approval.

- [ ] Deployment pipelines: [Martin Fowler: DeploymentPipeline](https://martinfowler.com/bliki/DeploymentPipeline.html)
- [ ] GitHub Actions workflows: [Actions docs](https://docs.github.com/en/actions)
- [ ] Environments and manual approvals: [Using environments](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment)
- [ ] Keyless AWS auth from CI (OIDC): [GitHub: OIDC with AWS](https://docs.github.com/en/actions/security-for-github-workflows/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)
- [ ] Lints and supply-chain checks: [Clippy](https://doc.rust-lang.org/clippy/), [cargo-audit](https://github.com/rustsec/rustsec/tree/main/cargo-audit)
- [ ] Infrastructure as code: [Terraform intro](https://developer.hashicorp.com/terraform/intro), [OpenTofu](https://opentofu.org/docs/)
- [ ] Safe schema changes in deploys: [Expand-and-contract](https://www.prisma.io/dataguide/types/relational/expand-and-contract-pattern)
- [ ] Rollbacks, SLOs, incident response: [Google SRE book](https://sre.google/sre-book/table-of-contents/)

**Build:** a pipeline running test, lint, audit, build, deploy to staging, manual approval, then prod.

## Phase 8: AI features (2 weeks)

Add an "explain this incident" summarizer, learning how LLMs work well enough to control cost and to measure whether the output is any good.

- [ ] What an LLM is, and its limits: Karpathy, [Intro to LLMs](https://www.youtube.com/watch?v=zjkBMFhNj_g)
- [ ] Transformers from scratch: Karpathy, [Let's build GPT](https://www.youtube.com/watch?v=kCc8FmEb1nY)
- [ ] Neural network intuition: 3Blue1Brown, [Neural Networks playlist](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi)
- [ ] Tokens, context windows, API mechanics: [Anthropic docs](https://docs.claude.com)
- [ ] Prompt design: [Prompt engineering guide](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview)
- [ ] Tool use / structured output: [Tool use overview](https://docs.claude.com/en/docs/agents-and-tools/tool-use/overview)
- [ ] Evals (measuring if it works): [Anthropic: develop tests](https://docs.claude.com/en/docs/test-and-evaluate/develop-tests), [Hamel Husain on evals](https://hamel.dev/blog/posts/evals/)

**Build:** a summarizer called from the Axum backend, plus a small eval set of past incidents.

## Phase 9 (optional): Leptos rebuild

Rebuild only the public status page in Leptos to see how fine-grained reactivity differs from Dioxus's virtual DOM.

- [ ] Fine-grained reactivity (the key idea): [Ryan Carniato's intro](https://dev.to/ryansolid/a-hands-on-introduction-to-fine-grained-reactivity-3ndf)
- [ ] Leptos signals, effects, SSR, hydration: [Leptos Book](https://book.leptos.dev/)

**Build:** the public status page.

**Write-up:** compare it with Dioxus on reactivity, bundle size, and developer experience.

## Using AI as a tutor (no code generation)

Use Claude to test and review your understanding, never to write the code, and end each phase with a short written note.

- **Before a phase:** ask Claude to quiz you on the prerequisite concepts.
- **After a concept:** explain it back in your own words and have Claude find the gaps.
- **On compiler errors:** ask "why does the borrow checker reject this?", not "fix this".
- **Before building:** describe your design in prose and ask for failure modes.
- **After building:** ask for a review of what is non-idiomatic and what breaks under load.
- **Phase-end note:** write "what I built, what surprised me, what I'd change". It doubles as LinkedIn content.

Rule of thumb: if you can't re-derive it on a whiteboard, you haven't learned it yet.

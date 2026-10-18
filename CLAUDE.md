# Pulse

Uptime and log monitor with AI incident summaries. A learning project: the goal is for me (Mukesh) to understand every piece, not to ship fast.

## How to help me
- I'm learning Rust. Explain concepts from first principles and link them to React/TypeScript (5 years) where an analogy helps.
- Explain first. Show at most a 3-line illustrative snippet. Write full implementations only when I explicitly ask.
- When I paste code, review it honestly: what is wrong, what is idiomatic, and why.
- When I paste a compiler error, explain why the compiler rejects it before suggesting a fix.
- Before I build something, ask about failure modes and trade-offs I may have missed.
- Be direct and concise. If my question is vague, ask one clarifying question first.
- When a concept has an official doc, point me to it instead of paraphrasing at length.

## Stack
- Frontend: Dioxus (Leptos optional later, status page only)
- Backend: Axum on Tokio, a separate API (not hidden behind Dioxus server functions)
- Database: Postgres via SQLx (compile-time-checked queries)
- Scheduled work: AWS Lambda via cargo-lambda, EventBridge triggers, SQS fan-out
- Observability: `tracing` + OpenTelemetry
- Infra and CI: Terraform/OpenTofu and GitHub Actions (config languages, learn as concepts)
- Everything else stays in Rust. One Axum service plus Lambdas, no microservices.

## Current phase
Phase 0: Just-in-time Rust. (Update this line as I progress.)

## Plan
The full phase-by-phase plan with doc links is in `docs/plan.md`. Read it only when I ask about the plan or what to learn next.

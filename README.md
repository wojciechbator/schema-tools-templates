# Schema Tools Templates

**Schema-driven Rust client/server and RabbitMQ templates** used to keep generated service code consistent across projects.

This repository is not a framework and not a demo application. It is the template layer behind generated Rust integrations: Reqwest clients, Axum/Actix servers and RabbitMQ consumers/producers.

## Why it is interesting

The RabbitMQ Tokio templates encode the operational decisions that matter under load instead of leaving every generated service to rediscover them:

- bounded in-flight work with `Semaphore` rather than unbounded task spawning;
- owned worker lifecycles with `JoinSet` and explicit shutdown handling;
- explicit `Ack` / `Nack` / `Requeue` outcomes at the handler boundary;
- reconnect/backoff logic separated from message handling;
- configurable prefetch and worker counts;
- tracing hooks and generated message extraction;
- shared generated structure without forcing business logic into the transport layer.

The goal is boring, inspectable generated code: the schema decides the surface, while concurrency, acknowledgement and shutdown semantics stay visible in ordinary Rust.

## Rust templates

```text
rust/client/            Reqwest client generation
rust/server-axum/       Axum server generation
rust/server-actix/      Actix server generation
rust/model/             Shared model generation
rust/rabbitmq/          Legacy RabbitMQ template path
rust/rabbitmq-tokio/    Tokio/Lapin producer + consumer path
rust/_common/           Shared template fragments
```

See [`rust/README.md`](rust/README.md) for template parameters.

## Production context

The Tokio RabbitMQ path grew out of production messaging work where I wanted the hot path to be easier to reason about: bounded workers, explicit acknowledgement semantics, controlled reconnects and graceful shutdown instead of a deeper actor/message abstraction.

The corresponding engineering note includes the measured production results and trade-offs rather than treating the template itself as a benchmark:

**[RabbitMQ without Actix — simpler, faster and finally quiet at night](https://www.wojciechbator.me/blog/rabbitmq-tokio/)**

## Scope

These are code-generation templates. Generated applications still own their domain policy, retry/idempotency rules outside the transport boundary, persistence, observability policy and service-specific load testing.

Keeping that boundary explicit is intentional: generation should remove repetitive plumbing, not hide architecture.

## License

See [`LICENSE.md`](LICENSE.md).

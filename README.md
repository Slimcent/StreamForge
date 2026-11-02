# StreamForge

StreamForge is a distributed event ingestion and stream processing platform built with .NET.

It is designed to explore and demonstrate practical distributed-systems concepts such as event ingestion, Apache Kafka, partitioning, consumer groups, parallel processing, ordering, at-least-once delivery, idempotency, offset management, replay, consumer lag, failure recovery and eventual consistency.

The project is being developed incrementally. Infrastructure and additional services are introduced only when a concrete system requirement justifies them.

## Architecture

The initial runtime architecture is intentionally small:

```
External Producer
       |
       v
StreamForge.Ingestion
       |
       v
      Kafka
       |
       v
StreamForge.Processor
```

`StreamForge.Ingestion` receives events from external producers.

`StreamForge.Processor` consumes and processes events asynchronously.

Kafka will provide the distributed boundary between ingestion and processing.

Kafka integration has not been implemented yet.

The current repository structure is:

```
StreamForge/
│
├── src/
│   ├── StreamForge.Ingestion/
│   ├── StreamForge.Models/
│   └── StreamForge.Processor/
│
├── tests/
│   ├── StreamForge.Ingestion.Tests/
│   └── StreamForge.Processor.Tests/
│
├── docs/
│   ├── architecture.md
│   ├── decisions.md
│   └── development/
│       ├── 001-project-foundation.md
│       └── CHANGELOG.md
│
├── README.md
├── StreamForge.slnx
├── .gitignore
├── .gitattributes
└── LICENSE.txt
```

## Projects

### StreamForge.Ingestion

ASP.NET Core Web API responsible for receiving and validating events before publishing them to the event stream.

### StreamForge.Processor

.NET Worker Service responsible for consuming and processing events asynchronously.

### StreamForge.Models

Shared class library containing platform-level models and event contracts that are genuinely required by more than one StreamForge process.

It does not contain producer domain models or service-specific implementation details.

## Documentation

Project documentation is maintained continuously alongside the implementation.

- `docs/architecture.md` describes how the system currently works.
- `docs/decisions.md` records important architectural decisions and the reasoning behind them.
- `docs/development/001-project-foundation.md` records the work completed during the first milestone.
- `docs/development/CHANGELOG.md` provides a short history of notable project changes.

Documentation reflects the current implementation and avoids repeating the same information across multiple files.

## Design principles

- Keep service boundaries small and intentional.
- Do not depend on producer domain models.
- Introduce infrastructure only when a concrete requirement justifies it.
- Do not assume global event ordering.
- Design for duplicate delivery.
- Make offset management explicit.
- Keep producer systems responsible for their own business data.
- Store derived state rather than duplicating producer domain models.
- Avoid premature microservices and unnecessary abstractions.
- Measure performance before making performance claims.

## Planned development

The first milestones are:

1. Project foundation
2. Event contract and ingestion boundary
3. Kafka-backed ingestion
4. First Kafka consumer
5. Partitions and consumer groups
6. Offset and failure behaviour
7. Idempotent processing
8. Persisted projections
9. Query API

Retries, dead-letter topics, replay, observability, caching, load testing, fault injection and deployment orchestration will be introduced later when the system reaches the problems that require them.

## Technology direction

StreamForge will primarily use:

- C#
- .NET 10
- ASP.NET Core
- Apache Kafka
- xUnit
- Testcontainers
- Docker

Additional technologies such as SQL Server, Redis, OpenTelemetry, Prometheus, Grafana and Kubernetes will only be introduced when justified by the architecture.

## Status

StreamForge is currently in the project foundation stage.

The initial solution, project structure and documentation structure have been created. Event ingestion and Kafka integration are the next major development steps.

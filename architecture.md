# Architecture

## Current architecture

StreamForge currently contains two independently running processes and one shared model library.

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

Kafka is the planned asynchronous boundary between ingestion and processing.

Kafka integration has not been implemented yet.

## Projects

### StreamForge.Ingestion

ASP.NET Core Web API responsible for receiving events from external producers.

Its responsibilities will include:

- receiving event requests
- validating event envelopes
- publishing accepted events to Kafka
- returning the appropriate API response

The API should remain thin. Event-processing logic should not live in controllers.

### StreamForge.Processor

.NET Worker Service responsible for consuming and processing events asynchronously.

Its responsibilities will include:

- consuming events from Kafka
- participating in consumer groups
- processing events
- handling offsets
- recovering from processing failures
- shutting down gracefully

### StreamForge.Models

Shared class library for platform-level models and event contracts used by more than one StreamForge process.

It must not contain producer domain models or service-specific implementation details.

## Repository structure

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

## Architecture rules

- Ingestion and Processor remain independently runnable.
- Ingestion does not call Processor directly.
- Kafka provides the asynchronous boundary between ingestion and processing.
- Producer domain models are not referenced.
- Shared code is kept minimal.
- New runtime services require a clear responsibility, scaling need or failure boundary.
- Infrastructure is introduced only when a concrete requirement justifies it.
- StreamForge will not assume global ordering or exactly-once processing.
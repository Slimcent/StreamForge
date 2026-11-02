# 001 - Project Foundation

## Goal

Create the initial StreamForge solution and establish the minimum project structure required to begin development.

## Added

Created the initial production projects:

- `StreamForge.Ingestion`
- `StreamForge.Processor`
- `StreamForge.Models`

Created the initial test projects:

- `StreamForge.Ingestion.Tests`
- `StreamForge.Processor.Tests`

Created the initial repository structure:

```
src/
tests/
docs/
```

Added:

- `.gitignore`
- `.gitattributes`
- `LICENSE.txt`
- `README.md`
- initial documentation structure

## Architecture

The initial runtime direction is:

```
Producer
   |
   v
Ingestion
   |
   v
Kafka
   |
   v
Processor
```

Kafka has not been integrated yet.

## Decisions

- Keep the first architecture small.
- Use separate Ingestion and Processor runtime processes.
- Share only platform-level models.
- Do not reference producer domain models.
- Do not introduce database, cache, observability or orchestration infrastructure yet.
- Add infrastructure only when a milestone introduces the problem that requires it.

## Verification

The foundation milestone is complete when:

- the solution builds successfully
- all initial tests pass
- project paths and references are correct
- repository structure matches the documented architecture
- the README and project documentation reflect the current state
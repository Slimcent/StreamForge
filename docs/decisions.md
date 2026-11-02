# Architecture Decisions

This file keeps track of the main technical choices made while building StreamForge and why they were made.

## Started with Ingestion and Processor only

The first version has two running processes:

- `StreamForge.Ingestion`
- `StreamForge.Processor`

Ingestion handles incoming HTTP requests, while Processor runs independently and handles events from Kafka.

I kept them separate because they have different jobs and will eventually scale differently. The API may need more instances because of HTTP traffic, while processors may need more instances because of Kafka lag.

There is no reason to create more services until there is a clear need for them.

## Shared Models project

`StreamForge.Models` is used for models that genuinely need to be shared between StreamForge processes.

The main example will be the common event contract.

I want to keep this project small so it does not become a place for unrelated service logic or infrastructure code.

## Producer models stay outside StreamForge

StreamForge should only depend on published event data, not on the internal models of the systems sending events.

That keeps the producer free to change its internal classes, database entities or structure without affecting StreamForge.

The event contract is the integration boundary.

## Kafka between ingestion and processing

Kafka sits between the Ingestion API and the Processor.

This lets ingestion accept an event without waiting for downstream processing to finish.

It also gives us the behaviour the project is meant to explore, including:

- partitions
- consumer groups
- offsets
- replay
- ordering
- duplicate delivery
- lag
- failure recovery

Using Kafka adds operational complexity, but that complexity is relevant to the purpose of StreamForge.

## Processing is asynchronous

When an event is accepted, that does not mean it has already been fully processed.

The producer should be able to continue once the event has been accepted into the stream.

Processing can happen later and at a different speed.

Because of this, derived data will be eventually consistent rather than immediately consistent.

## Ordering is based on partitions

StreamForge will rely on Kafka ordering within a partition.

There is no plan to provide global ordering across the whole event stream.

When related events need to stay in order, they should use a partition key that keeps them on the same partition.

This gives us ordering where it matters without preventing parallel processing across the whole system.

## Expect duplicate delivery

The system will be designed with the assumption that an event can be seen more than once.

For example, a processor may finish its work and crash before the Kafka offset is committed. When it starts again, the same event may be delivered again.

This means processing logic that changes state will eventually need to be idempotent.

## Offset handling should be deliberate

Offsets determine how far a consumer has progressed through a Kafka partition.

Where and when we commit an offset affects failure behaviour.

Committing too early can lose work. Committing after processing can mean an event is seen again after a crash.

For that reason, offset handling will be treated as part of the processing design rather than something we leave unexplained.

## Derived state will be stored separately

When persistence is introduced, StreamForge will store projections and aggregates created from the event stream.

These will be optimised for querying rather than treated as the original event history.

This also gives us the option to rebuild projections later by replaying events if the projection logic changes.

## SQL Server comes later

There is no database in the project foundation.

At this stage there is nothing useful to persist yet.

SQL Server will be added when we reach the point where processors need to store projections or other derived state.

## Redis comes later

Redis is part of the possible future stack, but there is currently no problem that requires it.

If we later need fast shared state, caching or another distributed use case where Redis makes sense, we can introduce it then.

## No load balancer yet

The first version only needs one Ingestion instance.

A load balancer becomes useful when we deliberately run multiple API instances and need to distribute HTTP traffic between them.

Kafka processors will scale differently through partitions and consumer groups.

## Kubernetes comes later

Kubernetes is not needed for the early stages of the project.

The first problems we need to solve are around ingestion, Kafka, consumers, partitions, offsets and failure behaviour.

Docker and Docker Compose will be enough while the architecture is still small.

Kubernetes can be considered later if the number of running components and deployment requirements make it worthwhile.

## Add infrastructure when there is a reason for it

The project should grow from actual requirements.

If we need persistence, we add a database.

If we need caching or shared fast state, we evaluate Redis.

If we need metrics and tracing, we add observability tooling.

If we need multiple HTTP instances, we add load balancing.

This keeps the architecture easier to understand and makes every technology in the project something I can explain and justify.
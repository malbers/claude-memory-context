---
id: 001
title: Use Postgres over Mongo for the primary application database
status: active
date: 2026-05-05
supersedes: null
superseded_by: null
tags: [database, vendor]
---

> **Note:** This is an illustrative example file — it shows the shape of a
> well-formed decision but is not a real decision in this repo. Replace with
> your own when you start using the pattern.

## Context

Picking the primary application database for a new product. Two finalists: Postgres and Mongo. Schema is well-defined and likely to stay relational; we expect transactional writes from day one; team has more ops experience with Postgres than with Mongo.

## Decision

Use Postgres as the primary application database. Standard managed offering (RDS / Cloud SQL / equivalent). Mongo not used anywhere in the stack for the launch product — defer the document-store question until a real document-shaped workload appears.

## Consequences

- Commits the team to relational schema modeling for all v1 entities.
- Forecloses easy schema flexibility — any "just add a field" lives behind a migration.
- Transactional guarantees come for free; we don't have to design around eventual consistency at the data layer.
- Ops cost is well-understood: backups, point-in-time restore, read replicas all standard.
- Early-warning sign that this was wrong: we end up writing JSONB-heavy schemas to fake document semantics. If that pattern shows up in two or more services, revisit.

## Revisit when

- A genuinely document-shaped workload appears (high-cardinality nested data with varying shape per record).
- We hit Postgres write-throughput limits that read replicas + connection pooling can't solve.
- Team composition shifts toward heavy Mongo expertise that we'd want to leverage.

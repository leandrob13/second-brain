# CQRS — Command Query Responsibility Segregation

#architecture #cqrs #design-patterns #domain-driven-design #application-patterns #command #query #event-sourcing

> **Scope:** Application-level patterns only. This note focuses on how CQRS is applied within a single application codebase — not system-wide or platform infrastructure concerns.

---

## Origin and Foundational Idea

CQRS was introduced by **Greg Young** and builds upon Bertrand Meyer's **Command-Query Separation (CQS)** principle, which states:

> *"A method should either change the state of an object, or return a result — but not both."*

CQRS elevates this from a method-level discipline to an **architectural separation**:

- **Commands** — change state, return only an acknowledgement or error.
- **Queries** — return data, never change state.

The key insight is that reading data and writing data are fundamentally different operations with different models, validation rules, and performance profiles. Trying to serve both with a single model produces unnecessary compromises.

---

## Core Concepts

### Command

A **Command** is an explicit object that represents an **intent to change** the application's state. It is named as an imperative business action, not a technical instruction.

| ✅ Good | ❌ Avoid |
|---|---|
| `PlaceOrder` | `SetOrderStatusToPending` |
| `CancelReservation` | `UpdateReservation` |
| `SubmitPayment` | `SavePaymentRecord` |

A command carries all the data required for that specific action. It is typically handled by a single **Command Handler** and should not return domain data — only a confirmation that the command was accepted (or a failure with the reason).

### Query

A **Query** is a request for information. It carries the criteria or parameters needed and is answered by a **Query Handler** that fetches and maps data into a **DTO (Data Transfer Object)**. Queries have no side effects — they never modify state.

### Command Handler

The Command Handler is the entry point for write logic. It:

1. Receives the Command object.
2. Validates the intent (business rules).
3. Loads the relevant **Aggregate** (the domain model responsible for that area of behavior).
4. Executes the operation on the Aggregate.
5. Persists the result to the **Write Store**.
6. Returns success or error — not domain data.

### Query Handler

The Query Handler is the entry point for read logic. It:

1. Receives the Query object.
2. Fetches data from the **Read Store** (often using simple queries — no domain objects required).
3. Maps the result to a **DTO** shaped for the caller's needs.
4. Returns the DTO — no domain logic involved.

---

## Architectural Shape

![[diagrams/cqrs-overview.drawio.svg]]

The application splits into two distinct sides:

**Write Side** — handles Commands, contains business logic, domain model, validation, and writes to a normalized Write Store.

**Read Side** — handles Queries, contains thin data-access logic, reads from a denormalized Read Store, and returns DTOs.

Both sides may share the same physical database (single-store CQRS) or use completely different databases optimized for each concern (split-store CQRS). The synchronization between them happens via **events or CDC (Change Data Capture)**.

---

## Request Pipeline

![[diagrams/cqrs-pipeline.drawio.svg]]

### Command Pipeline Flow

```
Command Object → Pipeline Behaviors → Validation → Command Handler → Aggregate → Write Store
                                          ↓ invalid
                                      Error Response
```

### Query Pipeline Flow

```
Query Object → Pipeline Behaviors → Query Handler → Read Store → DTO Response
```

**Pipeline Behaviors** are cross-cutting concerns applied transparently to every command or query before reaching the handler. Common behaviors include:

- **Logging** — trace every operation.
- **Authentication / Authorization** — gate access before processing.
- **Validation** — enforce structural and business rule constraints.
- **Retry policies** — handle transient failures for commands.
- **Caching** — cache query results to reduce read-store pressure.
- **Performance tracing** — measure execution time per handler.

---

## The Write Model

The Write Side is where **business complexity lives**. Its responsibilities:

- **Aggregate** — a cluster of domain objects treated as a unit of consistency. The Aggregate enforces all invariants (business rules) before any state change occurs. Commands are always directed at an Aggregate.
- **Domain Validation** — rules specific to the business domain, not just data format checks (e.g., "a cancelled order cannot be shipped").
- **Transactional integrity** — each command is handled as an atomic operation. The Aggregate root is the single point of truth for that operation.

### Aggregate Rules at Application Level

1. One Aggregate per Command — a command is handled by exactly one Aggregate.
2. Aggregates should not reference other Aggregates directly by object — use identifiers.
3. Keep Aggregates small — model around consistency boundaries, not data groupings.
4. The Aggregate is loaded, mutated, and saved within a single transaction.

---

## The Read Model

The Read Side is deliberately **thin and data-access focused**. Its responsibilities:

- Serve **pre-shaped projections** of data optimized for each use case.
- Return **DTOs**, not domain objects. There is no domain logic in the Read Model.
- Be eventually consistent with the Write Side — a small delay is acceptable and expected.

### Projection Patterns

A **projection** is a denormalized view of domain data, assembled specifically for a query use case. Projections may:

- Combine data from multiple aggregates or entities.
- Pre-compute calculated fields (totals, counts, statuses).
- Flatten nested structures for easy consumption.
- Exist in different forms for different clients (mobile vs. web vs. reporting).

Projections can be **live** (recomputed on demand from events) or **materialized** (stored and kept in sync via events).

---

## CQRS + Event Sourcing

![[diagrams/cqrs-event-sourcing.drawio.svg]]

CQRS pairs naturally with **Event Sourcing**, though neither requires the other.

In this combination:

1. The Command Handler processes the command and directs the Aggregate.
2. The Aggregate **emits Domain Events** instead of directly mutating a record (e.g., `OrderPlaced`, `PaymentConfirmed`).
3. Events are **appended to an Event Store** (an append-only log) — this becomes the single source of truth.
4. A **Projection Engine** subscribes to the event stream and updates one or more Read Model stores asynchronously.
5. Queries are served from those Read Models via Query Handlers.

### Domain Events

Domain Events are immutable facts about something that happened in the domain. They:

- Are named in past tense: `OrderShipped`, `UserRegistered`, `PaymentFailed`.
- Carry only the data relevant to what occurred.
- Are append-only — they are never updated or deleted.
- Can be replayed to rebuild state or generate new projections.

### Snapshots

When replaying a long event history to rebuild an Aggregate's state becomes expensive, **snapshots** are used. A snapshot captures the Aggregate's state at a given point in time. On load, the system restores from the latest snapshot and replays only the events that occurred after it.

---

## Mediator Pattern (Application-Level Wiring)

CQRS is commonly implemented in combination with the **Mediator pattern**. The Mediator acts as a **dispatcher** that decouples the sender from the handler:

```
Caller → Mediator → Command/Query Handler
```

The caller issues a Command or Query to the Mediator without knowing which handler will process it. The Mediator routes it to the correct handler based on the type. This produces several benefits:

- Handlers can be registered and replaced independently.
- Pipeline behaviors are applied at the Mediator level, not in the handlers.
- Handlers become small, focused, and individually testable.

---

## When to Use CQRS

CQRS adds structural complexity. Apply it selectively — not to every feature or domain area. It pays off when:

- The domain has **complex business rules** that benefit from an explicit domain model on the write side.
- Read and write **performance profiles diverge** significantly (e.g., many reads, few writes).
- Different teams own the **read and write concerns** and need to evolve them independently.
- The UI is **task-based** (user performs specific business tasks, not generic CRUD forms).
- The system integrates with **event-driven** subsystems or requires an audit trail.
- Multiple **projections** of the same data are needed for different clients or reporting needs.

## When NOT to Use CQRS

- Simple CRUD screens with straightforward data models — the overhead outweighs the benefit.
- Small domains with minimal read/write asymmetry.
- Teams without experience in event-driven patterns — the learning curve is steep and mistakes are expensive.
- Systems that need strong consistency between every read and write (CQRS introduces eventual consistency on the read side).

> **Martin Fowler's caution:** *"CQRS is a significant mental leap and should only be used on specific parts of a system where it gives a clear benefit."*

---

## Best Practices

### Command Design

- Name Commands after **business intent**, not technical operations.
- Commands should be **self-validating** — carry all the data needed for their own validation.
- Validate in two layers: structural (format, required fields) before reaching the handler; business rules inside the handler or on the Aggregate.
- Commands are **fire-and-forget** when processed asynchronously — the caller receives only an acknowledgement.
- Do not return domain objects from commands. Return only success/failure and an identifier if needed.

### Query Design

- Queries are **side-effect free** — they never trigger state changes.
- Design DTOs for the **consumer's shape**, not the domain model's shape.
- Prefer **dedicated DTOs** per use case over reusing domain objects.
- Queries can be cached aggressively since they do not alter state.
- Avoid adding business logic to Query Handlers — they should be thin data-fetchers.

### Handler Design

- Each handler should have **a single responsibility**: one Command per handler, one Query per handler.
- Handlers should be **independently testable** in isolation.
- Avoid handler-to-handler calls — use Domain Events for reactions instead.
- Keep handlers **free of infrastructure concerns** — delegate to repositories or data access objects.

### Consistency

- Accept **eventual consistency** on the read side by design.
- Communicate this clearly to the UI layer — read models may lag by milliseconds to seconds.
- Where strong consistency is critical for a specific operation, consider returning the updated state directly from the command (as an exception, not a rule).

### Bounded Application

- Apply CQRS only to **bounded contexts or modules** where complexity warrants it.
- The rest of the application can remain CRUD-style.
- Do not impose CQRS uniformly on all data access.

---

## Key Relationships

- [[vertical-slicing]] — CQRS commands and queries map cleanly to vertical slices; each slice can own its own command and query handlers.
- [[application-event-sourcing]] — Event Sourcing is the natural write-side complement to CQRS.
- [[application-domain-model]] — The Aggregate pattern is the domain model construct that Command Handlers operate on.
- [[application-mediator-pattern]] — The Mediator is the common wiring mechanism for CQRS handlers in application code.

---

## Summary Table

| Aspect | Command Side | Query Side |
|---|---|---|
| Purpose | Change state | Return data |
| Model | Domain model / Aggregate | DTO / Projection |
| Validation | Full business rule validation | Minimal or none |
| Side Effects | Yes — persists state changes | None |
| Return value | Ack or Error | DTO |
| Store | Write Store (normalized) | Read Store (denormalized) |
| Testing | Unit test against Aggregate | Unit test against data mapping |
| Consistency | Strong (transactional) | Eventual |

---

## Sources

- [Martin Fowler — CQRS](https://martinfowler.com/bliki/CQRS.html)
- [Microsoft Azure Architecture Center — CQRS Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs)
- [Kurrent — A Beginner's Guide to CQRS](https://www.kurrent.io/cqrs-pattern)
- [microservices.io — CQRS Pattern](https://microservices.io/patterns/data/cqrs.html)
- [Confluent — CQRS](https://www.confluent.io/learn/cqrs/)
- [Kurrent — Event Sourcing and CQRS](https://www.kurrent.io/blog/event-sourcing-and-cqrs)

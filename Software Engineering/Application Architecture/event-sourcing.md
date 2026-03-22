# Event Sourcing

#architecture #event-sourcing #design-patterns #domain-driven-design #application-patterns #aggregate #cqrs #projections #event-store #snapshots

> **Scope:** Application-level patterns only. This note focuses on how Event Sourcing is applied within a single application codebase — the building blocks, design patterns, and best practices for developers. It is intentionally technology and framework agnostic.

---

## The Core Idea

In a traditional application, you store the **current state** of an entity. When a user changes something, you overwrite what was there before. History is lost.

Event Sourcing inverts this: instead of storing the latest state, you store the **sequence of events that led to that state**. The current state is always derived by replaying those events, never stored directly.

> *"Instead of storing just the current state of the data in a domain, use an append-only store to record the full series of events that describe actions taken on that data."*
> — Microsoft Azure Architecture Center

The event log becomes the **single source of truth**. The application state is secondary — a derivation.

---

## Application-Level Overview

![[diagrams/event-sourcing-overview.drawio.svg]]

The application is split into two distinct paths that share a single source of truth — the Event Store.

**Write Path** — a command arrives, the Command Handler rehydrates the Aggregate from past events, the Aggregate executes business logic and emits Domain Events, and those events are appended to the Event Store.

**Read Path** — a Projection Engine subscribes to the Event Store and builds denormalized Read Models. Queries are served from those Read Models — never from the Event Store directly.

---

## Core Building Blocks

### Command

A Command represents an explicit **intent to change** the application's state. It is not a generic CRUD operation — it carries business meaning.

| Good | Avoid |
|---|---|
| `PlaceOrder` | `UpdateOrder` |
| `CancelReservation` | `SetReservationStatus` |
| `ConfirmPayment` | `SavePaymentRecord` |

A command is handled by exactly one **Command Handler**. It should not return domain data — only a confirmation of acceptance or a failure with the reason.

---

### Command Handler

The Command Handler is the entry point for write logic. Its responsibilities are:

1. Receive the command.
2. Load the relevant **Aggregate** by reading and replaying its events from the Event Store (rehydration).
3. Invoke the appropriate domain method on the Aggregate.
4. Persist the emitted Domain Events back to the Event Store.
5. Return success or error — not domain data.

The Command Handler itself contains no business rules. All invariants live inside the Aggregate.

---

### Aggregate

The Aggregate is the **consistency boundary** for your domain. It enforces all business rules (invariants) before any state change is allowed. In event sourcing, an Aggregate does not store state directly — its state is reconstructed by replaying its past events.

**Key Aggregate rules at application level:**

- One Aggregate per Command — a command targets exactly one Aggregate.
- Aggregates do not reference other Aggregates by object — use identifiers.
- Keep Aggregates small — model around consistency boundaries, not data groupings.
- The Aggregate is loaded, mutated, and its events saved within a single transaction.

When a business action is invoked on an Aggregate, it does not directly mutate a field. Instead, it **emits a Domain Event** that records what happened. The Aggregate then applies that event to update its own internal state. This is the fundamental shift of event sourcing.

---

### Domain Event

A Domain Event is an **immutable fact** about something that happened in the domain. It is always named in the past tense.

```
OrderPlaced
ItemAddedToCart
PaymentFailed
ReservationCancelled
```

Domain Events:
- Carry only the data relevant to what occurred.
- Are never updated or deleted — they are permanent facts.
- Are assigned a sequential position within their stream (the Aggregate's stream).
- Can be replayed at any time to reconstruct state or build new projections.

---

### Event Store

The Event Store is an **append-only log** — the authoritative source of truth for the application. It is structured as a series of named streams, one per Aggregate instance.

```
Order stream (orderId=abc123):
  position 1: OrderPlaced     { orderId, customerId, items }
  position 2: ItemAdded       { orderId, item }
  position 3: PaymentConfirmed { orderId, amount }
  position 4: OrderShipped    { orderId, trackingId }
```

Events are never modified. To "undo" a change, a **compensating event** is appended — a new fact that represents the reversal.

The Event Store must guarantee:

- **Ordering** — events within a stream are strictly ordered by position.
- **Atomicity** — all events from a single command are persisted as one atomic operation.
- **Immutability** — once written, events cannot be altered.

---

### Projection (Read Model)

Because the Event Store is not designed for querying (it is a stream, not a table), the application builds **Projections** — denormalized read models tailored for specific query use cases.

![[diagrams/event-sourcing-projections.drawio.svg]]

A Projection Engine subscribes to the Event Store and handles events to update a dedicated Read Model database. The same event stream can feed multiple independent projections, each optimized for a different consumer.

**Examples of multiple projections from one stream:**
- Orders Summary View (for the dashboard)
- Inventory View (for the stock management screen)
- Audit Log View (for compliance reporting)

Each projection can be **rebuilt from scratch** at any time by replaying the event stream from the beginning. This makes projections disposable and evolvable — if requirements change, you create a new projection and replay.

**Projection design principles:**
- Projections are idempotent — applying the same event twice must produce the same result. Use natural keys and upserts.
- Projections must not contain business logic — they are read-only data transformers.
- Projections are eventually consistent with the Event Store — there is an expected, brief lag.

---

## Aggregate Rehydration

Rehydration is the process of reconstructing an Aggregate's current state from its event history before processing a new command.

![[diagrams/event-sourcing-aggregate-lifecycle.drawio.svg]]

The process is:

1. Load all events for the Aggregate's stream from the Event Store.
2. Start with an empty (initial) state.
3. Apply each event in sequence — each event handler mutates the Aggregate's internal state.
4. After all events are applied, the Aggregate reflects its current state and is ready for the command.

This "apply" loop is the heart of event sourcing. Every state transition the Aggregate has ever undergone is encoded in this sequence.

---

## Snapshot Pattern

For long-lived Aggregates with hundreds or thousands of events, full rehydration on every command becomes expensive. The **Snapshot** pattern solves this.

A snapshot captures the Aggregate's full state at a given event position. On the next load, the system:

1. Loads the most recent snapshot.
2. Loads only the events that occurred **after** that snapshot position.
3. Applies only those newer events to the snapshot state.

```
Without snapshot: replay E1 → E2 → E3 → E4 → E5 → ... → E2000
With snapshot:    restore snapshot(E1800) → apply E1801 → ... → E2000
```

Snapshots are created as a background optimization — they do not alter the event log, which remains the authoritative record.

**When to introduce snapshots:**
- When rehydration latency becomes measurable (e.g., >50ms).
- For high-frequency Aggregates with long histories.
- As a targeted optimization, not a default from the start.

---

## Event Versioning

Events are permanent facts. Over time, the shape of an event may need to change (e.g., adding a field, renaming a property). This is **event versioning**, one of the harder operational concerns in event sourcing.

Common strategies:

**Upcasting** — transform older event versions into the current version at read time, before applying them to the Aggregate. The event store retains the original; a layer converts it transparently.

**Weak schema** — design event payloads with enough flexibility (e.g., optional fields, forward-compatible formats) that consumers handle both old and new versions without transformation.

**Version stamp** — embed a `version` field in every event. Event handlers use the version to dispatch to the appropriate handling logic.

**New event types** — instead of modifying an existing event, introduce a new event type for the new behavior. Old events continue to work. New projections can subscribe to the new type.

> The immutability of the event log means you can never change what happened in the past. You can only build forward.

---

## Compensating Events

Since events are immutable, you cannot delete or modify a past event to "undo" a mistake. Instead, you **append a compensating event** — a new fact that represents the reversal of the unwanted state change.

```
E1: OrderPlaced
E2: ItemAdded  (wrong item added by mistake)
E3: ItemRemoved  ← compensating event
E4: ItemAdded  (correct item)
```

The full history is preserved. The current state correctly reflects the intended outcome.

---

## CQRS + Event Sourcing

![[diagrams/cqrs-event-sourcing.drawio.svg]]

Event Sourcing pairs naturally with **CQRS** (Command Query Responsibility Segregation), though neither requires the other. In this combination:

1. The Command Handler processes the command and rehydrates the Aggregate from the Event Store.
2. The Aggregate emits Domain Events.
3. Events are appended to the Event Store (the write side's persistent store).
4. The Projection Engine subscribes to events and updates Read Models asynchronously.
5. Queries are served from Read Models via Query Handlers.

See [[application-cqrs]] for the full CQRS note.

The key benefit of the combination: the Event Store is simultaneously the **write store** and the **audit log**. There is no separate audit mechanism — history is inherent.

---

## Eventual Consistency

Event Sourcing introduces **eventual consistency** on the read side. After a command is processed and events are appended, the Projection Engine updates the Read Models asynchronously. During that brief window, a query may return slightly stale data.

This is expected and acceptable by design. The application should:

- Communicate this behavior clearly to UI consumers.
- Design the UI to handle stale reads gracefully (e.g., optimistic UI updates).
- Not attempt to achieve strong read-after-write consistency by querying the Event Store directly.

Where strong consistency is truly required for a specific scenario (e.g., immediately returning the new state after a command), the command handler may return the updated data as an exception — but this should be the exception, not the norm.

---

## Event Replay

One of event sourcing's most powerful capabilities is **event replay**: the ability to reprocess the entire event history to produce a new outcome.

**Use cases for replay:**
- **Rebuilding a projection** — if a projection's logic was incorrect, fix the handler and replay to regenerate the view from scratch.
- **Creating a new read model** — add a new projection and replay history to backfill it with past events.
- **Bug correction** — identify which events were produced by a defect, apply compensating events, and optionally re-derive affected state.
- **Time-travel queries** — reconstruct application state at any historical point in time (e.g., "what was the order status at 14:00 yesterday?").
- **Testing** — deterministically replay production events in a test environment to reproduce edge cases.

---

## Idempotency

Because event delivery in distributed systems is often "at least once" (events may be delivered more than once due to retries), Projection handlers must be **idempotent**.

An idempotent handler produces the same result regardless of how many times the same event is applied. Design patterns:

- Use natural keys and `UPSERT` (insert or update) operations.
- Track processed event positions per projection — skip already-handled positions.
- Design state transitions such that applying an already-applied event is a no-op.

---

## Best Practices

### Event Design
- Name events in the **past tense** — they describe facts, not intentions.
- Keep events **small and focused** — one event per state transition.
- Include a **timestamp** and a **correlation ID** in every event for debugging and tracing.
- Include **enough data in the event payload** to be self-contained — avoid the need to query external state during replay.
- Never include mutable references in events — embed the data directly.

### Aggregate Design
- Separate the **business logic** (decision making) from the **state update** (applying the event). The Aggregate decides what happened; the event records it; the apply method reflects it.
- Make Aggregate apply methods **deterministic** — given the same event sequence, the result must always be identical.
- Keep event streams **per Aggregate instance**, not per Aggregate type. Each Order has its own stream, not one stream for all orders.

### Event Store Design
- Store events with a **stream identifier** (Aggregate ID), **event type**, **event version**, **timestamp**, and **payload**.
- Use **optimistic concurrency control** — before appending, verify the current stream version matches what was loaded. If another writer has modified the stream, reject and retry.
- Never delete events from the production store — archive them to cold storage if needed, but preserve the record.

### Projection Design
- Idempotency first — always design for at-least-once delivery.
- Projections should be **rebuiltable** — treat them as disposable caches.
- Keep projection logic **simple** — it is data transformation, not business logic.
- Track the last processed event position per projection — this is your recovery point.

### Testing
- Test Aggregates by feeding a sequence of past events, issuing a command, and asserting the resulting events — no database required.
- Test Projections by feeding a sequence of events and asserting the resulting Read Model state.
- Test full flows by exercising the command pipeline and asserting the events appended to the Event Store.

---

## When to Use Event Sourcing

Event Sourcing adds significant complexity. It is appropriate when:

- An **audit trail** is a core requirement (financial transactions, compliance, legal).
- The ability to **replay history** and generate new views of past data is valuable.
- The domain involves **complex business rules** where tracking every transition matters.
- The system requires **multiple independently evolving read models** from the same data.
- You need to support **time-travel queries** (state at any point in time).
- There is a strong **event-driven integration model** between bounded contexts.

It is likely inappropriate when:

- The domain is simple CRUD with no meaningful business logic.
- The team lacks experience with event-driven patterns — the learning curve is steep.
- The system requires **strong read-after-write consistency** everywhere.
- There is no genuine need for audit history or event replay.

> *"Event sourcing is a complex pattern that permeates through the entire architecture and introduces trade-offs to achieve increased performance, scalability, and auditability. There is a high cost to migrate to or from an event sourcing system."*
> — Microsoft Azure Architecture Center

---

## Key Relationships

- [[application-cqrs]] — CQRS is the natural architectural companion to Event Sourcing; the two patterns are frequently combined.
- [[vertical-slicing]] — Event Sourcing commands and projections map naturally onto vertical slices; each slice can own its own Aggregate, events, and projections.

---

## Summary Table

| Concept | Role | Key Property |
|---|---|---|
| Command | Expresses intent to change state | Named as a business action (imperative) |
| Command Handler | Entry point for write logic | Rehydrates Aggregate, persists events |
| Aggregate | Enforces business invariants | Emits events; does not store state directly |
| Domain Event | Immutable fact about what happened | Past tense; append-only; never deleted |
| Event Store | Source of truth for all state changes | Append-only log; ordered per stream |
| Snapshot | Performance optimization for rehydration | Cached state at a given event position |
| Projection | Builds Read Models from the event stream | Idempotent; rebuiltable from history |
| Read Model | Denormalized view optimized for queries | Eventually consistent; not the source of truth |
| Compensating Event | Reverts an unwanted state change | Appended as a new fact; history preserved |

---

## Sources

- [Martin Fowler — Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html)
- [Microsoft Azure Architecture Center — Event Sourcing Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)
- [Kurrent — Event Sourcing and CQRS](https://www.kurrent.io/blog/event-sourcing-and-cqrs)
- [Designing Scalable Systems with CQRS and Event Sourcing — Mees Egberts](https://www.meesegberts.nl/blog/event-sourcing)
- [CQRS Step-by-Step — Daniel Whittaker](https://danielwhittaker.me/2020/02/20/cqrs-step-step-guide-flow-typical-application/)
- [IBM Event-Driven Architecture — Event Sourcing Patterns](https://ibm-cloud-architecture.github.io/refarch-eda/patterns/event-sourcing/)
- [microservices.io — Event Sourcing Pattern](https://microservices.io/patterns/data/event-sourcing.html)

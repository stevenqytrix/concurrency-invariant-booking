# Database Guarantees

## Purpose
This document defines the **non-negotiable guarantees**
required from the persistence layer
to preserve the booking invariant under concurrency.

The database is not an implementation detail.
It is a **correctness boundary**.

---

## Required Guarantees
To preserve the invariant, the database must provide:

- atomic state transitions
- isolation at the write boundary
- detection of write-write conflicts
- rejection of invalid state transitions

Any persistence mechanism that cannot provide these guarantees
is incompatible with this system.

---

## Atomicity
Each booking attempt must result in:

- a fully applied state transition
or
- no state change at all

Partial updates are forbidden.

Atomicity ensures that the system
never exposes intermediate or impossible states.

---

## Isolation at the Write Boundary
While reads may be stale,
writes must be evaluated against a **consistent snapshot**
of the resource state.

This ensures that:
- invariant validation is meaningful
- concurrent writes do not interleave incorrectly

Isolation is required at the point of persistence,
not during application execution.

---

## Conflict Detection
The database must detect conflicting concurrent writes.

This may be achieved through:
- version checks
- compare-and-swap semantics
- constraint enforcement

The mechanism is not prescribed.
The guarantee is.

---

## Constraint Enforcement
The invariant must be enforceable
as a database-level constraint or condition.

If a write would violate the invariant,
the database must reject it.

Acceptance of an invalid state
is considered a system failure.

---

## Rejection as a Correct Outcome
A rejected write is a **valid and expected outcome**.

Rejection indicates:
- successful detection of contention
- correct enforcement of the invariant

The application must surface this rejection explicitly,
not mask or retry it implicitly.

---

## What the Database Is Not Responsible For
The database is NOT responsible for:

- fairness between clients
- request ordering
- retry policies
- user-facing semantics

These concerns belong outside the persistence layer.

---

## Architectural Implication
By making the database the final arbiter:

- the application remains simple
- concurrency control is centralized
- correctness is non-probabilistic

Any attempt to weaken these guarantees
directly weakens system correctness.

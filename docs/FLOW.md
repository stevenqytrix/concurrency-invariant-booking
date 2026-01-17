# Booking Flow Under Concurrency

## Overview
This document describes the **semantic execution flow**
of a booking attempt under concurrent load.

The flow is designed to preserve the invariant
without relying on global locks or serialized execution.

---

## High-Level Flow
Each execution of the `AttemptBooking` use case
follows the same logical steps:

1. Observe current resource state
2. Attempt a state transition
3. Persist the transition atomically
4. Emit a single deterministic outcome

The flow does not assume exclusive access at any point.

---

## Step 1 — Observe State
The system reads the current state of the resource,
including:

- declared capacity
- current confirmed bookings
- version or concurrency marker

This read is **non-exclusive** and may become stale immediately.

Staleness is expected and accounted for.

---

## Step 2 — Propose Transition
Based on the observed state,
the system proposes a transition:

- increase confirmed bookings by `requested_units`

At this stage:
- no invariant is assumed to hold
- no decision is final
- no success is guaranteed

This is a **proposal**, not a commitment.

---

## Step 3 — Attempt Persistence
The proposed transition is persisted atomically.

The persistence layer is responsible for enforcing:
- version consistency
- invariant preservation

Possible results at this stage:

- the transition is accepted
- the transition is rejected due to invariant violation
- the transition fails due to a technical conflict

No partial state is allowed to escape this step.

---

## Step 4 — Emit Outcome
Based on the persistence result,
exactly one outcome is emitted:

- BOOKED → transition accepted
- REJECTED → invariant would be violated
- FAILED → transition could not be completed

No retries occur within this flow.

Each execution terminates definitively.

---

## Concurrency Characteristics
This flow remains correct under:

- concurrent reads
- overlapping writes
- write-write conflicts
- non-deterministic execution order

Correctness is achieved through **constrained state transitions**,
not through execution control.

---

## Failure Visibility
All failures are surfaced explicitly as outcomes.

There are:
- no hidden retries
- no swallowed errors
- no implicit compensation

Failure is part of the normal execution model.

---

## Non-Goals
This flow does NOT attempt to:

- serialize concurrent attempts
- optimize for maximum acceptance
- guarantee fairness
- hide contention effects

Any system that hides contention
does so at the cost of correctness.

# Capacity Invariant

## Invariant Statement
At any point in time, the number of **confirmed bookings**
for a given resource **must not exceed** its declared capacity.

Formally:

confirmed_bookings(resource) ≤ capacity(resource)

This invariant is global, non-negotiable, and must hold
regardless of concurrency level or execution order.

---

## Why This Invariant Exists
The invariant protects the system from **oversubscription**.

Without strict enforcement:
- resources can be over-allocated
- downstream systems observe impossible states
- recovery becomes undefined and unsafe

Once violated, the system cannot be repaired without manual intervention.

---

## Violation Scenarios
This invariant is vulnerable under concurrency when:

- multiple clients read the same available capacity concurrently
- multiple booking attempts proceed based on stale state
- application-level checks race before persistence
- retries amplify contention windows

A single-threaded execution is not sufficient to expose these failures.
They only emerge under **real concurrent load**.

---

## Why Application-Level Checks Are Insufficient
Application-level validation alone cannot guarantee this invariant because:

- reads are not serialized
- state becomes stale immediately under contention
- in-memory locks do not scale across processes
- distributed execution breaks local assumptions

Any solution that relies solely on application logic
is inherently probabilistic under load.

---

## Database as the Final Arbiter
The database is the **last line of defense** for this invariant.

The invariant is enforced through:
- atomic state transitions
- version-based optimistic locking
- database-level constraints

If the application layer fails to coordinate correctly,
the database **must reject** the violating operation.

A rejected write is a **correct outcome**, not an error.

---

## Consequences of Enforcement
As a direct consequence of enforcing this invariant:

- some booking attempts will be rejected under contention
- retries may occur but are bounded
- throughput is preferred over global serialization
- correctness is preferred over acceptance rate

These outcomes are intentional and required.

---

## Non-Goals
This invariant does NOT attempt to guarantee:

- fairness between clients
- ordering of successful bookings
- eventual acceptance of all requests
- user-level experience optimization

Any system that promises all of the above under contention
is either lying or incorrect.

# Concurrency Model

## Overview
This system is designed to operate correctly
in the presence of **uncontrolled concurrency**.

Concurrency is treated as the default condition,
not as an edge case.

Race conditions are expected, observable,
and intentionally tolerated.

---

## Sources of Concurrency
Concurrent execution arises from:

- multiple clients issuing requests simultaneously
- parallel application instances
- retries triggered by transient failures
- non-deterministic scheduling

No assumption is made about execution order.

---

## Expected Race Conditions
The following race conditions are explicitly allowed:

- concurrent reads of the same resource state
- overlapping booking attempts against remaining capacity
- write-write conflicts during persistence
- stale reads leading to rejected transitions

These races are not bugs.
They are the normal operating environment.

---

## Why Races Are Acceptable
Race conditions are acceptable because:

- correctness is enforced at the state transition boundary
- invariant validation occurs atomically
- rejected writes preserve system correctness

The system does not rely on preventing races.
It relies on **containing their effects**.

---

## Optimistic Locking Strategy
Concurrency is managed using optimistic assumptions:

- conflicts are expected but rare relative to reads
- failed writes are cheaper than serialized execution
- contention is resolved by rejection, not waiting

This strategy favors throughput and correctness
over global coordination.

---

## Retry Semantics
Retries are **not implicit**.

A failed execution produces a `FAILED` outcome.
Any retry decision is external to this flow.

This prevents:
- retry storms
- hidden amplification of contention
- loss of failure visibility

---

## What Is Not Guaranteed
This concurrency model does NOT guarantee:

- fairness between competing requests
- ordering of successful operations
- eventual success under sustained contention
- starvation avoidance

These guarantees would require stronger coordination
and would weaken throughput and simplicity.

---

## Design Trade-Off
The system intentionally trades:

- acceptance rate
for
- invariant preservation
and
- operational predictability

This trade-off is explicit and deliberate.

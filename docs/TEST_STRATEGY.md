# Test Strategy

## Purpose
This document defines how the system's correctness
is verified under concurrent execution.

Tests are designed to **challenge the invariant**,
not to confirm happy paths.

---

## Definition of Correctness
The system is considered correct if:

- the capacity invariant is never violated
- rejected outcomes occur under contention
- failures do not corrupt state
- no execution produces ambiguous results

A test suite that never observes rejection
is considered insufficient.

---

## Test Categories

### Single-Threaded Baseline
Purpose:
- establish expected behavior without contention

Verifies:
- successful booking below capacity
- deterministic rejection at capacity boundary

---

### Concurrent Booking Attempts
Purpose:
- expose race conditions

Setup:
- multiple concurrent attempts against the same resource
- total requested capacity exceeds availability

Expected Results:
- some attempts succeed
- some attempts are rejected
- the invariant always holds

Rejection is a **required signal**, not a failure.

---

### High Contention Stress Tests
Purpose:
- validate behavior under sustained load

Setup:
- bursty concurrent traffic
- repeated attempts over time

Expected Results:
- invariant preserved
- no partial state
- bounded failure modes

Throughput degradation is acceptable.
Invariant violation is not.

---

### Persistence Conflict Simulation
Purpose:
- validate handling of write-write conflicts

Setup:
- artificially increase conflict probability

Expected Results:
- `FAILED` outcomes occur
- no state corruption
- no implicit retries

---

### Fault Injection
Purpose:
- validate resilience to infrastructure issues

Injected Faults:
- transient persistence failures
- delayed responses
- temporary unavailability

Expected Results:
- `FAILED` outcomes
- invariant preserved
- system recovers without manual repair

---

## What Is Not Tested
The following are explicitly excluded:

- fairness between requests
- ordering guarantees
- eventual acceptance
- user experience
- performance optimization

Testing these would obscure
the correctness objective.

---

## Success Criteria
A test run is successful if:

- the invariant is never violated
- failures are explicit and bounded
- rejection is observed under contention

Absence of failure signals
indicates insufficient test coverage.

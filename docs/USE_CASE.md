# AttemptBooking Use Case

## Purpose
This use case represents a **single attempt** to reserve capacity
for a given resource under concurrent execution.

It does not guarantee success.
It guarantees **correctness**.

---

## Input
The use case accepts the following input:

- `booking_id`
- `resource_id`
- `requested_units`

All inputs are assumed to be:
- syntactically valid
- semantically valid
- already authorized

Validation is **explicitly out of scope**.

---

## Output
Each execution of this use case produces **exactly one** outcome.

The outcome is deterministic and explicit.

### Possible Outcomes

#### 1. BOOKED
The requested units were successfully reserved.
The invariant is preserved.

#### 2. REJECTED
The request was rejected due to insufficient remaining capacity.
The invariant is preserved.

This is a **correct and expected outcome** under contention.

#### 3. FAILED
The attempt could not be completed due to a technical failure
(e.g. persistence conflict, transient infrastructure issue).

This outcome does **not** imply invariant violation.

---

## Execution Semantics
The use case is executed with the following guarantees:

- No implicit retries
- No hidden loops
- No exceptions used as control flow
- No partial success

Each invocation represents **one attempt** and **one decision**.

---

## Idempotency
The use case is idempotent with respect to `booking_id`.

Repeated executions with the same identifier
must not result in duplicated reservations.

Idempotency is a correctness requirement, not an optimization.

---

## Concurrency Model
Multiple instances of this use case may execute concurrently
against the same resource.

Correctness does not rely on execution order.

The system must remain correct under:
- race conditions
- stale reads
- write conflicts

---

## Non-Goals
This use case does NOT:

- ensure fairness between concurrent requests
- guarantee eventual success
- serialize execution globally
- optimize for acceptance rate
- manage user experience

Any attempt to add these concerns
would weaken the correctness guarantees.

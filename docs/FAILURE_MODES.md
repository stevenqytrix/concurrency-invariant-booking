# Failure Modes

## Purpose
This document enumerates the **expected failure modes**
of the system under concurrent execution.

Failures are not exceptional conditions.
They are **first-class outcomes** of a contested system.

---

## Failure Classification
Failures are classified based on **their effect on correctness**
and **required system response**.

---

## Rejection Due to Invariant Violation
### Description
A booking attempt is rejected because
accepting it would violate the capacity invariant.

### Cause
- concurrent bookings exhaust remaining capacity
- stale reads propose an invalid transition

### Characteristics
- deterministic
- expected under load
- correctness-preserving

### Outcome
`REJECTED`

This is a **successful execution** from a correctness perspective.

---

## Persistence Conflict
### Description
The booking attempt fails due to a write-write conflict
detected by the persistence layer.

### Cause
- concurrent updates to the same resource
- version mismatch during optimistic locking

### Characteristics
- transient
- non-deterministic
- invariant-preserving

### Outcome
`FAILED`

The invariant remains intact.

---

## Infrastructure Failure
### Description
The booking attempt cannot complete due to
temporary infrastructure unavailability.

### Cause
- database connectivity loss
- timeout during persistence
- resource exhaustion

### Characteristics
- transient
- external to domain logic
- invariant-preserving

### Outcome
`FAILED`

---

## Invalid Input
### Description
The use case receives invalid or inconsistent input.

### Cause
- malformed request
- inconsistent identifiers

### Characteristics
- programming error
- outside expected execution model

### Outcome
Out of scope.

Input validation is explicitly excluded
from this system.

---

## Failure Visibility
All failures are surfaced explicitly
as use case outcomes.

There are:
- no hidden retries
- no swallowed errors
- no implicit compensations

Failure is part of the normal operating envelope.

---

## Non-Goals
Failure handling does NOT include:

- automatic retry policies
- exponential backoff
- dead letter queues
- user-facing error mapping

These concerns are intentionally delegated elsewhere.

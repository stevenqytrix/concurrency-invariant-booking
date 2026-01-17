# Concurrency Invariant Booking

## Purpose
This repository demonstrates how to enforce a **single critical invariant**
under **high write contention**, using **optimistic locking**
and **database-level guarantees**.

This is not a feature-rich system.
This is a correctness-focused system.

---

## The Invariant
> The number of confirmed bookings must never exceed the resource capacity.

This invariant must hold:
- under concurrent requests
- under race conditions
- under retries
- under partial failures

---

## Why This Is Hard
Under high concurrency:
- multiple clients read stale state
- writes race
- naive locking collapses throughput
- application-only checks are insufficient

This repository shows how the invariant is preserved **even when the application layer is under contention**.

---

## What This System Does
- Accepts concurrent booking attempts
- Allows valid bookings until capacity is reached
- Rejects excess attempts deterministically
- Preserves the invariant at all times

---

## What This System Explicitly Does NOT Do
- No authentication
- No users
- No roles
- No UI
- No generic CRUD
- No microservices
- No event sourcing
- No messaging

Anything not required to demonstrate concurrency correctness is intentionally excluded.

---

## Architectural Principle
> The database is the last line of defense.

If the application layer fails under race conditions,
the database guarantees invariant preservation.

---

## Intended Audience
Senior / Staff backend engineers interested in:
- concurrency correctness
- invariant enforcement
- race condition handling
- optimistic locking under load


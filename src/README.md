# Source Code Boundary

This directory intentionally contains **no implementation logic**.

All architectural decisions, invariants, concurrency guarantees,
and failure semantics are defined in the `/docs` directory.

The source code exists only to reflect **architectural boundaries**,
not behavior.

Any implementation added here must comply with the documented constraints.

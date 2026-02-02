# Entropy-Buffer Constitution

**Version**: 1.0.0 | **Ratified**: 2026-02-01

## Core Principles

### I. CSPRNG Only
All randomness MUST come from cryptographically secure sources. Never use `random.random()`.

### II. Bias Elimination
Bounded ranges MUST use rejection sampling to eliminate modulo bias.

### III. Verifiability
All random outputs MUST be accompanied by auditable entropy proofs.

### IV. Performance Awareness
Entropy pool access MUST be async-safe and non-blocking.

---

## Governance
Subordinate to platform-core constitution.

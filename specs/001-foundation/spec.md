# Specification: Foundation (v0.1.0)

**Feature ID:** 001-foundation
**Milestone:** v0.1.0 — Foundation
**Priority:** P1
**Status:** Specified
**Target Date:** Feb 2026

---

## 1. Problem Statement

The Entropy-Buffer repository is initialized but contains no implementation.
The Vindicta Platform requires a dedicated entropy source that provides
casino-grade randomness for all game mechanics. The Dice-Engine currently
depends on Python's `secrets` module directly, but needs a buffered,
pre-fetched entropy source for high-throughput scenarios (Monte Carlo
simulations in Primordia-AI, batch combat in Dice-Engine).

---

## 2. Vision

Implement a CSPRNG wrapper with rejection sampling, buffered entropy
pre-fetching, and audit-grade entropy proof generation that serves as
the foundational randomness layer for the entire platform.

---

## 3. User Stories

### US-01: Dice-Engine Developer — Entropy Source

> As the **Dice-Engine**,
> I want to **request entropy bytes from a pre-filled buffer**,
> So that **dice rolls are not blocked by entropy generation latency**.

**Acceptance Criteria:**

- [ ] `EntropyBuffer` provides `get_bytes(n)` returning `n` cryptographically random bytes
- [ ] Buffer pre-fills in background to avoid blocking
- [ ] Falls back to synchronous `secrets.token_bytes()` if buffer is empty
- [ ] Thread-safe for concurrent access

### US-02: Auditor — Entropy Proofs

> As the **Agent-Auditor-SDK**,
> I want **every entropy consumption to generate an EntropyProof**,
> So that **randomness can be verified after the fact**.

**Acceptance Criteria:**

- [ ] Each `get_bytes()` call returns both bytes and an `EntropyProof`
- [ ] Proof contains SHA-256 hash of seed, timestamp, algorithm used
- [ ] Proofs are serializable to JSON
- [ ] Proofs can be independently verified

### US-03: AI Engineer — Rejection Sampling

> As a **Primordia AI engineer**,
> I want to **generate uniformly distributed integers in arbitrary ranges**,
> So that **dice rolls are mathematically unbiased regardless of range**.

**Acceptance Criteria:**

- [ ] `sample_uniform(min, max)` uses rejection sampling (not modulo)
- [ ] Distribution passes chi-squared uniformity test
- [ ] Configurable algorithm: `csprng` or `rejection_sampling`

---

## 4. Functional Requirements

### 4.1 CSPRNG Wrapper

| Method           | Signature                                                          | Description                   |
| ---------------- | ------------------------------------------------------------------ | ----------------------------- |
| `get_bytes`      | `(n: int) -> tuple[bytes, EntropyProof]`                           | Get n random bytes with proof |
| `get_int`        | `(min: int, max: int) -> tuple[int, EntropyProof]`                 | Uniform int in [min, max]     |
| `sample_uniform` | `(min: int, max: int, algorithm: str) -> tuple[int, EntropyProof]` | Rejection sampling variant    |

### 4.2 Rejection Sampling

- Uses `secrets.token_bytes()` as entropy source
- Rejection sampling for uniform distribution (avoids modulo bias)
- Algorithm: generate random int in [0, 2^k), reject if >= range, retry
- Maximum 100 retries before raising `EntropyExhaustedError`

### 4.3 Entropy Proof Generation

| Field            | Type                                      | Description                                |
| ---------------- | ----------------------------------------- | ------------------------------------------ |
| `seed_hash`      | `str`                                     | SHA-256 of entropy bytes consumed          |
| `timestamp`      | `datetime`                                | UTC timestamp of generation                |
| `algorithm`      | `Literal["csprng", "rejection_sampling"]` | Algorithm used                             |
| `audit_trail_id` | `UUID`                                    | Unique proof identifier                    |
| `buffer_hit`     | `bool`                                    | Whether bytes came from buffer or fallback |

### 4.4 Buffer Management

| Config             | Type    | Default | Description               |
| ------------------ | ------- | ------- | ------------------------- |
| `buffer_size`      | `int`   | 4096    | Pre-allocated bytes       |
| `refill_threshold` | `float` | 0.25    | Refill when 25% remaining |
| `max_pool_size`    | `int`   | 65536   | Maximum buffer size       |

---

## 5. Non-Functional Requirements

| Category           | Requirement                                      |
| ------------------ | ------------------------------------------------ |
| **Performance**    | Buffer hit: < 0.01ms per call; Fallback: < 0.1ms |
| **Thread Safety**  | All public methods must be thread-safe           |
| **Type Safety**    | 100% strict mypy compliance                      |
| **Dependencies**   | stdlib only (secrets, hashlib, threading, uuid)  |
| **Python Version** | 3.12+                                            |

---

## 6. Out of Scope

- Async buffer pre-fetching (deferred to v0.2.0)
- Hardware entropy source detection (deferred to v1.0.0)
- PyPI publication (deferred to v1.0.0)

---

## 7. Success Criteria

| Metric             | Target                        |
| ------------------ | ----------------------------- |
| CSPRNG wrapper     | Functional with proofs        |
| Rejection sampling | Passes chi-squared test       |
| Buffer             | Pre-fill and fallback working |
| Type safety        | Zero mypy errors              |
| Test coverage      | > 90%                         |

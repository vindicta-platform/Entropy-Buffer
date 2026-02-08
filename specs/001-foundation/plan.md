# Implementation Plan: Foundation (v0.1.0)

**Spec Reference:** [spec.md](./spec.md)
**Feature ID:** 001-foundation

---

## Goal

Implement the core entropy buffer with CSPRNG wrapper, rejection sampling,
and entropy proof generation. Stdlib-only dependencies.

---

## Proposed Changes

### Core Module

#### [NEW] src/entropy_buffer/__init__.py

Package root with re-exports.

#### [NEW] src/entropy_buffer/buffer.py

- `EntropyBuffer` class with configurable pool size
- Background refill using `threading.Thread` (daemon)
- `get_bytes(n)` → `tuple[bytes, EntropyProof]`
- `get_int(min, max)` → `tuple[int, EntropyProof]`
- Thread-safe via `threading.Lock`
- Fallback to `secrets.token_bytes()` on empty buffer

#### [NEW] src/entropy_buffer/sampling.py

- `rejection_sample(min, max)` → unbiased uniform int
- Max 100 retries with `EntropyExhaustedError`
- `sample_uniform(min, max, algorithm)` dispatcher

#### [NEW] src/entropy_buffer/proofs.py

- `EntropyProof` Pydantic model (matches Vindicta-Core definition)
- `create_proof(seed_bytes, algorithm, buffer_hit)` factory
- SHA-256 hashing of consumed entropy

#### [NEW] src/entropy_buffer/exceptions.py

- `EntropyExhaustedError` — raised after max retries

### Tests

#### [NEW] tests/test_buffer.py

- Buffer fill/drain cycle
- Fallback on empty buffer
- Thread safety (concurrent access)
- Proof generation for every call

#### [NEW] tests/test_sampling.py

- Rejection sampling uniformity (chi-squared)
- Edge cases: range of 1, large ranges
- Max retry enforcement

#### [NEW] tests/test_proofs.py

- Proof fields populated correctly
- SHA-256 hash verification
- JSON serialization round-trip

---

## Verification Plan

```powershell
uv run pytest tests/ -v --tb=short
uv run mypy src/entropy_buffer/ --strict
uv run python -c "
from entropy_buffer import EntropyBuffer
buf = EntropyBuffer()
val, proof = buf.get_int(1, 6)
print(f'Roll: {val}, Proof: {proof.seed_hash[:16]}...')
"
```

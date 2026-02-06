# Entropy-Buffer Constraints

> Critical rules agents MUST follow when modifying this repository.

## ⛔ Hard Constraints

1. **No `random` Module** - Only `secrets` and `os.urandom`
2. **Pool Isolation** - No pool state exposure via API
3. **Mixing Required** - Never output raw input entropy
4. **Forward Secrecy** - Extraction destroys source bits

## 🔐 Cryptographic Rules

### Mixing Algorithm
```python
# Required: hash-based mixing
mixed = hashlib.sha256(
    pool_state + new_entropy + counter
).digest()
```

### Pool Size
- Minimum: 256 bytes
- Maximum: 4096 bytes
- Extraction: max 50% per request

### Entropy Sources
- `os.urandom` (required)
- Hardware RNG (optional, if available)
- Timing jitter (supplemental only)

## 🔒 Security Rules

- Pool state never serialized to disk unencrypted
- No entropy values in logs
- Memory cleared after extraction

## 🧪 Testing Requirements

Before merging:
- [ ] `pytest` passes
- [ ] No `random` module usage (verified)
- [ ] Statistical tests pass (NIST SP 800-22)
- [ ] Pool depletion tests pass

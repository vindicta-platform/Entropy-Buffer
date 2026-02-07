# Entropy-Buffer

A CSPRNG entropy pool manager for cryptographically secure random number generation.

## Overview

Entropy-Buffer manages a hardware-backed entropy pool using the operating system's CSPRNG, providing casino-grade randomness for game mechanics and simulations.

## Features

- **Hardware-Backed CSPRNG**: Uses OS-level secure random sources
- **Rejection Sampling**: Eliminates modulo bias in bounded ranges
- **Entropy Proofs**: Generates verifiable proof of randomness
- **Buffer Management**: Async pre-fetching for performance

## Installation

Install from source using uv:

```bash
uv pip install git+https://github.com/vindicta-platform/Entropy-Buffer.git
```

Or clone and install locally:

```bash
git clone https://github.com/vindicta-platform/Entropy-Buffer.git
cd Entropy-Buffer
uv pip install -e .
```

## Quick Start

```python
from entropy_buffer import EntropyPool

pool = EntropyPool()

# Roll a d6 with no modulo bias
result = pool.roll(1, 6)

# Get proof for auditing
proof = pool.last_proof()
print(f"Result: {result}, Proof: {proof.hex()}")
```

## Security Guarantees

- No use of `random.random()` or other weak PRNGs
- All outputs are cryptographically unpredictable
- Entropy proofs enable fairness verification

## Related Repositories

| Repository | Relationship |
|------------|-------------|
| [platform-core](https://github.com/vindicta-platform/platform-core) | Dice Engine consumer |
| [Arbiter-Predictor](https://github.com/vindicta-platform/Arbiter-Predictor) | Monte Carlo backend |

## Platform Documentation

> **📌 Important:** All cross-cutting decisions, feature proposals, and platform-wide architecture documentation live in [**Platform-Docs**](https://github.com/vindicta-platform/Platform-Docs).
>
> Any decision affecting multiple repos **must** be recorded there before implementation.

- 📋 [Feature Proposals](https://github.com/vindicta-platform/Platform-Docs/tree/main/docs/proposals)
- 🏗️ [Architecture Decisions](https://github.com/vindicta-platform/Platform-Docs/tree/main/docs)
- 📖 [Contributing Guide](https://github.com/vindicta-platform/Platform-Docs/blob/main/CONTRIBUTING.md)

## License

MIT License - See [LICENSE](./LICENSE) for details.

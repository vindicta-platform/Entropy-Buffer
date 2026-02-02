# Entropy Buffer

**Hardware-backed entropy for cryptographic operations.**

Manages a pre-filled entropy pool using the operating system's CSPRNG for non-blocking random number generation.

## Features

- **OS CSPRNG** — Uses `secrets` module
- **Pre-buffered** — Non-blocking reads
- **Thread-safe** — Async-compatible

## Installation

```bash
uv pip install git+https://github.com/vindicta-platform/Entropy-Buffer.git
```

MIT License

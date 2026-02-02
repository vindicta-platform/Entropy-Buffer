# Getting Started

```bash
uv pip install git+https://github.com/vindicta-platform/Entropy-Buffer.git
```

```python
from entropy_buffer import EntropyPool

pool = EntropyPool()
random_bytes = pool.get_bytes(32)
```

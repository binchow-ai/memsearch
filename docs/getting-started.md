# Getting Started

## Installation

```bash
pip install memsearch
```

### With additional embedding providers

```bash
pip install "memsearch[google]"      # Google Gemini
pip install "memsearch[voyage]"      # Voyage AI
pip install "memsearch[ollama]"      # Ollama (local)
pip install "memsearch[local]"       # sentence-transformers (no API key)
pip install "memsearch[all]"         # Everything
```

## How It All Fits Together

```mermaid
sequenceDiagram
    participant U as Your App
    participant M as MemSearch
    participant E as Embedding API
    participant V as Milvus

    U->>M: save_memory("Redis config...")
    U->>M: ms.index()
    M->>M: Chunk markdown
    M->>M: SHA-256 dedup
    M->>E: Embed new chunks
    E-->>M: Vectors
    M->>V: Upsert
    U->>M: ms.search("Redis?")
    M->>E: Embed query
    E-->>M: Query vector
    M->>V: Cosine similarity
    V-->>M: Top-K matches
    M-->>U: Results with source info
```

## Your First Memory Search

```python
import asyncio
from memsearch import MemSearch

async def main():
    ms = MemSearch(paths=["./memory"])
    await ms.index()
    results = await ms.search("how to configure Redis?", top_k=3)
    for r in results:
        print(r["content"][:200])

asyncio.run(main())
```

## API Keys

Set the environment variable for your chosen provider:

```bash
export OPENAI_API_KEY="sk-..."       # OpenAI (default)
export GOOGLE_API_KEY="..."          # Google
export VOYAGE_API_KEY="..."          # Voyage
```

## Milvus Backends

```mermaid
graph TD
    A[memsearch] --> B{Choose backend}
    B -->|"Default\n(zero config)"| C["Milvus Lite\n~/.memsearch/milvus.db"]
    B -->|"Self-hosted\n(multi-agent)"| D["Milvus Server\nlocalhost:19530"]
    B -->|"Managed\n(production)"| E["Zilliz Cloud\ncloud.zilliz.com"]

    style C fill:#2a3a5c,stroke:#6ba3d6,color:#a8b2c1
    style D fill:#2a3a5c,stroke:#6ba3d6,color:#a8b2c1
    style E fill:#2a3a5c,stroke:#e0976b,color:#a8b2c1
```

### Milvus Lite (default)

```python
ms = MemSearch(paths=["./docs/"], milvus_uri="~/.memsearch/milvus.db")
```

Zero config, local file. Perfect for personal use.

### Milvus Server

```python
ms = MemSearch(paths=["./docs/"], milvus_uri="http://localhost:19530")
```

### Zilliz Cloud

```python
ms = MemSearch(paths=["./docs/"], milvus_uri="https://...", milvus_token="...")
```

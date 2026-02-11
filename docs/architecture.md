# Architecture

**Markdown is the source of truth** — the vector store is just a derived index, rebuildable anytime.

## Search Flow

```mermaid
graph LR
    Q[/"Query"/] --> E[Embed] --> M[(Milvus)] --> R[Top-K Results]
```

## Ingest Flow

```mermaid
graph LR
    F["Markdown files"] --> C[Chunker] --> D{"Dedup\n(SHA-256)"}
    D -->|new| E[Embed & Upsert]
    D -->|exists| S[Skip]
```

## Watch & Flush

```mermaid
graph LR
    W[File Watcher] -->|1500ms debounce| I[Auto re-index]
    FL[Flush] --> L[LLM Summarize] --> MD["memory/YYYY-MM-DD.md"]
    MD -.->|triggers| W
```

## Key Design Decisions

### chunk_hash as Primary Key

Each chunk is identified by its SHA-256 content hash. This means:

- **Natural dedup** — identical content is never stored twice
- **No external cache** — the hash *is* the primary key in Milvus
- **Incremental indexing** — only new/changed chunks get embedded

### Single Collection

All agents share the `memsearch_chunks` collection. Physical isolation is achieved via different `milvus_uri` paths (each agent gets its own Milvus Lite database file).

### Data Flow Overview

```mermaid
graph TB
    subgraph "Source of Truth"
        MEM["MEMORY.md"]
        D1["memory/2026-02-08.md"]
        D2["memory/2026-02-09.md"]
    end

    subgraph "Processing"
        SCAN[Scanner] --> CHUNK[Chunker]
        CHUNK --> HASH["SHA-256\nDedup"]
    end

    subgraph "Storage (derived)"
        EMB[Embedding API] --> MIL[(Milvus)]
    end

    MEM & D1 & D2 --> SCAN
    HASH -->|new chunks| EMB
    MIL -->|search| RES[Results]

    style MEM fill:#2a3a5c,stroke:#e0976b,color:#a8b2c1
    style D1 fill:#2a3a5c,stroke:#e0976b,color:#a8b2c1
    style D2 fill:#2a3a5c,stroke:#e0976b,color:#a8b2c1
    style MIL fill:#2a3a5c,stroke:#6ba3d6,color:#a8b2c1
```

### Security

The entire pipeline runs locally by default — your data never leaves your machine unless you choose a remote Milvus backend or a cloud embedding provider.

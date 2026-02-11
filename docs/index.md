# memsearch

**OpenClaw's memory, everywhere.**

> memsearch extracts [OpenClaw](https://github.com/openclaw/openclaw)'s memory system into a standalone library — same markdown-first architecture, same chunking, same chunk ID format. Pluggable into *any* agent framework, backed by [Milvus](https://milvus.io/).

---

## Why memsearch?

```bash
$ cat /dev/philosophy
Markdown is the source of truth.
Simple. Readable. Git-friendly. Zero vendor lock-in.
The vector store is just a derived index — rebuildable anytime.
```

- **OpenClaw's memory, everywhere** — markdown as the single source of truth
- **Smart dedup** — SHA-256 content hashing means unchanged content is never re-embedded
- **Live sync** — file watcher auto-indexes on changes, deletes stale chunks
- **Memory flush** — LLM-powered summarization compresses old memories
- **Claude Code plugin included** — persistent memory across sessions with zero config

## Quick Install

```bash
$ pip install memsearch

$ memsearch index ./memory/
Scanning 3 files...
Chunked 42 sections, 38 new, 4 unchanged (dedup)
Embedded and upserted 38 chunks. Done.

$ memsearch search "how to configure Redis?"
[0.92] memory/2026-02-08.md:14 — "We chose Redis for caching..."
[0.87] memory/2026-02-07.md:23 — "Redis config: host=localhost..."
```

## Embedding Providers

| Provider | Install | Default Model |
|----------|---------|---------------|
| OpenAI | `memsearch` (included) | `text-embedding-3-small` |
| Google | `memsearch[google]` | `text-embedding-004` |
| Voyage | `memsearch[voyage]` | `voyage-3-lite` |
| Ollama | `memsearch[ollama]` | `nomic-embed-text` |
| Local | `memsearch[local]` | `all-MiniLM-L6-v2` |

## License

MIT

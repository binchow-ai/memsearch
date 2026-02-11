# CLI Reference

## `memsearch index`

Index markdown files into the vector store.

```bash
memsearch index ./docs/ ./notes/
memsearch index ./docs/ --provider google
memsearch index ./docs/ --force              # re-index everything
memsearch index ./docs/ --milvus-uri http://localhost:19530
```

## `memsearch search`

Search indexed memories.

```bash
memsearch search "how to configure Redis caching"
memsearch search "authentication flow" --top-k 10
memsearch search "error handling" --json-output
```

## `memsearch watch`

Auto-index on file changes.

```bash
memsearch watch ./docs/ ./notes/
memsearch watch ./docs/ --debounce-ms 3000
```

## `memsearch flush`

Summarize indexed chunks into condensed memory using an LLM.

```bash
memsearch flush
memsearch flush --llm-provider anthropic
memsearch flush --source ./docs/old-notes.md
```

## `memsearch config`

Manage configuration.

```bash
memsearch config init                # interactive wizard
memsearch config set milvus.uri http://localhost:19530
memsearch config get milvus.uri
memsearch config list --resolved     # merged config from all sources
```

## `memsearch stats`

Show index statistics.

```bash
memsearch stats
```

## `memsearch reset`

Drop all indexed data (with confirmation prompt).

```bash
memsearch reset
```

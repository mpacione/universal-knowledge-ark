---
version: "2.5.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
source: "~/universal-knowledge-ark/specs/05-library-manifest.md"
tags:
  - spec
  - universal-knowledge-ark
  - content
---

# Library Manifest

**Version:** 2.5.0

## Summary

Knowledge base content sources, preprocessing pipeline, and storage budget.

## Content Sources

| Source | Size | Articles |
|--------|------|----------|
| Wikipedia (full) | 95 GB | 6.7M |
| iFixit | 5 GB | — |
| WikiHow | 10 GB | — |
| OpenStax | 3 GB | — |
| **Total** | **230 GB** | — |

## Storage Budget

| Partition | Size | Used |
|-----------|------|------|
| /boot | 1 GB | — |
| /root | 64 GB | ~20 GB |
| /data | 404 GB | 230 GB (57%) |
| Reserved | ~11 GB | — |

## Preprocessing Pipeline

1. **ZIM Extraction** — python-libzim (no Kiwix runtime)
2. **HTML → Text** — lxml parsing, structure preservation
3. **Chunking** — 512 token chunks with 128 overlap
4. **Embedding** — MiniLM-L6-v2 (384-dim vectors)
5. **Indexing** — FAISS IVFFlat with 4096 clusters

## Import Format

```
/data/library/
├── wikipedia/
│   ├── chunks/          # Text chunks
│   ├── embeddings/      # Vector files
│   └── metadata.json    # Source info
├── ifixit/
├── wikihow/
└── openstax/
```

## Dependencies

- [[04-Technical-Design]] — RAG implementation uses this content
- [[01-Hardware-Specification]] — Storage hardware specs

## Source

Full specification: `~/universal-knowledge-ark/specs/05-library-manifest.md`

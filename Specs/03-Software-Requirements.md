---
version: "2.8.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
source: "~/universal-knowledge-ark/specs/03-software-requirements.md"
tags:
  - spec
  - universal-knowledge-ark
  - software
---

# Software Requirements

**Version:** 2.8.0 (Vault Edition)

## Summary

Functional and non-functional requirements for the Universal Knowledge Ark software system.

## Key Capabilities

- **Knowledge Query** — Natural language questions answered via RAG + LLM
- **Voice I/O** — Whisper STT, Piper TTS for hands-free operation
- **Power Management** — Na-ion voltage monitoring, graceful shutdown
- **Archival Mode** — `ark-seal` for 50-year storage integrity
- **Discovery Boot** — Orientation sequence for unknown future handlers

## Functional Requirements

| ID | Requirement |
|----|-------------|
| FR-01 | System shall respond to knowledge queries within 15s (NPU) / 30s (CPU) |
| FR-02 | System shall operate on 32GB RAM without swap |
| FR-03 | System shall provide voice input/output as primary interface |
| FR-04 | System shall monitor battery voltage and trigger graceful shutdown at 18V |
| FR-05 | System shall support archival sealing with integrity manifests |

## Archival Mode (Vault Edition)

- `ark-seal` command remounts /data read-only
- SHA-256 manifests stored with triple redundancy
- Discovery boot detects TIME_LOST (RTC < 2025)
- Reports INTACT / DEGRADED / CRITICAL status

## Dependencies

- [[01-Hardware-Specification]] — Hardware constraints inform requirements
- [[04-Technical-Design]] — Implementation of these requirements
- [[05-Library-Manifest]] — Content that software must serve

## Source

Full specification: `~/universal-knowledge-ark/specs/03-software-requirements.md`

---
version: "3.1.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
source: "~/universal-knowledge-ark/specs/04-technical-design.md"
tags:
  - spec
  - universal-knowledge-ark
  - software
  - architecture
---

# Technical Design

**Version:** 3.1.0 (Vault Edition)

## Summary

Implementation architecture for the Universal Knowledge Ark software system.

## System Architecture

- **OS:** Armbian (Debian-based) on Orange Pi 5+
- **Runtime:** Python 3.11 + systemd services
- **LLM:** Llama 3.1 8B W8A8 via RKLLM (NPU) + llama.cpp (CPU fallback)
- **RAG:** FAISS IVFFlat with MiniLM-L6-v2 embeddings
- **STT:** Whisper (accuracy) + VOSK (speed)
- **TTS:** Piper (quality) + espeak (reliability)

## Key Components

| Component | Technology |
|-----------|------------|
| Inference | RKLLM → llama.cpp fallback |
| Embeddings | MiniLM-L6-v2 (384-dim) |
| Vector Store | FAISS IVFFlat (4096 clusters) |
| Content | python-libzim ZIM extraction |
| Power Monitor | INA219 + coulomb counting |

## Vault Edition Additions

- **ArchivalManager** — `ark-seal` / `ark-unseal` commands
- **IntegrityVerifier** — SHA-256 manifest generation/verification
- **DiscoveryBootHandler** — TIME_LOST detection, orientation sequence
- **PowerMonitor** — Na-ion voltage thresholds (18V shutdown, 16V emergency)

## Dependencies

- [[03-Software-Requirements]] — Requirements this design implements
- [[05-Library-Manifest]] — Content format and preprocessing
- [[01-Hardware-Specification]] — Hardware constraints

## Source

Full specification: `~/universal-knowledge-ark/specs/04-technical-design.md`

---
version: "2.2.0"
status: current
project: "[[_Brief]]"
updated: 2025-12-29
tags:
  - spec
  - universal-knowledge-ark
  - reference
---

# Cross-References

**Version:** 2.2.0

This document tracks cross-references between specifications, canonical values, and term definitions.

## Power References

### power-budget
**Canonical:** [[01-Hardware-Specification#Power Management]]

| State | Value |
|-------|-------|
| Active | 18W @ 24V nominal |
| Idle | 5W @ 24V nominal |
| Standby | 2.8W @ 24V nominal |
| Battery | 40Ah / 960Wh (Na-ion 8S4P) |

**Referenced in:**
- [[02-Assembly-Specification#Wiring Harness]]
- [[03-Software-Requirements#Power State Machine]]
- [[04-Technical-Design#Power Manager]]
- [[Appendix-C-Operations-Manual#Daily Use]]

### voltage-thresholds
**Canonical:** [[01-Hardware-Specification#Power Management]]

| Threshold | Voltage | Notes |
|-----------|---------|-------|
| Full charge | 32.0V | 8S × 4.0V/cell |
| Nominal | 24V | 8S × 3.0V/cell |
| Full mode | 20.0V | System fully operational |
| Software LVD | 18.0V | Graceful shutdown |
| Emergency mode | 16.0V | Text retrieval only |
| Load disconnect open | 18.0V | |
| Load disconnect close | 20.0V | |
| Empty | 12.0V | 8S × 1.5V/cell |

### lazarus-phoenix
**Canonical:** [[01-Hardware-Specification#Power Management]]

| Component | Specification |
|-----------|---------------|
| Battery chemistry | Na-ion Polyanion (HiNa 32140) |
| Battery config | 8S4P (40Ah / 960Wh) |
| MPPT | Genasun GVB-8-WP boost topology |
| Solar | 3× Renogy 50W (150W total) |
| Bootstrap | 1N5408 + 47Ω passive circuit |
| Load disconnect | TL431 + latching relay |
| Analog meter | 0-30V moving-coil |

## Storage References

### storage-capacity
**Canonical:** [[01-Hardware-Specification#Storage Subsystem]]

| Partition | Size |
|-----------|------|
| NVMe raw | 480GB (SLC) |
| NVMe formatted | ~447 GiB |
| /data | 381 GB |
| Library budget | 230GB |
| Library used | ~205GB |

### thermal-limits
**Canonical:** [[01-Hardware-Specification#Enclosure & Thermal]]

| Condition | Range |
|-----------|-------|
| Operating | 0°C to +60°C |
| Storage | -20°C to +60°C |
| Thermal cutoff | 85°C (KSD9700) |

## Software References

### power-states
**Canonical:** [[03-Software-Requirements#Power State Machine]]

States: OFF → DORMANT → LAZARUS_WAKE → EMERGENCY_MODE → BOOT → ACTIVE → STANDBY

| State | Details |
|-------|---------|
| Standby power | 2.8W |
| Emergency mode voltage | 16V |
| Full mode voltage | 20V |

### rag-architecture
**Canonical:** [[04-Technical-Design#RAG Implementation]]

| Parameter | Value |
|-----------|-------|
| Embedding model | MiniLM 384-dim |
| Index type | FAISS IVFFlat |
| Clusters | 4096 |
| nprobe | 64 |
| Context tokens | 4096 |

### llm-config
**Canonical:** [[04-Technical-Design#LLM Integration]]

| Parameter | Value |
|-----------|-------|
| Model | Llama 3.1 8B W8A8 |
| RAM budget | 14GB |
| NPU speed | 3-6 tok/s |
| CPU fallback | 2-3 tok/s |

## Content References

### content-domains
**Canonical:** [[05-Library-Manifest#Source Inventory]]

| Source | Size |
|--------|------|
| Wikipedia | 95GB (6.7M articles) |
| iFixit | 5GB |
| WikiHow | 10GB |
| OpenStax | 3GB |

## Term Definitions

### Power Terms
| Term | Definition Location |
|------|---------------------|
| MPPT | [[Appendix-B-Glossary#Power Terms]] |
| Na-ion | [[Appendix-B-Glossary#Power Terms]] |
| 8S4P | [[Appendix-E-NaIon-Pack-Assembly#Pack Architecture]] |
| Lazarus-Protocol | [[Appendix-B-Glossary#Power Terms]] |
| Phoenix-Protocol | [[Appendix-B-Glossary#Power Terms]] |
| Load-Disconnect | [[Appendix-B-Glossary#Power Terms]] |
| Passive-Bootstrap | [[Appendix-B-Glossary#Power Terms]] |
| Boost-MPPT | [[Appendix-B-Glossary#Power Terms]] |

### Hardware Terms
| Term | Definition Location |
|------|---------------------|
| Genasun-GVB-8-WP | [[Appendix-B-Glossary#Hardware Terms]] |
| Analog-Voltmeter | [[Appendix-B-Glossary#Hardware Terms]] |
| E-ink | [[Appendix-B-Glossary#Hardware Terms]] |
| SBC | [[Appendix-B-Glossary#Hardware Terms]] |

### Software Terms
| Term | Definition Location |
|------|---------------------|
| RAG | [[Appendix-B-Glossary#Software Terms]] |
| graceful-degradation | [[Appendix-B-Glossary#Software Terms]] |
| quantization | [[Appendix-B-Glossary#Software Terms]] |
| vector-store | [[Appendix-B-Glossary#Software Terms]] |

### Vault Edition Terms
| Term | Definition Location |
|------|---------------------|
| SLC NAND | [[Appendix-B-Glossary#Hardware Terms]] |
| Archival Seal | [[Appendix-B-Glossary#Software Terms]] |
| Discovery Boot | [[Appendix-B-Glossary#Software Terms]] |
| Integrity Manifest | [[Appendix-B-Glossary#Software Terms]] |
| Li-SOCl2 | [[Appendix-B-Glossary#Hardware Terms]] |
| Passive Survivability | [[Appendix-B-Glossary#Architecture Terms]] |

## Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0.0 | 2025-12-25 | Initial cross-references |
| 2.0.0 | 2025-12-27 | Lazarus-Phoenix integration |
| **2.2.0** | **2025-12-29** | HIGH-GLO-001: Added Lazarus-Phoenix glossary terms |

---
version: "4.0.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
source: "~/universal-knowledge-ark/specs/01-hardware-specification.md"
tags:
  - spec
  - universal-knowledge-ark
  - hardware
---

# Hardware Specification

**Version:** 4.0.0 (Vault Edition)

## Summary

Complete bill of materials and hardware architecture for the Universal Knowledge Ark.

## Key Components

| Component | Selection | Cost |
|-----------|-----------|------|
| Compute | Orange Pi 5+ 32GB (RK3588) | $189 |
| Storage | Swissbit X-76m 480GB SLC NVMe × 2 | $1,520 |
| Battery | Na-ion 8S4P 40Ah (960Wh) | ~$600 |
| Solar | 3× Renogy 50W + Genasun GVB-8 | ~$400 |
| Display | 10.3" E-ink + 1.3" OLED | ~$300 |
| Enclosure | Pelican 1557 Air + Faraday | ~$350 |

**Total BOM:** $4,153 base + $623 contingency = **$4,776**

## Sections

1. System Architecture (5-layer cyberdeck)
2. Compute Module (Orange Pi 5+ 32GB)
3. Storage Subsystem (SLC NAND for 50-year retention)
4. Power Management (Lazarus-Phoenix architecture)
5. Display & I/O
6. Protection Systems (Faraday, thermal cutoffs)
7. Bill of Materials

## Dependencies

- [[02-Assembly-Specification]] — Build procedures reference this BOM
- [[03-Software-Requirements]] — Power states defined here
- [[04-Technical-Design]] — Implementation uses these specs
- [[Appendix-E-NaIon-Pack-Assembly]] — Battery build guide

## Source

Full specification: `~/universal-knowledge-ark/specs/01-hardware-specification.md`

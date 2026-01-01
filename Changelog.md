---
project: "[[_Brief]]"
updated: 2025-12-29
tags:
  - changelog
  - universal-knowledge-ark
---

# Changelog

All notable changes to the Universal Knowledge Ark specification.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), using [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [4.1.0] - 2025-12-29 (Vault Edition)

### Added
- SLC NAND storage (Swissbit X-76m NVMe, S-56 SD) for 50-year retention
- Li-SOCl2 RTC battery (Tadiran TL-5903) for 40-year timekeeping
- Conformal coating (MG Chemicals 422B) for humidity protection
- `ark-seal` archival mode with SHA-256 manifests
- Discovery boot sequence for unknown future handler
- Appendix: Cross-References document

### Changed
- Philosophy shift: Active stewardship → Passive survivability
- Budget: $3,165 → $4,776 (+$2,064 for storage architecture)
- All appendices updated to v5.0

## [4.0.0] - 2025-12-28 (Lazarus-Phoenix)

### Added
- Na-ion 8S4P battery pack (HiNa 32140 cells, 40Ah/960Wh)
- Genasun GVB-8-WP boost MPPT
- Passive bootstrap circuit (1N5408 + 47Ω)
- Load disconnect (TL431 + ADW1124HLW latching relay)
- Analog voltmeter (0-30V moving-coil)
- Appendix E: Na-ion Pack Assembly

### Changed
- Battery: LiFePO4 → Na-ion polyanion chemistry
- MPPT: Victron SmartSolar (buck) → Genasun GVB-8-WP (boost)
- Solar: 50W single → 150W array (3× 50W)
- Voltage thresholds: 12V system → 24V system

### Removed
- LiFePO4 battery references
- Victron MPPT configuration
- LVD module (replaced by load disconnect)

## [3.0.0] - 2025-12-27

### Added
- Complete issue tracking (appendix-d)
- All 120 baseline issues resolved

### Changed
- Assembly spec: v3.9.0 with complete procedures
- Hardware spec: v2.8.0 with MPNs and mounting hardware

## [2.0.0] - 2025-12-26

### Added
- Case upgrade: Pelican 1500 → 1557 Air
- Protection chain schematic (§6.8)
- GPIO pin assignments
- Cold spare imaging procedure

### Changed
- All specs rewritten to v2.0.0 baseline
- OPi5+ platform confirmed
- 10.3" IT8951 display confirmed

## [1.0.0] - 2025-12-25

### Added
- Project initialization
- 00-project-overview.md — Mission and design philosophy
- 01-hardware-specification.md — Component selection
- 02-assembly-specification.md — Build instructions
- 03-software-requirements.md — Functional requirements
- 04-technical-design.md — RAG and LLM implementation
- 05-library-manifest.md — Knowledge base content
- appendix-a-threat-model.md — Protection mechanisms
- appendix-b-glossary.md — Term definitions
- appendix-c-operations-manual.md — User procedures
- Context manager integration

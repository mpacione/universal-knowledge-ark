---
version: "4.0.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
source: "~/universal-knowledge-ark/specs/02-assembly-specification.md"
tags:
  - spec
  - universal-knowledge-ark
  - assembly
---

# Assembly Specification

**Version:** 4.0.0 (Vault Edition)

## Summary

Step-by-step build procedures for assembling the Universal Knowledge Ark.

## Major Sections

1. **Enclosure Modification** — Pelican 1557 Air preparation, Faraday lining
2. **Component Prep** — SLC storage identity verification, conformal coating
3. **Internal Layout** — 5-layer cyberdeck assembly
4. **Wiring Harness** — Power distribution, signal routing
5. **Battery Pack** — Na-ion 8S4P assembly (see [[Appendix-E-NaIon-Pack-Assembly]])
6. **Validation Tests** — Lazarus wake, cold spare imaging, thermal verification

## Key Procedures

- **Faraday Installation** — Copper mesh + conductive gasket for >40dB attenuation
- **Conformal Coating** — MG Chemicals 422B with UV verification
- **Cold Spare Imaging** — Clonezilla procedure for SLC NVMe backup
- **Lazarus Wake Test** — Verify 0V recovery via solar

## Dependencies

- [[01-Hardware-Specification]] — Component list and BOM
- [[Appendix-C-Operations-Manual]] — Post-build usage procedures
- [[Appendix-E-NaIon-Pack-Assembly]] — Battery pack build guide

## Source

Full specification: `~/universal-knowledge-ark/specs/02-assembly-specification.md`

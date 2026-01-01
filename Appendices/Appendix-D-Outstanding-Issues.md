---
version: "2.0.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
tags:
  - spec
  - universal-knowledge-ark
  - issues
  - tracking
---

# Appendix D: Outstanding Issues

**Version:** 2.0.0 (Final - All Resolved)

## Overview

This appendix tracks specification issues discovered during cross-reference audits. All issues have been resolved.

**Severity Definitions:**

| Level | Definition | Impact |
|-------|------------|--------|
| CRITICAL | Build cannot proceed until resolved | Blocks procurement or assembly |
| HIGH | Significant inconsistency or missing content | May cause assembly errors or test failures |
| MEDIUM | Minor inconsistency or unclear content | Could cause confusion but workarounds exist |
| LOW | Editorial or cosmetic issue | No functional impact |

## Issue Summary

| Severity | Total | Resolved |
|----------|-------|----------|
| CRITICAL | 6 | 6 |
| HIGH | 17 | 17 |
| MEDIUM | 9 | 9 |
| LOW | 7 | 7 |
| **Total** | **39** | **39** |

**Status: BUILD READY** — All issues resolved.

## Critical Issues (All Resolved)

### CRIT-ASM-001: §3.2 Contains Victron Configuration Instead of Genasun
- **Resolved:** 2025-12-28
- **Resolution:** Replaced Victron SmartSolar configuration with comprehensive Genasun GVB-8-WP section

### CRIT-ASM-002: §5.1 Layer Diagram Shows LiFePO4 and Victron
- **Resolved:** 2025-12-28
- **Resolution:** Updated LAYER 5 ASCII diagram for Na-ion 8S4P + Genasun

### CRIT-ASM-003: §5.3 Describes LiFePO4 Battery Mounting
- **Resolved:** 2025-12-28
- **Resolution:** §5.3 rewritten for Na-ion 8S4P. Created [[Appendix-E-NaIon-Pack-Assembly]]

### CRIT-ASM-004: §5.6 Describes Victron MPPT Mounting
- **Resolved:** 2025-12-28
- **Resolution:** §5.6 rewritten for Genasun GVB-8-WP boost MPPT

### CRIT-ASM-005: §6.1 Power Distribution Diagram Shows Old Architecture
- **Resolved:** 2025-12-29
- **Resolution:** Complete rewrite for Lazarus-Phoenix: Na-ion 8S4P, Genasun, passive bootstrap, load disconnect

### CRIT-ASM-006: §6.8 Protection Chain Schematic Shows Old Architecture
- **Resolved:** 2025-12-28
- **Resolution:** Complete rewrite with 38-wire schedule, passive bootstrap, analog voltmeter, load disconnect

## High Issues (All Resolved)

| ID | Issue | Resolution |
|----|-------|------------|
| HIGH-ASM-001 | §6.2 Wire Color Code Shows 12V | Updated to 24V (v3.2.0) |
| HIGH-ASM-002 | §6.3 References LVD Module | Updated to Load Disconnect |
| HIGH-ASM-003 | §8.1 Power System Tests Wrong Voltages | Fixed thresholds for 24V Na-ion |
| HIGH-ASM-004 | §8.1.1 LVD Verification Wrong Values | Rewritten as Load Disconnect Verification |
| HIGH-ASM-005 | Missing §3.4 Cable Schedule | Complete wire schedule W01-W34 |
| HIGH-ASM-006 | Missing Na-ion Cell Pack Procedure | Created Appendix E |
| HIGH-ASM-007 | Missing Passive Bootstrap Procedure | Created §5.7 |
| HIGH-ASM-008 | Missing Load Disconnect Procedure | Created §5.8 |
| HIGH-ASM-009 | Missing Analog Voltmeter Procedure | Created §5.9 |
| HIGH-ASM-010 | Missing Lazarus Wake Test | Created §8.9 |
| HIGH-OVW-001 | Project Overview Lists Victron | Fixed to Genasun (v4.0.1) |
| HIGH-GLO-001 | Orphaned LiFePO4/Victron Terminology | Glossary v4.3.0 update |
| HIGH-OPS-001 | LiFePO4 Voltage Chart | Ops Manual v4.4 with Na-ion 8S |
| HIGH-STG-001 | Replace Transcend NVMe with SLC | Hardware Spec v4.0.0 |
| HIGH-STG-002 | Replace Kingston SD with SLC | Hardware Spec v4.0.0 |
| HIGH-STG-003 | Implement ark-seal Archival Mode | Software Req v2.8.0 |
| HIGH-STG-004 | Implement Discovery Boot | Technical Design v3.1.0 |

## Medium Issues (All Resolved)

| ID | Issue | Resolution |
|----|-------|------------|
| MED-ASM-001 | §4.2 Missing Voltmeter Cutout | Added to hole schedule |
| MED-ASM-002 | §8.7 Solar Charge Time | Split normal vs Lazarus |
| MED-ASM-003 | Version History Incomplete | Updated to v3.9.0 |
| MED-CTX-001 | state.json Out of Sync | Cleared and updated |
| MED-THR-001 | Threat Model LiFePO4 Refs | Updated to Na-ion |
| MED-STG-005 | Add Conformal Coating Procedure | Assembly Spec v4.0.0 |
| MED-STG-006 | Upgrade RTC to Li-SOCl2 | Hardware Spec v4.0.0 |
| MED-STG-007 | Update Spare Parts for SLC | Hardware Spec v4.0.0 |
| MED-STG-008 | Revise Storage Budget | Hardware Spec v4.0.0 |

## Low Issues (All Resolved)

| ID | Issue | Resolution |
|----|-------|------------|
| LOW-ASM-001 | §2.3 Missing Load Disconnect Parts | Added to list |
| LOW-OVW-001 | Version Mismatch | Fixed v4.0.1 entry |
| LOW-OPS-002 | §7 Storage LiFePO4 Refs | Updated for Na-ion |
| LOW-STG-009 | Add SLC/Archival Terms to Glossary | Glossary v5.0.0 |
| LOW-STG-010 | Update Threat Model for SLC | Threat Model v5.0 |
| LOW-STG-011 | Add Archival Procedures to Ops | Ops Manual v5.0 |
| LOW-STG-012 | Revise Project Overview | Overview v4.1.0 |

## Key Architectural Changes

### Lazarus-Phoenix (Dec 2025)
- LiFePO4 → Na-ion 8S4P chemistry
- Buck MPPT → Genasun GVB-8-WP boost MPPT
- Added passive bootstrap circuit (1N5408 + 47Ω)
- Added load disconnect (TL431 + latching relay)
- Added analog voltmeter (0-30V moving-coil)
- Budget: $3,165

### Vault Edition (Dec 2025)
- TLC NAND → SLC NAND (50-year retention)
- Swissbit X-76m NVMe + S-56 SD
- Li-SOCl2 RTC battery (Tadiran TL-5903)
- Conformal coating (MG Chemicals 422B)
- ark-seal archival mode with SHA-256 manifests
- Discovery boot sequence
- Budget: $4,776 (+$2,064)

## Resolution Timeline

| Date | Milestone |
|------|-----------|
| 2025-12-28 | Lazarus-Phoenix integration audit - 22 issues identified |
| 2025-12-28 | All 6 CRITICAL issues resolved |
| 2025-12-28 | All 11 HIGH assembly issues resolved |
| 2025-12-29 | Deep audit - 4 new appendix issues found |
| 2025-12-29 | Vault Edition - 12 new storage issues created |
| 2025-12-29 | All 39 issues resolved - BUILD READY |

## Final Budget

| Category | Amount |
|----------|--------|
| Base BOM | $4,153 |
| Contingency (15%) | $623 |
| **Total** | **$4,776** |

## Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0.0 | 2025-12-28 | Initial issue tracking (22 issues from Lazarus-Phoenix audit) |
| 1.1.0 | 2025-12-29 | Pre-build deep audit (+4 appendix issues) |
| 1.5.0 | 2025-12-29 | Vault Edition (+12 storage issues) |
| **2.0.0** | **2025-12-29** | **ALL 39 ISSUES RESOLVED. BUILD READY.** |

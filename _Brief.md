---
version: "4.1.0"
status: build-ready
created: 2024-12-01
updated: 2025-12-29
budget_base: 3165
budget_contingency: 475
budget_total: 3640
depends_on: []
tags:
  - project
  - hardware
  - ai
  - resilience
summary: "Solar-powered, offline AI assistant in ruggedized Pelican case for civilizational resilience. Vault Edition complete - 50-year passive survivability."
---

# Universal Knowledge Ark

## Summary

A solar-powered, completely offline AI assistant housed in a ruggedized Pelican case, designed to preserve and deliver critical knowledge during infrastructure collapse scenarios. The "Vault Edition" architecture enables 50-year sealed storage without maintenance.

## Vision

One device creates a leader. Ten thousand devices create a stabilization force.

The goal: distributed network of Arks held by community pillars (librarians, fire chiefs, nurses, ham operators, farmers). No community more than a day's walk from a working doctor, engineer, or teacher—via the AI.

## Current Status

**Phase:** BUILD READY (Vault Edition Complete)
**Issues:** 130/130 resolved (0 open)
**Last Session:** 2025-12-29 — All issues resolved, specifications frozen

## Key Specifications

| Component | Spec |
|-----------|------|
| Compute | Orange Pi 5 Plus (RK3588, 16GB RAM) |
| Model | Llama 3.1 8B W8A8 quantized |
| Storage | 512GB NVMe + SLC cold spare |
| Battery | Na-ion 8S4P (40Ah / 960Wh) |
| Solar | 3× Renogy 50W (150W total) |
| MPPT | Genasun GVB-8-WP (boost topology) |
| Display | Waveshare 10.3" E-ink |
| Enclosure | Pelican 1557 Air (IP67) |
| Runtime | ~48 hours active, ~170 hours standby |

## Architecture Highlights

### Lazarus-Phoenix Power System
- **Na-ion chemistry** with aluminum collectors (0V-safe for decade storage)
- **Boost MPPT** charges depleted battery directly from solar
- **Passive bootstrap** circuit enables recovery from complete discharge
- **Load disconnect** prevents boot-loop drain below 18V
- **Analog voltmeter** provides decade-stable status without electronics

### Vault Edition Storage
- **SLC NAND** (Swissbit X-76m + S-56) for 50-year data retention
- **Li-SOCl2 RTC battery** (Tadiran TL-5903) for 40-year timekeeping
- **Conformal coating** (MG Chemicals 422B) for humidity protection
- **ark-seal** archival mode with SHA-256 manifests
- **Discovery boot** sequence for unknown future handlers

## Key Documents

| Document | Version | Purpose |
|----------|---------|---------|
| [[00-Project-Overview]] | 4.1.0 | Vision, philosophy, constraints |
| [[01-Hardware-Specification]] | 4.0.0 | BOM, power budget, components |
| [[02-Assembly-Specification]] | 4.0.0 | Build procedures, wiring |
| [[03-Software-Requirements]] | 2.8.0 | Functional requirements |
| [[04-Technical-Design]] | 3.1.0 | Implementation architecture |
| [[05-Library-Manifest]] | 2.5.0 | Knowledge base content |
| [[Appendix-A-Threat-Model]] | 5.0 | Risk analysis |
| [[Appendix-B-Glossary]] | 5.0.0 | Term definitions |
| [[Appendix-C-Operations-Manual]] | 5.0 | Usage procedures |
| [[Appendix-D-Outstanding-Issues]] | 1.23.0 | Issue tracking (all resolved) |
| [[Appendix-E-NaIon-Pack-Assembly]] | 1.0.0 | Battery build guide |

## Key Decisions

See [[_Decisions]] for full log.

| Date | Decision |
|------|----------|
| 2025-12-27 | Lazarus-Phoenix power architecture (Na-ion + boost MPPT) |
| 2025-12-29 | Vault Edition storage (SLC NAND for 50-year retention) |
| 2025-12-29 | Active stewardship → Passive survivability philosophy |

## Open Questions

See [[_Questions]] — Currently empty (all resolved).

## Budget

| Category | Amount |
|----------|--------|
| Base BOM | $3,165 |
| Contingency (15%) | $475 |
| **Total** | **$3,640** |

Note: Storage subsystem exempt from cost ceiling per design philosophy.

## Next Steps

1. Order components from BOM
2. Build Na-ion battery pack (see [[Appendix-E-NaIon-Pack-Assembly]])
3. Assemble per [[02-Assembly-Specification]]
4. Image storage per cold spare procedures
5. Validate per test procedures
6. Execute ark-seal for archival deployment

## Related

- [[_Decisions]] — Decision log
- [[_Questions]] — Open questions (empty)
- Source repository: `~/universal-knowledge-ark/`

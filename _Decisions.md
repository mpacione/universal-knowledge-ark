---
project: "[[_Brief]]"
updated: 2025-12-29
tags:
  - decisions
---

# Decisions

Decision log for [[_Brief|Universal Knowledge Ark]].

## 2025-12-29 — Vault Edition Complete

### Storage Architecture: SLC NAND for 50-Year Retention
**Context:** Consumer TLC/QLC SSDs have 10-year retention max. Need 50-year sealed storage.
**Decision:** Use enterprise SLC NAND (Swissbit X-76m + S-56) with Li-SOCl2 RTC battery.
**Consequences:** +$2,064 BOM increase, but enables passive survivability goal.

### Philosophy Shift: Active Stewardship → Passive Survivability
**Context:** Original design assumed periodic maintenance. Vault Edition assumes sealed storage.
**Decision:** Design for "seed vault" scenario — system must function after 50 years without human intervention.
**Consequences:** Drives all storage, RTC, and conformal coating decisions.

## 2025-12-28 — Lazarus-Phoenix Architecture

### Power System: Na-ion + Boost MPPT
**Context:** LiFePO4 cannot survive decade storage at 0V. System must wake from complete discharge.
**Decision:** Na-ion battery (0V-safe) + Genasun GVB-8-WP boost MPPT + passive bootstrap circuit.
**Consequences:** Enables decade dormancy capability. Removes cold-weather charging restrictions.

### Load Disconnect: TL431 + Latching Relay
**Context:** SBC boot-loop at low voltage drains battery to death.
**Decision:** Hardware load disconnect at 18V prevents boot attempts below safe threshold.
**Consequences:** Requires manual reconnect after recovery, but prevents unrecoverable state.

## 2025-12-26 — Case Migration

### Enclosure: Pelican 1557 Air
**Context:** Original Pelican 1500 basement depth left only 6mm clearance above batteries.
**Decision:** Migrate to 1557 Air — 196mm base depth = 49mm clearance. 93% more volume.
**Consequences:** Internal layout redesign required, but enables proper cable routing.

## Earlier Decisions

- **E-ink display** — Zero power when static, readable in sunlight
- **Orange Pi 5 Plus** — RK3588 NPU for on-device inference
- **Llama 3.1 8B** — Balance of capability and resource requirements
- **RAG architecture** — Retrieval-augmented generation for knowledge access
- **Faraday protection** — EMP/CME survivability requirement

## Declined Proposals

### 2025-12-26 — Pelican 1535 Air Migration (DECLINED)

**Proposal:** Migrate from Pelican 1557 Air to Pelican 1535 Air (airline carry-on compliant)

**Key Changes:**
- Enclosure: 1557 Air → 1535 Air
- Battery: 100Ah → 80Ah (1280Wh → 1024Wh)
- Layout: Vertical stack → Horizontal "Valley/Dumbbell"

**Decline Rationale:**
1. Carry-on compliance not required
2. Thermal risk in Valley layout unmodeled
3. 25% less internal volume
4. Case is 2 lbs heavier (wheels/trolley)
5. Wheel mechanisms = additional failure points
6. 1557 stackable, no moving parts

**Revisit trigger:** If carry-on compliance becomes hard requirement

## Resolution Notes (from Issue Resolution Sessions)

### HIGH-005: Cold Spare Imaging
Cold spare is full system clone, not just recovery image. Two methods: drive swap (faster) and USB adapter (no reboot). Annual update cadence.

### HIGH-024: Display USB Architecture
Display USB routes: deck-mounted source → coiled cable in cavity → storm window access port → IT8951. Zero-hinge wiring preserved.

### HIGH-027: Negative Bus Implementation
Floating DC system requires dedicated negative bus (not "ground"). Added 6-position 30A barrier terminal block.

### Case Upgrade Analysis
Pelican 1500 basement depth (109mm after partitioning) left only 6mm clearance above batteries. 1557 Air provides 196mm base depth = 49mm clearance. Orange for emergency visibility.

### Lazarus-Phoenix Edit Plan
Complete architectural change from LiFePO4 to Na-ion:
- Bioenno 50Ah LiFePO4 → HiNa 32140 Na-ion 8S4P
- Victron SmartSolar → Genasun GVB-8-WP boost MPPT
- Single 50W panel → 3× 50W panels
- Added: passive bootstrap, load disconnect, analog voltmeter

### Cross-Reference Audits
- **2025-12-25:** 8 issues found (4 HIGH, 4 MEDIUM) — all resolved
- **2025-12-26:** 12 issues found (0 HIGH, 9 MEDIUM, 3 LOW) — all resolved
- **2025-12-27:** Deep scan verified all specs consistent with Lazarus-Phoenix

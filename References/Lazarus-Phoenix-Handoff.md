---
version: "1.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-28
decision_type: architecture
tags:
  - spec
  - universal-knowledge-ark
  - architecture
  - power
---

# Lazarus-Phoenix Architecture Handoff

**Version:** 1.0
**Status:** DECISION COMPLETE — Ready for procurement/build
**Budget:** $3,165 (Lazarus-Phoenix) → $4,776 (Vault Edition)

## Executive Summary

This document captures the complete technical decision process for solving the Universal Knowledge Ark's **decade-dormancy power system problem**. The original LiFePO4 battery architecture was discovered to have a fatal flaw that would brick the device after years of storage.

### Final Architecture

| Component | Specification |
|-----------|---------------|
| Battery | 8S4P Na-ion (HiNa 32140, Polyanion, 40Ah/960Wh) |
| MPPT | Genasun GVB-8-WP boost topology (8A limit) |
| Solar | 3× 50W 12V panels (150W total) |
| Status | Analog 0-30V moving-coil voltmeter |
| Bootstrap | 1N5408 + 47Ω passive circuit |
| Load Disconnect | TL431 + ADW1124HLW latching relay |

**Key Outcome:** System can wake from true 0V after 10+ years dormancy, reach Emergency Mode in ~4-6 hours of sunlight, with zero silicon in the critical feedback path.

## Part 1: The Problem — Why LiFePO4 Fails

### BMS Quiescent Drain
Every LiFePO4 battery contains a BMS that draws 1-5mA continuously.

**The math:**
- 1mA × 24 hours × 365 days × 10 years = 87.6 Ah drained
- 100Ah battery → completely depleted in ~10 years from BMS alone

### Deep Discharge Electrochemistry
When LiFePO4 cells drop below ~2.5V:
1. **Copper dissolution:** Cu anode collector oxidizes
2. **Ion migration:** Dissolved copper ions migrate through electrolyte
3. **Dendrite formation:** Copper redeposits as metallic dendrites
4. **Internal short circuit:** Dendrites pierce separator
5. **Thermal runaway:** Potential fire hazard

**Result:** A LiFePO4 battery stored at 50% SoC will eventually self-discharge to dangerous levels.

## Part 2: Lazarus Protocol — Sodium-Ion Solution

### Why Na-ion Works at 0V

**The key difference: Aluminum current collectors on both electrodes.**

| Chemistry | Anode Collector | 0V Behavior |
|-----------|-----------------|-------------|
| Li-ion/LiFePO4 | Copper | Copper dissolves below 2.5V |
| Na-ion | Aluminum | Stable at all potentials |

At 0V, a Na-ion cell is electrochemically inert — no voltage potential to drive parasitic reactions.

### Cathode Chemistry Selection: Polyanion (NFPP)

| Cathode Type | 0V Stability | Moisture Sensitivity |
|--------------|--------------|---------------------|
| Layered Oxide | Poor | HIGH |
| Prussian Blue | Medium | MEDIUM |
| **Polyanion (NFPP)** | Excellent | LOW ✅ |

### Cell Selection: HiNa 32140

| Parameter | Value |
|-----------|-------|
| Chemistry | Polyanion (NFPP) |
| Capacity | 10Ah/cell |
| Voltage | 1.5V (empty) → 4.0V (full) |
| Form factor | 32mm × 140mm cylindrical |
| Cost | ~$7/cell |

**Pack: 8S4P** = 32 cells, 24V nominal, 40Ah, 960Wh

## Part 3: Phoenix Protocol — Boost MPPT

### The 0V Wake Paradox

Standard buck MPPT controllers power logic from battery side.
At 0V battery → controller is dead → can't start charging.

### Boost Topology Solution

Boost controllers step UP voltage (panel → battery):
- Controller logic runs from PV input
- Passive current path: PV+ → Inductor → Diode → Battery+
- Current flows whenever PV > battery, no active switching required

### Genasun GVB-8-WP Selection

| Feature | Specification |
|---------|---------------|
| Input | 5V-60V |
| Output | Boost to 32V (custom for 8S Na-ion) |
| Efficiency | 99% peak |
| 0V Recovery | YES (datasheet explicit) |
| Capacitors | Ceramic/tantalum (no electrolytics) |
| Enclosure | IP68 potted |
| Self-consumption | 0.2W |

## Part 4: Final Architecture Block Diagram

```
SOLAR ARRAY (3×50W 12V parallel)
         │
    ┌────┼────────────────────┐
    │    │                    │
 Passive  Genasun          Analog
Bootstrap GVB-8-WP        Voltmeter
(1N5408)  Boost MPPT       0-30V
    │    │                    │
    └────┴────────────────────┘
                │
        8S4P Na-ion Pack
         40Ah / 960Wh
         + JK BMS (8S)
                │
         Load Disconnect
        (TL431 + Relay)
        closes at 20V
                │
        Wide-Input DC-DC
         12-32V → 5V
                │
           OPi5+ 32GB
```

### Passive Bootstrap Circuit

**Purpose:** Unconditional 0V recovery via Ohm's Law.

| Component | Specification |
|-----------|---------------|
| Diode | 1N5408 (NOT Schottky — low reverse leakage) |
| Resistor | 47Ω 25W vitreous enamel |
| Fuse | 2A slow-blow |

At 0V battery with 22V Voc panel: I = 22V / 47Ω = 0.47A trickle charge.

### Analog Voltmeter

**Purpose:** User feedback without any silicon.

Moving-coil meters use permanent magnets and wire coils — no decade-degradation risk.

| Voltage | State |
|---------|-------|
| <16V | Still charging, wait |
| 16-20V | Emergency Mode possible |
| 20-24V | Full boot available |
| 24-32V | Normal operation |
| 32V | Fully charged |

### Load Disconnect

**Purpose:** Prevent boot-loop that drains battery.

| Threshold | Action |
|-----------|--------|
| >20V | Relay closes, load connected |
| <18V | Relay opens, load disconnected |

## Part 5: Lazarus Wake Sequence

### Timeline (150W panel, full sun)

| Hour | Voltage | Mode | Status |
|------|---------|------|--------|
| 0 | 0V | — | Deploy panels, connect |
| 0-0.5 | 0→5V | Bootstrap | Diode trickle charges |
| 0.5-4 | 5→16V | MPPT (8A) | Genasun active |
| 4 | 16V | Emergency | Text retrieval available |
| 4-6 | 16→20V | MPPT | Load disconnect closes |
| 6 | 20V | Full | LLM available |
| 6-18 | 20→32V | MPPT | Full charge |

## Part 6: Bill of Materials (Power System)

| Item | Qty | Cost |
|------|-----|------|
| HiNa 32140 Na-ion cells | 32 | $224 |
| JK BMS BD4A8S4P | 1 | $45 |
| Genasun GVB-8-WP-Li-CV | 1 | $200 |
| Renogy 50W 12V Foldable | 3 | $210 |
| 1N5408 diode | 2 | $1 |
| 47Ω 25W resistor | 1 | $5 |
| Analog voltmeter 0-30V | 1 | $12 |
| TL431 + relay circuit | 1 | $9 |
| DC-DC 12-32V → 5V | 2 | $30 |
| Misc (connectors, wire) | — | $49 |
| **Power System Total** | | **$785** |

## Part 7: Known Risks

| Risk | Mitigation |
|------|------------|
| HiNa cells unavailable | Alt: CATL/BYD Na-ion (verify Polyanion) |
| Genasun custom unavailable | Alt: Standard 48V with series panels |
| Cell capacity lower than rated | Oversize to 8S5P |
| BMS doesn't wake from 0V | Bypass BMS during Lazarus wake |
| Decade storage untested | Extrapolated from 6-month validation |

## Part 8: Glossary

| Term | Definition |
|------|------------|
| **Lazarus Protocol** | Research identifying Na-ion for decade-dormancy |
| **Phoenix Protocol** | Research identifying boost MPPT for 0V wake |
| **Polyanion** | Na-ion cathode with phosphate framework |
| **Boost MPPT** | Steps UP voltage (panel < battery) |
| **Load disconnect** | Prevents boot until battery sufficient |
| **Emergency Mode** | Low-power text retrieval at 16V |
| **Full Mode** | Complete LLM inference at 20V |

## Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0 | 2025-12-28 | Initial handoff document |

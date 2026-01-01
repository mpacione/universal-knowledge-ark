---
version: "1.0.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-28
tags:
  - spec
  - universal-knowledge-ark
  - battery
  - assembly
---

# Appendix E: Na-ion Cell Pack Assembly

**Version:** 1.0.0

## Overview

This appendix provides detailed instructions for assembling a Na-ion 8S4P battery pack from individual HiNa 32140 cells. If you purchased a pre-assembled pack, skip this appendix entirely.

**Estimated Time:** 4-6 hours
**Skill Level:** Intermediate (requires spot welding experience)
**Safety:** Low risk if procedures followed (Na-ion is inherently safer than Li-ion)

## 1. Required Materials

| Item | Specification | Qty | Notes |
|------|---------------|-----|-------|
| HiNa 32140 Na-ion cells | 10Ah, Polyanion (NFPP), 3.1V nominal | 32 | Verify chemistry before purchase |
| JK BMS BD4A8S4P | 8S active balancing, Bluetooth | 1 | Or equivalent 8S Na-ion BMS |
| Nickel strip | 0.15mm × 8mm, pure nickel | 2m | NOT nickel-plated steel |
| 3D-printed cell holders | 32140 format, 2×4 arrangement | 8 | See §2 for STL files |
| Fish paper | 0.5mm thickness, red or black | 1 sheet | Electrical insulation |
| Kapton tape | 25mm width | 1 roll | High-temp insulation |
| Heat shrink | Large diameter (fits pack) | 1m | Optional outer wrap |
| Conformal coating | MG Chemicals 422B or equivalent | 1 can | Moisture protection |
| XT60 connectors | Male (pack output) | 1 pair | Gold-plated |
| Balance lead wire | 22AWG silicone, 9 colors | 2m | B0-B8 connections |
| JST-XH connector | 9-pin (8S balance) | 1 | Matches JK BMS |

## 2. Required Tools

| Tool | Specification | Purpose |
|------|---------------|---------|
| Spot welder | kWeld, Malectrics, or Sunkko 737G+ | Nickel strip welding |
| Multimeter | DC voltage, mΩ resistance | Cell testing |
| Nitrile gloves | — | Skin oil protection |
| Safety glasses | — | Weld spark protection |
| Flush cutters | — | Nickel strip trimming |
| Soldering iron | 60W+ | XT60 and balance wires |
| Heat gun | — | Heat shrink |
| Calipers | Digital | Cell sorting |
| Marker | Fine tip permanent | Cell labeling |

**3D-Printed Cell Holders:**
- Download STL: `assets/32140-holder-2x4.stl` (or design to fit 32mm cells in 2×4 grid)
- Print in PETG or ABS (NOT PLA — melting point too low)
- 100% infill for rigidity

## 3. Cell Preparation

### 3.1 Incoming Inspection

Inspect all 32 cells before assembly:

| Check | Method | Reject If |
|-------|--------|-----------|
| Physical damage | Visual inspection | Dents, swelling, rust, leaking |
| Terminal condition | Visual | Corroded, damaged threads |
| Voltage | Multimeter | <2.5V or >4.1V (storage voltage should be 3.0-3.3V) |
| Weight | Kitchen scale | >5% deviation from mean |

**Label each cell** with number 1-32 using permanent marker on the side (not terminals).

### 3.2 Capacity Matching

For optimal pack performance, group cells by capacity:

1. Charge all cells to 4.0V (full) using bench charger (1A rate)
2. Discharge each cell at 2A to 1.5V, recording Ah delivered
3. Sort cells into 8 groups of 4 by capacity (within 5% of each other)
4. Each group of 4 becomes one parallel block

**Shortcut:** If purchasing matched cells from same production batch, skip capacity matching. Cells will be within 3% naturally.

### 3.3 Terminal Cleaning

1. Wipe both terminals with isopropyl alcohol
2. Lightly sand with 400-grit if oxidized
3. Wipe again with IPA
4. Handle with nitrile gloves from this point forward

## 4. Pack Architecture

### 4.1 Configuration Diagram

```
═══════════════════════════════════════════════════════════════
                     8S4P PACK TOPOLOGY
═══════════════════════════════════════════════════════════════

PACK+  ──┬── P1+ ─┬─ Cell 1  ─┬── P1- ──┬── P2+ ─┬─ Cell 5  ─┬── ...
         │        ├─ Cell 2  ─┤         │        ├─ Cell 6  ─┤
         │        ├─ Cell 3  ─┤         │        ├─ Cell 7  ─┤
         │        └─ Cell 4  ─┘         │        └─ Cell 8  ─┘
         │                              │
         │      (4P group 1)            │      (4P group 2)
         │         3.0V                 │         3.0V
         └──────────────────────────────┘
                  Series connection

Full chain: P1 ─ P2 ─ P3 ─ P4 ─ P5 ─ P6 ─ P7 ─ P8  = 8S = 24V nominal
Each Px: 4 cells in parallel = 4P = 40Ah
Total: 8S4P = 24V × 40Ah = 960Wh

═══════════════════════════════════════════════════════════════
```

### 4.2 Physical Arrangement

```
TOP VIEW (terminals facing up)

┌─────────────────────────────────────────┐
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐        │
│  │ P1  │ │ P2  │ │ P3  │ │ P4  │  Row A │
│  │(4P) │ │(4P) │ │(4P) │ │(4P) │        │
│  └─────┘ └─────┘ └─────┘ └─────┘        │
│                                         │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐        │
│  │ P5  │ │ P6  │ │ P7  │ │ P8  │  Row B │
│  │(4P) │ │(4P) │ │(4P) │ │(4P) │        │
│  └─────┘ └─────┘ └─────┘ └─────┘        │
│                                         │
│  ◄──── ~280mm ────►                     │
└─────────────────────────────────────────┘
       ▲
       │
    ~160mm

SIDE VIEW

┌─────────────────────────────────────────┐
│  ○ ○ ○ ○   ○ ○ ○ ○   ○ ○ ○ ○   ○ ○ ○ ○ │ ← Positive terminals
│  │ │ │ │   │ │ │ │   │ │ │ │   │ │ │ │ │
│  │ │ │ │   │ │ │ │   │ │ │ │   │ │ │ │ │   ~120mm
│  │ │ │ │   │ │ │ │   │ │ │ │   │ │ │ │ │   (cell length
│  │ │ │ │   │ │ │ │   │ │ │ │   │ │ │ │ │    + holders)
│  ○ ○ ○ ○   ○ ○ ○ ○   ○ ○ ○ ○   ○ ○ ○ ○ │ ← Negative terminals
└─────────────────────────────────────────┘
```

## 5. Assembly Procedure

### 5.1 Build Parallel Groups (4P)

For each of the 8 parallel groups (P1-P8):

1. **Insert 4 cells into holder:**
   - All cells same orientation (positive up)
   - Cells should fit snugly with no rattle

2. **Cut nickel strips:**
   - Positive bus: ~70mm (spans 4 positive terminals)
   - Negative bus: ~70mm (spans 4 negative terminals)

3. **Spot weld positive bus:**
   - Place strip across 4 positive terminals
   - 2 welds per terminal (8 welds total)
   - Weld settings: ~40-50 joules per spot (adjust for your welder)
   - Verify each weld: tug strip firmly, should not lift

4. **Spot weld negative bus:**
   - Same procedure on bottom terminals
   - 8 welds total

5. **Verify parallel group:**
   - Measure voltage across group: should read ~3.0-3.3V
   - Measure resistance between any two cells: <5mΩ

6. **Insulate:**
   - Apply fish paper between adjacent groups
   - Wrap exposed nickel with Kapton tape

**Repeat for all 8 groups.**

### 5.2 Connect Series (8S)

Connect parallel groups in series (P1- to P2+, P2- to P3+, etc.):

1. **Arrange groups:**
   - Row A: P1, P2, P3, P4 (left to right)
   - Row B: P5, P6, P7, P8 (left to right)
   - Alternate orientation so series connections are short

2. **Cut series interconnect strips:**
   - ~50mm each, 7 strips total (P1-P2, P2-P3, ... P7-P8)

3. **Weld series connections:**
   - P1 negative bus → P2 positive bus
   - 4 welds per connection (2 each side)
   - Insulate each joint with Kapton tape immediately after welding

4. **Final connections:**
   - P1 positive = PACK+ (main positive output)
   - P8 negative = PACK- (main negative output)
   - Leave unwelded tabs for main output wires

### 5.3 Verify Pack

| Test | Procedure | Expected |
|------|-----------|----------|
| Pack voltage | Multimeter PACK+ to PACK- | 24V ±2V (depends on SOC) |
| Group voltages | Probe each series junction | ~3.0-3.3V per group |
| Balance | Max voltage difference between groups | <100mV |
| Short check | Resistance PACK+ to any metal surface | >1MΩ |

**If group voltages differ by >200mV:** Stop. Check for weld failures, reversed cells, or mismatched cells.

### 5.4 Install BMS

1. **Mount BMS module:**
   - Position alongside pack or on top
   - Use double-sided tape or small screws to plastic bracket

2. **Connect balance leads:**
   - B0 (black) → P1 negative (PACK-)
   - B1 (brown) → P1 positive / P2 negative junction
   - B2 (red) → P2 positive / P3 negative junction
   - ...continue through...
   - B8 (white) → P8 positive (PACK+)
   - Solder directly to nickel strips or use ring terminals
   - Use JST-XH connector on BMS end

3. **Connect main leads:**
   - BMS B- → PACK- (nickel strip)
   - BMS P- → External negative output (to Negative Bus)
   - BMS P+ / B+ → PACK+ → External positive output (to Battery Bus)
   - Use 10AWG silicone wire, solder to nickel strips
   - Add XT60 connectors on output ends

4. **Configure BMS via Bluetooth:**
   - Install JK BMS app on phone
   - Connect to BMS
   - Set parameters:
     - OVP: 4.00V/cell (32.0V pack)
     - UVP: 1.50V/cell (12.0V pack)
     - Balance start: 3.80V/cell
     - Balance delta: 30mV

## 6. Final Assembly

### 6.1 Insulation

1. **Fish paper wrap:**
   - Wrap entire pack perimeter with fish paper
   - Secure with Kapton tape
   - Ensure no exposed nickel visible

2. **Conformal coating:**
   - Spray all solder/weld joints with conformal coat
   - Allow 2 hours to cure
   - Provides moisture barrier

3. **Heat shrink (optional):**
   - Slide large-diameter heat shrink over entire pack
   - Apply heat gun evenly
   - Creates unified, clean appearance

### 6.2 Final Verification

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| Visual | Inspect all connections | No exposed metal, secure wrap |
| Voltage | Multimeter on XT60 output | 12-32V range |
| BMS | App shows all 8 cell groups | All voltages displayed |
| Balance | Cell delta in app | <50mV |
| Capacity | Full discharge test (optional) | >38Ah (95% rated) |
| Thermal | Charge at 10A for 30 min | No cell >45°C |

**Label pack** with:
- Chemistry: Na-ion NFPP
- Configuration: 8S4P
- Capacity: 40Ah / 960Wh
- Build date
- Builder initials

## 7. Troubleshooting

| Issue | Possible Cause | Solution |
|-------|----------------|----------|
| Low pack voltage | One or more cells reversed | Check individual group voltages, find reversed cell |
| Cell imbalance >200mV | Bad weld, high-resistance joint | Re-weld affected connection |
| BMS won't communicate | Balance leads reversed | Verify B0-B8 order |
| BMS shows OVP | Cells charged above 4.0V | Discharge pack slightly, reconfigure BMS |
| Pack warm during charge | Internal short, high-resistance weld | Stop charging, inspect all welds |
| Capacity <90% rated | Mismatched cells, aged cells | Re-test individual cells, replace weak ones |

## Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0.0 | Dec 2025 | Initial appendix created for Lazarus-Phoenix Na-ion pack assembly |

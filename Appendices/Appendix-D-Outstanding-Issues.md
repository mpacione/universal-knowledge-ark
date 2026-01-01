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

---

## Overview

This appendix tracks unresolved specification issues discovered during cross-reference audits. Issues are classified by severity and organized by affected document.

**Severity Definitions:**

| Level | Definition | Impact |
|-------|------------|--------|
| CRITICAL | Build cannot proceed until resolved | Blocks procurement or assembly |
| HIGH | Significant inconsistency or missing content | May cause assembly errors or test failures |
| MEDIUM | Minor inconsistency or unclear content | Could cause confusion but workarounds exist |
| LOW | Editorial or cosmetic issue | No functional impact |

**Status Definitions:**

| Status | Definition |
|--------|------------|
| OPEN | Issue identified, not yet addressed |
| IN-PROGRESS | Work underway |
| RESOLVED | Fix committed to document |
| DEFERRED | Intentionally postponed |
| WON'T-FIX | Determined not to be an issue |

**Note:** LOW-STG-009, LOW-STG-010, and LOW-STG-011 require appendix files (appendix-a-threat-model.md, appendix-b-glossary.md, appendix-c-operations-manual.md) which do not currently exist in the repository. These issues are deferred until the appendix files are created.

**UPDATE 2025-12-29:** Appendix files located in `/appendices/` directory. All three issues now RESOLVED.

---

## Issue Summary

| Severity | Open | In-Progress | Resolved | Total |
|----------|------|-------------|----------|-------|
| CRITICAL | 0 | 0 | 6 | 6 |
| HIGH | 0 | 0 | 17 | 17 |
| MEDIUM | 0 | 0 | 9 | 9 |
| LOW | 0 | 0 | 7 | 7 |
| **Total** | **0** | **0** | **39** | **39** |

---

## Document: 02-assembly-specification.md

**Audit Context:** Post-Lazarus-Phoenix integration audit (2025-12-28). Document header claims v3.0.0 alignment with Lazarus-Phoenix architecture, but major sections contain orphaned LiFePO4/Victron content that was never updated.

### CRITICAL Issues

#### CRIT-ASM-001: §3.2 Contains Victron Configuration Instead of Genasun

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28  
**Affected Section:** §3.2 Firmware & Configuration, Item 2

**Resolution:**
Replaced Victron SmartSolar configuration with comprehensive Genasun GVB-8-WP section:
- Factory programming details (32.0V absorption, 26.4V float)
- Bench test verification procedure (18V input, measure output)
- 0V wake capability explanation
- Cross-references to Hardware Spec §4.3 and §4.5

---

#### CRIT-ASM-002: §5.1 Layer Diagram Shows LiFePO4 and Victron

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28  
**Affected Section:** §5.1 5-Layer Cyberdeck Architecture, LAYER 5 - BASEMENT

**Resolution:**
Updated LAYER 5 ASCII diagram:
- "NA-ION 8S4P BATTERY" (was "LiFePO4 BATTERY")
- "40Ah / 960Wh (24V nominal)" (was "12V 100Ah")
- "32× HiNa 32140 + JK BMS"
- "GENASUN GVB-8-WP Boost MPPT (18V→32V)" (was "VICTRON SMARTSOLAR 75/15")
- Added Passive Bootstrap and Analog Voltmeter to basement items

---

#### CRIT-ASM-003: §5.3 Describes LiFePO4 Battery Mounting (Complete Rewrite Required)

**Status:** RESOLVED  
**Resolved:** 2025-12-28  
**Resolution:** §5.3 completely rewritten with Na-ion 8S4P hybrid approach. Main section covers installation (pre-assembled or DIY options, foam pocket construction, verification). Full cell pack fabrication procedure moved to new appendix-e-naion-pack-assembly.md (spot welding, BMS integration, troubleshooting). Version 3.1.0.  
**Discovered:** 2025-12-28  
**Affected Section:** §5.3 Battery Mounting (Dual 50Ah Configuration)

**Current Content (ENTIRELY WRONG):**
- References "Two Bioenno BLF-1250A 50Ah batteries in parallel"
- Dimensions: 212 × 107 × 147mm per battery
- Anderson Powerpole Y-harness wiring
- Kaizen foam pockets for pre-assembled batteries

**Required Content:**
Complete rewrite for Na-ion 8S4P cell pack assembly:
- 32× HiNa 32140 cylindrical cells (3.1V nominal, 10Ah each)
- 8S4P configuration: 8 series × 4 parallel
- Spot-welded nickel strip interconnects (0.15mm × 8mm)
- 3D-printed cell holders or Kaizen foam with cell-diameter holes
- JK BMS BD4A8S4P wiring to cell groups
- Balance lead connections (8S = 9 wires)
- Pack dimensions: ~280 × 150 × 140mm (estimated based on 32140 cell size)
- Thermal considerations: foam insulation for cold weather

**Cross-Reference:** 01-hardware-specification.md §4.4 (Battery specifications)

---

#### CRIT-ASM-004: §5.6 Describes Victron MPPT Mounting (Complete Rewrite Required)

**Status:** RESOLVED  
**Resolved:** 2025-12-28  
**Resolution:** §5.6 completely rewritten for Genasun GVB-8-WP boost MPPT. New subsections §5.6.1-5.6.9 cover unit specifications (80×50×25mm, IP68 potted), thermal considerations (no airflow needed), compact foam pocket mounting, terminal identification, installation procedure with polarity warnings, verification tests including 0V battery wake, and Genasun vs Victron comparison table. Wire schedule updated (W19-W22). Version 3.2.0.  
**Discovered:** 2025-12-28  
**Affected Section:** §5.6 MPPT Mounting

**Current Content (ENTIRELY WRONG):**
- References "Victron SmartSolar 75/15"
- Dimensions: 100 × 113 × 40mm
- Bluetooth connectivity, VictronConnect app
- Airflow slots for convection cooling

**Required Content:**
Complete rewrite for Genasun GVB-8-WP:
- Dimensions: 80 × 50 × 25mm (much smaller than Victron)
- Potted/sealed construction (no airflow slots needed)
- No Bluetooth/app (fixed-voltage boost controller)
- Mounting: M3 screws through flange holes, or VHB tape
- Orientation: Any (no convection requirements)
- Wire entry: Screw terminals for PV+/PV-/BAT+/BAT-
- Location: Basement layer, near battery pack

**Cross-Reference:** 01-hardware-specification.md §4.3 (Genasun GVB-8-WP specifications)

---

#### CRIT-ASM-005: §6.1 Power Distribution Diagram Shows Entire Old Architecture

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Resolution:** Complete rewrite of §6.1 for Lazarus-Phoenix architecture. New diagram includes: 3×50W solar array, passive bootstrap circuit, Genasun GVB-8-WP boost MPPT, Na-ion 8S4P battery system, analog voltmeter, TL431/latching relay load disconnect (18V/20V thresholds), wide-input DC-DC (12-32V→5V), wire segment references, voltage threshold summary table, and architecture comparison table. Also updated §6.2 (12V→24V) and §6.3 (protection chain sequence). Version 3.2.0.  
**Discovered:** 2025-12-28  
**Affected Section:** §6.1 Power Distribution Diagram

**Current Content (ENTIRELY WRONG):**
- Shows "VICTRON SMARTSOLAR 75/15"
- Shows "LiFePO4 BATTERY 12V 100Ah [Integrated BMS]"
- Shows "Meanwell SD-15A-5" DC-DC (9.2-18V input)
- Shows "LVD MODULE (11.5V cutoff)"
- Shows 12V power path throughout

**Required Content:**
Complete diagram rewrite showing:
- SOLAR ARRAY: 3× 50W panels (150W total) via XT60
- PASSIVE BOOTSTRAP: 1N5408 diode + 47Ω 25W resistor (parallel to MPPT)
- GENASUN GVB-8-WP: Boost MPPT (18V→32V)
- NA-ION 8S4P: 40Ah/960Wh with JK BMS BD4A8S4P
- ANALOG VOLTMETER: 0-30V moving-coil (parallel to battery)
- LOAD DISCONNECT: TL431 + Panasonic ADW1124HLW latching relay (18V open, 20V close)
- DPST KILL SWITCH: (after load disconnect)
- PROTECTION CHAIN: NTC → Fuse Block → INA219 → KSD9700
- WIDE-INPUT DC-DC: 12-32V → 5V @ 5A
- 24V power path nominal

**Cross-Reference:** 01-hardware-specification.md §1.1 Block Diagram, §4 Power Management

---

#### CRIT-ASM-006: §6.8 Protection Chain Schematic Shows Entire Old Architecture

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28  
**Affected Section:** §6.8 Protection Chain Schematic & Wire Schedule

**Resolution:**
Complete rewrite of §6.8.1-6.8.7 with Lazarus-Phoenix architecture:
- New ASCII schematic with nodes A-T (expanded from A-R)
- Passive bootstrap circuit (1N5408 + 47Ω 25W resistor)
- Analog voltmeter 0-30V moving-coil connection
- Load disconnect (TL431 + ADW1124HLW latching relay)
- 38-wire schedule (expanded from 30) with new segments:
  - W11-W17: Load disconnect circuit
  - W18-W19: Analog voltmeter
  - W28-W30: Passive bootstrap path
- Negative bus updated with new wire IDs
- Test points with 18V/20V/32V thresholds
- Fuse coordination with load disconnect behavior table
- Assembly checklist with 0V recovery test procedure

**Verification:** Review §6.8 circuit topology for Genasun boost MPPT, Na-ion 8S4P pack, and all protection layers.

---

### HIGH Issues

#### HIGH-ASM-001: §6.2 Wire Color Code Shows 12V Instead of 24V

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28  
**Affected Section:** §6.2 Wire Color Code

**Resolution:** Updated wire color code table: "Battery + / 12V main" → "Battery + / 24V main". Fixed as part of CRIT-ASM-005 §6.1 rewrite (v3.2.0).

---

#### HIGH-ASM-002: §6.3 References LVD Module Instead of Load Disconnect

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28  
**Affected Section:** §6.3 Protection Chain Wiring Sequence

**Resolution:** Updated protection chain sequence to: "Load Disconnect → Kill Switch → NTC → Fuse Block → INA219 → KSD9700 → DC-DC → Loads". Load disconnect now correctly shown first in chain (prevents boot-loop). Fixed as part of CRIT-ASM-005 §6.1 rewrite (v3.2.0).

---

#### HIGH-ASM-003: §8.1 Power System Tests Use Wrong Voltage Thresholds

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28  
**Affected Section:** §8.1 Power System Tests

**Resolution:** Fixed §8.1 table: Battery voltage 12.8-13.6V → 24-32V. Renamed "LVD function" → "Load disconnect function" with correct thresholds (22V→16V sweep, 18V±0.5V open, 20V±0.5V close). Also fixed §8.5 INA219 test voltage (12V → 24V) and §8.7 Software/Hardware LVD tests. Version 3.3.0.

---

#### HIGH-ASM-004: §8.1.1 LVD Verification Uses Wrong Bench PSU Values

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28  
**Affected Section:** §8.1.1 LVD Verification Procedure

**Resolution:** Complete rewrite of §8.1.1 as "Load Disconnect Verification Procedure". 10-step bench PSU test sequence (24V→16V sweep). Pass criteria: 18V±0.5V open, 20V±0.5V close. Added TL431 resistor divider notes and threshold calculation reference. Fixed as part of HIGH-ASM-003 (v3.3.0).

---

#### HIGH-ASM-005: Missing §3.4 Cable Schedule for Lazarus-Phoenix Components

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28  
**Affected Section:** §3.4 Cable Preparation

**Resolution:**
Complete rewrite of §3.4.2 Power Cables. Replaced obsolete 12V/LiFePO4 cable table (Anderson Y-Harness, Victron references) with Lazarus-Phoenix wire schedule (W01-W34):
- Main Protection Chain (W01-W10): Na-ion pack to DC-DC
- Load Disconnect Circuit (W11-W17): TL431 + latching relay wiring
- Analog Voltmeter (W18-W19): Battery bus to 0-30V meter
- 5V Load Distribution (W20-W23): DC-DC to USB-C breakout and TPA2016
- Solar Input Path (W24-W27): XT60 to Genasun MPPT
- Passive Bootstrap (W28-W30): 1N5408 diode + 47Ω resistor
- Scavenger Input (W31-W34): Binding posts to Schottky diode

Added cross-reference to §6.8.2 Wire Schedule as authoritative source.

Added new §3.4.7 External Cables & BMS Leads covering:
- Solar panel Y-harness prep (3× MC4 parallel to XT60 adapter)
- BMS balance lead verification (9-wire JST-XH B0-B8)

Version 3.4.0.

**Cross-Reference:** 01-hardware-specification.md §4.5, §4.6, §4.7, §8.1 BOM

---

#### HIGH-ASM-006: Missing Na-ion Cell Pack Assembly Procedure (New Section Required)

**Status:** RESOLVED  
**Resolved:** 2025-12-28  
**Resolution:** appendix-e-naion-pack-assembly.md (v1.0.0) created during CRIT-ASM-003 resolution. Covers all 9 requirements: cell testing/matching, 3D-printed holders, spot welding procedure (kWeld, 0.15mm nickel), 8S series connections, JK BMS BD4A8S4P installation with Bluetooth config, pack verification, insulation (fish paper, Kapton, conformal coating, heat shrink), and troubleshooting. Foam enclosure covered in 02-assembly-specification.md §5.3.3. Cross-reference from §5.3.2 already present.  
**Discovered:** 2025-12-28  
**Affected Section:** New section required (suggest §5.3.1 or replace §5.3 entirely)

**Missing Content:**
No procedure exists for assembling the Na-ion 8S4P cell pack:
1. Cell testing: Verify all 32× HiNa 32140 cells at 3.0-4.0V
2. Cell matching: Group by voltage (±0.02V) for parallel strings
3. Cell holder assembly: 3D-printed holders or Kaizen foam jig
4. Spot welding: 0.15mm nickel strips, 8mm width, kWeld settings
5. Series connections: 8 series groups with nickel bus bars
6. BMS installation: JK BD4A8S4P balance lead connections
7. Pack testing: Verify total voltage (24-32V), balance current
8. Insulation: Kapton tape on exposed connections
9. Foam enclosure: Pack retention in basement layer

**Cross-Reference:** 01-hardware-specification.md §4.4, §8.1 BOM (cell and BMS specs)

---

#### HIGH-ASM-007: Missing Passive Bootstrap Assembly Procedure

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28  
**Affected Section:** New §5.7 Passive Bootstrap Assembly

**Resolution:**
Created §5.7 Passive Bootstrap Assembly with 4 subsections:
- §5.7.1 Purpose & Components: 1N5408 silicon diode (NOT Schottky for low reverse leakage), 47Ω 25W vitreous enamel wirewound resistor, optional 2A slow-blow fuse. Cross-references Hardware Spec §4.5.
- §5.7.2 Thermal Mounting: Power dissipation calculation (10.3W at Voc), bolt-to-deck mounting procedure with M4 hardware, ASCII diagram, thermal behavior notes.
- §5.7.3 Assembly & Wiring: Wire schedule W28-W30 per §6.8.2, step-by-step assembly sequence, polarity check diagram.
- §5.7.4 Verification: Pre-power checks (diode polarity, resistor value, continuity, thermal mount), 0V recovery functional test with expected values table.

Also updated §7.1 Phase 2 integration sequence (steps 8-10) to include bootstrap assembly.

Version 3.6.0.

---

#### HIGH-ASM-008: Missing Load Disconnect Assembly Procedure

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28

**Resolution:**
§5.8 Load Disconnect Assembly created with 5 subsections:
- §5.8.1 Purpose & Components: TL431A ×2 (SET/RESET), 2N2222A drivers, Panasonic ADW1124HLW latching relay, resistor dividers (R1-R4), pull-ups (R5-R6), base resistors (R7-R8), pre-charge resistors (R9-R10), pulse capacitors (C1-C2), flyback diodes (D1-D2). Threshold calculations: SET ~19.5V, RESET ~16.5V.
- §5.8.2 Circuit Topology: Complete ASCII schematic with dual-threshold pulse-latch design, capacitor pre-charge network.
- §5.8.3 Perfboard Assembly: 50×70mm layout with component placement diagram, TL431A and 2N2222A pinouts, 8-step assembly sequence.
- §5.8.4 Relay Mounting & Wiring: Separate relay mounting (M3×6 screws), wire schedule W31-W37, connection diagram.
- §5.8.5 Verification: Pre-power checks table (resistor values, polarity, coil resistance), 10-step bench PSU test procedure (12V→20V→24V→17V sweep), threshold verification table, troubleshooting guide.

**Cross-Reference:** 01-hardware-specification.md §4.7

---

#### HIGH-ASM-009: Missing Analog Voltmeter Installation Procedure

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28

**Resolution:**
§5.9 Analog Voltmeter Installation created with 5 subsections:
- §5.9.1 Purpose & Component: 0-30V DC moving-coil panel meter specs (d'Arsonval mechanism, ±2% accuracy, ~45×35mm face). Voltage-to-state reference table (0V dormant through 32V full).
- §5.9.2 Panel Cutout: Location (280mm × 80mm per §4.2), 45×35mm rectangular cutout procedure (10-step including pilot holes, Dremel/jigsaw cutting, filing). "Measure twice" warning for manufacturer variance.
- §5.9.3 Mounting Procedure: Three mounting methods (bezel clips, rear screws, front bezel ring). 5-step installation. Silicone seal consideration for humidity protection.
- §5.9.4 Wiring: Wire schedule W18/W19 per §6.8.2. ASCII connection diagram. 8-step installation procedure. Polarity warning.
- §5.9.5 Verification: Pre-power visual checks, 6-step bench PSU calibration test (0V/12V/18V/24V/30V/32V), in-system verification against multimeter and TP2. Troubleshooting table.

Version 3.8.0.

**Cross-Reference:** 01-hardware-specification.md §4.6

---

#### HIGH-ASM-010: Missing Lazarus Wake Validation Test

**Status:** RESOLVED  
**Discovered:** 2025-12-28  
**Resolved:** 2025-12-28

**Resolution:**
§8.9 Lazarus Wake Validation Test created with 7 subsections:
- §8.9.1 Test Overview: Purpose, prerequisites (bench PSU, multimeter, timer), duration estimates (15-30 min bench, 4-8 hours real solar).
- §8.9.2 Test Configuration Options: Option A (bench PSU, controllable) vs Option B (real solar, highest fidelity).
- §8.9.3 Option A Bench PSU Procedure: 4-phase test sequence—Phase 1: Passive Bootstrap Verification (0V→12V, 7 steps), Phase 2: MPPT Takeover (12V→20V, 6 steps), Phase 3: System Boot (>20V, 5 steps), Phase 4: Low-Voltage Disconnect (5 steps).
- §8.9.4 Option B Real Solar Procedure: 8-step outdoor test with expected timeline table (2-4 hours per phase).
- §8.9.5 Pass/Fail Criteria: 7 quantitative criteria including bootstrap current (0.25-0.55A), threshold voltages, recovery times.
- §8.9.6 Troubleshooting: 6 symptom/cause/fix entries for common failures.
- §8.9.7 Documentation: Test record form template.

Also updated §8.8 Pre-Deployment Checklist to include Lazarus wake test.

Version 3.9.0.

**Cross-Reference:** 01-hardware-specification.md §4.5, §4.3

---

### MEDIUM Issues

#### MED-ASM-001: §4.2 Deck Faceplate Missing Voltmeter Cutout

**Status:** RESOLVED  
**Resolved:** 2025-12-28  
**Resolution:** Added voltmeter rectangular cutout (45×35mm) to hole schedule at position 280mm/80mm. Panel-mounted per Hardware Spec §4.6. Version 3.5.0.  
**Discovered:** 2025-12-28  
**Affected Section:** §4.2 Deck Faceplate Layout, Hole Schedule

**Issue:**
Hardware spec §4.6 states analog voltmeter is on "Deck faceplate, visible when lid open" but the hole schedule in §4.2 does not include a voltmeter cutout.

**Required Action:**
Either:
A) Add voltmeter rectangular cutout to hole schedule (if panel-mounted), OR
B) Clarify in hardware spec that voltmeter is internal-only (not panel-mounted)

**Cross-Reference:** 01-hardware-specification.md §4.6

---

#### MED-ASM-002: §8.7 Runtime Test Needs Solar Charge Time Verification

**Status:** RESOLVED  
**Resolved:** 2025-12-28  
**Resolution:** Split solar charge time into two scenarios: Normal (20%→100% in <6 hours) and Lazarus (0V→bootable in 4-8 hours via passive bootstrap + MPPT). Version 3.5.0.  
**Discovered:** 2025-12-28  
**Affected Section:** §8.7 Full System Integration Tests

**Current Content:**
```
| Solar charge time | 0%→100% in full sun | <8 hours |
```

**Issue:**
The <8 hours estimate was based on 50W single panel. With 150W array (3× 50W), charge time should be faster. However, Lazarus wake from 0V involves passive bootstrap first, which is slower.

**Required Action:**
Update to specify two scenarios:
1. Normal recharge (battery >20V): 0%→100% in ~6 hours with 150W array
2. Lazarus wake (battery 0V): 0V→bootable in ~4-8 hours (passive bootstrap + MPPT)

**Cross-Reference:** 01-hardware-specification.md §4.2, §4.5

---

#### MED-ASM-003: Version History Claims Lazarus-Phoenix Alignment But Content Not Updated

**Status:** RESOLVED  
**Resolved:** 2025-12-28  
**Discovered:** 2025-12-28  
**Affected Section:** §10 Version History, v3.0.0 entry

**Resolution:**
Version history §10 updated to accurately reflect all remediation work:
- Expanded 3.2.0 entry to cover CRIT-ASM-004/005/006 + HIGH-ASM-001/002 (power system documentation batch)
- Added missing 3.5.0 entry (Batch A cleanup: LOW-ASM-001, MED-ASM-001, MED-ASM-002)
- Added 3.7.0 entry (HIGH-ASM-008: Load disconnect assembly)
- Added 3.8.0 entry (HIGH-ASM-009: Analog voltmeter installation)
- Added 3.9.0 entry (HIGH-ASM-010: Lazarus wake validation test)

Version history now complete from 1.0.0 through 3.9.0 with accurate issue tracking.

---

### LOW Issues

#### LOW-ASM-001: §2.3 Organization List Missing Load Disconnect Components

**Status:** RESOLVED  
**Resolved:** 2025-12-28  
**Resolution:** Added "resistor divider R1/R2, pulse capacitor" to protection components list. Version 3.5.0.  
**Discovered:** 2025-12-28  
**Affected Section:** §2.3 Organization

**Current Content:**
```
- Protection components (fuses, breaker, load disconnect relay, KSD9700, TL431)
```

**Issue:**
The list mentions "load disconnect relay" and "TL431" which is correct, but should also mention:
- Resistor divider components (for TL431 threshold setting)
- Pulse capacitor (for relay coil activation)

**Required Action:**
Minor update to protection components list for completeness.

---

## Document: 00-project-overview.md

### HIGH Issues

#### HIGH-OVW-001: §6.1 Hardware Platform Lists Victron Instead of Genasun

**Status:** RESOLVED  
**Resolved:** 2025-12-28  
**Resolution:** Fixed §6.1 (Victron SmartSolar → Genasun GVB-8-WP) and §6.3 (Victron MPPT → Genasun boost MPPT). Version 4.0.1.  
**Discovered:** 2025-12-28  
**Affected Section:** §6.1 Hardware Platform

**Current Content (WRONG):**
```
- Victron SmartSolar 75/15 industrial MPPT
```

**Required Content:**
```
- Genasun GVB-8-WP boost MPPT (custom Na-ion profile)
```

**Cross-Reference:** 01-hardware-specification.md §4.3

---

## Document: .context/state.json

### MEDIUM Issues

#### MED-CTX-001: state.json Claims All Issues Resolved But Content Not Updated

**Status:** RESOLVED  
**Resolved:** 2025-12-28  
**Discovered:** 2025-12-28  
**Affected Section:** session_summary, resolved_*_issues arrays

**Resolution:**
state.json updated to reflect actual remediation completion:
- `current_focus`: Updated to "All issues resolved - BUILD phase ready"
- `pending_changes`: Cleared (all completed)
- `open_questions`: Cleared (MED-ASM-001 resolved)
- `pending_xref_updates`: Cleared (Victron→Genasun done)
- `session_summary`: Updated to reflect all 22 issues resolved
- `issue_summary`: All counts show 0 open
- `audit_findings.result`: Changed from FAIL to PASS
- `next_phase`: Changed to BUILD

---

## Document: 00-project-overview.md (Additional Issues)

**Audit Context:** Pre-build deep audit (2025-12-29). Minor discrepancies discovered.

### LOW Issues

#### LOW-OVW-001: Version Mismatch and Stale Spec Table

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Sections:** Header, §5.1 Specification Status

**Issues Found:**
1. Header shows "Version: 4.0.1" but Version History only goes to v4.0.0
2. §5.1 table shows Assembly Spec at v3.0.0 but actual version is v3.9.0

**Required Actions:**
1. Add v4.0.1 entry to Version History explaining the minor update
2. Update §5.1 Specification Status table with correct versions:
   - 02-assembly-specification.md: 3.0.0 → 3.9.0
   - appendix-d-outstanding-issues.md: 1.23.0 → 1.1.0 (renumbered)

**Resolution:**
Project Overview updated:
- §5.1: Corrected all spec versions (assembly 3.9.0, appendices 4.x, issues 1.3.0)
- §8: Added v4.0.1 entry to Version History

---

## Document: appendix-a-threat-model.md

**Audit Context:** Pre-build deep audit (2025-12-29). Appendix not updated for Lazarus-Phoenix architecture.

### MEDIUM Issues

#### MED-THR-001: LiFePO4 References Not Updated to Na-ion

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Sections:** §1.5 Component Failure, §3.1 Preparation, §4 Failure Modes

**Current Content (WRONG):**
- §1.5: "LiFePO4 chemistry (2000+ cycles, tolerates partial charge)"
- §3.1: "Charge battery to 50-60% (optimal for LiFePO4 storage)"
- §4: Uses old voltage thresholds (11.5V, 12V)

**Required Content:**
- Update all battery chemistry references: LiFePO4 → Na-ion (Polyanion NFPP)
- Update storage procedure: Na-ion tolerates 0V storage (aluminum collectors)
- Add Lazarus-Phoenix decade dormancy protocol reference
- Update voltage thresholds: 16V hardware LVD, 18V software LVD, 24V nominal, 32V full
- Update §3.2 Periodic Maintenance for 0V-safe storage
- Update §3.3 Reactivation with Lazarus wake procedure reference

**Cross-Reference:** 01-hardware-specification.md §4.4, §4.16, appendix-c-operations-manual.md

**Resolution:**
Threat Model v4.1 updated:
- §1.5: LiFePO4 → Na-ion polyanion (0V-safe, decade dormancy)
- §3.1: Storage charge now optional (Na-ion tolerates 0V)
- §3.2: Removed mandatory recharge, added Lazarus wake reference
- §3.3: Updated reactivation to use analog voltmeter + Lazarus wake
- §4: Fixed voltage threshold (11.5V → 18V/16V hybrid LVD)

---

## Document: appendix-b-glossary.md

**Audit Context:** Pre-build deep audit (2025-12-29). Glossary contains orphaned LiFePO4/Victron terminology and missing Lazarus-Phoenix terms.

### HIGH Issues

#### HIGH-GLO-001: Orphaned LiFePO4/Victron Terminology Throughout

**Status:** RESOLVED  
**Discovered:** 2025-12-29  
**Resolved:** 2025-12-29  
**Affected Sections:** Architecture Terms, Power Terms, Hardware Terms

**Resolution:**
Glossary v4.3.0 comprehensive update:
- Updated 6 existing terms: Basement (Na-ion 8S4P), BMS (JK BD4A8S4P), Hybrid LVD (16V/18V), SOC (12V-32V curve), PV Bootstrap (Genasun), Victron SmartSolar (deprecated note)
- Removed obsolete term: Eco Mode (12V concept no longer applicable)
- Added 10 new Lazarus-Phoenix terms: Na-ion, Genasun GVB-8-WP, Passive Bootstrap, Load Disconnect, Analog Voltmeter, Lazarus Protocol, Phoenix Protocol, 8S4P, HiNa 32140, JK BMS BD4A8S4P
- Added NFPP acronym (Na-ion cathode chemistry)
- Fixed RKLLM speed (25-40 tok/s → 3-6 tok/s for 8B models)
- Added Lazarus Wake to Operational Terms

**Cross-Reference:** 01-hardware-specification.md §4, cross-refs.json terms section

---

## Document: appendix-c-operations-manual.md

**Audit Context:** Pre-build deep audit (2025-12-29). Operations manual contains LiFePO4 voltage charts and missing Lazarus procedures.

### HIGH Issues

#### HIGH-OPS-001: LiFePO4 Voltage Chart and Missing Lazarus Procedures

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Sections:** §3.1 Reading Battery Status, §3.3 Charging Times, Missing Lazarus section

**Current Content (WRONG):**

§3.1 Voltage to Charge table shows LiFePO4 range:
```
| Voltage | Approx. SOC |
|---------|-------------|
| 13.4V | 100% |
| 13.2V | 90% |
| ... |
| 11.5V | 0% (LVD cutoff) |
```

**Required Content:**

1. Replace voltage table with Na-ion 8S range:
```
| Voltage | Approx. SOC | System State |
|---------|-------------|---------------|
| 32.0V | 100% | Full charge |
| 28.0V | 75% | Nominal |
| 24.0V | 50% | Nominal (name voltage) |
| 20.0V | 25% | Load disconnect closes |
| 18.0V | 10% | Software LVD / load disconnect opens |
| 16.0V | 5% | Emergency mode only |
| 12.0V | 0% | Deep discharge floor |
| 0.0V | Dormant | Decade storage (safe) |
```

2. Add new section: **§X Lazarus Wake Procedure (Decade Dormancy Recovery)**
   - When to use: System stored at 0V for extended period
   - Prerequisites: 150W solar array, clear sky, 4-8 hours time
   - Step-by-step procedure referencing analog voltmeter readings
   - Expected timeline (passive bootstrap → MPPT takeover → boot)
   - Troubleshooting for failed wake

3. Add new section: **§X.X Reading the Analog Voltmeter**
   - Meter location and scale interpretation
   - Voltage-to-state reference (0V dormant → 32V full)
   - When digital displays are not available (decade storage wake)

4. Update §3.3 Charging Times for:
   - Normal recharge: 20%→100% in ~6 hours (150W array)
   - Lazarus wake: 0V→bootable in 4-8 hours

**Cross-Reference:** 01-hardware-specification.md §4.16, 02-assembly-specification.md §8.9

**Resolution:**
Operations Manual v4.4 comprehensive update:
- §3.1: Replaced LiFePO4 voltage table with Na-ion 8S range (12V-32V) including system state column
- §3.1.1: New section "Reading the Analog Voltmeter" with scale interpretation table and usage guidance
- §3.3: Updated charging times table for 150W array with normal and Lazarus scenarios
- §3.4: Updated scavenger voltage range (12-30V → 18-32V)
- §8.1: Expanded power scavenging section with 12V limitations and 24V preferred source
- §9: New full section "Lazarus Wake Procedure" with 6 subsections (When to Use, Prerequisites, Procedure, Timeline, Troubleshooting, Post-Wake Checklist)

---

#### LOW-OPS-002: §7 Long-Term Storage Still References LiFePO4 Maintenance

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Sections:** §7.1 Preparing for Storage, §7.2 Storage Duration Guidelines

**Current Content:**
- §7.1 step 1: "Charge battery to 50-60%" (LiFePO4 best practice, unnecessary for Na-ion)
- §7.2: "Mandatory recharge" entries at 24-36 months (Na-ion tolerates 0V indefinitely)

**Required Content:**
- Update §7.1 to note that 50-60% charge is optional for Na-ion (0V storage is safe)
- Update §7.2 to simplify guidelines: recharge not mandatory, just recommended for faster wake
- Add note that Lazarus wake (§9) handles any storage duration

**Impact:** Low. Current text is overly cautious but not incorrect—following LiFePO4 procedures won't harm Na-ion cells.

**Cross-Reference:** appendix-c-operations-manual.md §9 Lazarus Wake Procedure

**Resolution:**
Operations Manual §7 updated:
- §7.1: Storage charge now noted as optional for Na-ion
- §7.2: 36+ months row updated to reference Lazarus wake instead of mandatory recharge

---

## Resolution Tracking

### Next Steps (Priority Order)

1. **CRITICAL-ASM-003** and **CRITICAL-ASM-004**: Complete rewrites of battery and MPPT mounting sections (prerequisite for assembly)
2. **CRITICAL-ASM-005** and **CRITICAL-ASM-006**: Power diagram and schematic rewrites (defines wiring for all other work)
3. **HIGH-ASM-006** through **HIGH-ASM-010**: New procedure sections for Lazarus-Phoenix components
4. **Remaining HIGH issues**: Voltage threshold corrections
5. **MEDIUM and LOW issues**: After critical path resolved

### Estimated Remediation Time

| Category | Issue Count | Est. Hours | Notes |
|----------|-------------|------------|-------|
| CRITICAL | 6 | 8-12 | Major section rewrites |
| HIGH | 10 | 4-6 | New procedures + corrections |
| MEDIUM | 3 | 1-2 | Clarifications |
| LOW | 1 | 0.5 | Editorial |
| **Total** | **20** | **13.5-20.5** | |

---

## Version History

| Version | Date | Summary |
|---------|------|---------|
| 2.0.0 | 2025-12-29 | **ALL ISSUES RESOLVED.** LOW-STG-009/010/011 resolved: appendix files found in /appendices/ directory. appendix-b-glossary.md v5.0.0 with 8 new Vault Edition terms. appendix-a-threat-model.md v5.0 with SLC failure modes. appendix-c-operations-manual.md v5.0 with §10 Archival Seal Procedure. |
| 1.9.0 | 2025-12-29 | Batch 4 (partial): LOW-STG-012 resolved. 00-project-overview.md v4.1.0 with Vault Edition updates. 3 issues remain (LOW-STG-009/010/011 deferred: appendix files A/B/C do not exist). |
| 1.8.0 | 2025-12-29 | Batch 3 complete: HIGH-STG-003/004 resolved. 03-software-requirements.md v2.8.0 with archival FRs and user stories. 04-technical-design.md v3.1.0 with archival subsystem implementation. 4 issues remain (all LOW). |
| 1.7.0 | 2025-12-29 | Batch 2 complete: MED-STG-005 resolved. 02-assembly-specification.md v4.0.0 with conformal coating procedure, SLC storage prep, Faraday bag storage. 6 issues remain. |
| 1.6.0 | 2025-12-29 | Batch 1 complete: HIGH-STG-001/002, MED-STG-006/007/008 resolved. 01-hardware-specification.md v4.0.0 with SLC storage, Li-SOCl2 RTC, revised BOM ($4,776). 7 issues remain. |
| 1.5.0 | 2025-12-29 | VAULT EDITION: 12 new issues added for storage architecture revision. SLC NAND, Li-SOCl2 RTC, ark-seal archival mode, discovery boot. Philosophy shift: Active stewardship → Passive survivability (50-year seed vault). BOM +$1,903. |
| 1.4.0 | 2025-12-29 | BATCH COMPLETE: MED-THR-001, LOW-OVW-001, LOW-OPS-002 resolved. All 27 issues closed. BUILD READY. |
| 1.3.0 | 2025-12-29 | HIGH-OPS-001 resolved: Operations Manual v4.4 with Na-ion voltage table, Lazarus wake procedure. Added LOW-OPS-002 for §7 storage cleanup. 3 open, 24 resolved. |
| 1.2.0 | 2025-12-29 | HIGH-GLO-001 resolved: Glossary v4.3.0 with Lazarus-Phoenix terminology. 3 open, 23 resolved. |
| 1.1.0 | 2025-12-29 | Pre-build deep audit. 4 new issues: LOW-OVW-001 (version mismatch), MED-THR-001 (threat model LiFePO4), HIGH-GLO-001 (glossary terminology), HIGH-OPS-001 (ops manual voltage chart). Total: 26 issues (4 open, 22 resolved). |
| 1.0.0 | 2025-12-28 | Initial issue tracking. 20 issues identified from Lazarus-Phoenix integration audit of 02-assembly-specification.md. |

---

## VAULT EDITION: Storage Architecture Revision

**Audit Context:** Deep research review (2025-12-29) identified critical gap: current architecture assumes active human maintenance (quarterly scrubs, PAR2 repair). For true 50-year "seed vault" survivability, passive survivability through SLC NAND physics is required.

**Philosophy Shift:** Active stewardship → Passive survivability  
**Codename:** Vault Edition  
**BOM Impact:** +$1,903 ($2,712 → $4,615)  

**Key Decisions:**
- SLC NAND for 50+ year retention (Swissbit X-76m NVMe, S-56 SD)
- Li-SOCl2 RTC battery (Tadiran TL-5903) for 40-year timekeeping
- Conformal coating (Parylene C) for humidity/corrosion protection
- Write-once archival mode (`ark-seal`) with integrity manifests
- Discovery boot sequence for unknown future handler

---

### HIGH Issues (Vault Edition)

#### HIGH-STG-001: Replace Transcend NVMe with Swissbit X-76m SLC

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** 01-hardware-specification.md, 02-assembly-specification.md

**Current Content (INSUFFICIENT FOR SEED VAULT):**
- §3 Storage: Transcend MTE652T-I 512GB (TLC NAND, ~2 year retention)
- Cold spare: Same TLC drive

**Required Content:**
- Primary: Swissbit X-76m 480GB SLC NVMe (SFPC480GV3AA4TO-I-OC-A26-STD)
- Cold spare: Pre-loaded duplicate in Faraday bag
- New section §3.X: Storage Longevity Rationale (SLC physics, Arrhenius modeling)
- New section §3.X: Swissbit X-76m Specifications table

**Swissbit X-76m Specifications:**
| Parameter | Value |
|-----------|-------|
| Capacity | 480GB |
| Interface | PCIe Gen3 x4, NVMe 1.3 |
| NAND Type | True SLC |
| Temperature | -40°C to +85°C |
| Endurance | 60,000 P/E cycles |
| Power-Loss Protection | Yes |
| Data Retention | 10+ years specified, 50+ years extrapolated |
| Price | ~$800 each |

**Cross-Reference:** 04-technical-design.md (archival subsystem)

---

#### HIGH-STG-002: Replace Kingston SD with Swissbit S-56 SLC

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** 01-hardware-specification.md, 02-assembly-specification.md

**Current Content:**
- §3 Storage: Kingston Industrial 32GB microSD (TLC NAND)

**Required Content:**
- Primary: Swissbit S-56 128GB SLC SD (SFSD128GN3AA4TO-I-OG-A21-STD)
- Cold spare: Pre-loaded duplicate in Faraday bag
- New section §3.X: Swissbit S-56 Specifications table
- New partition: /archive (read-only archival corpus subset)

**Swissbit S-56 Specifications:**
| Parameter | Value |
|-----------|-------|
| Capacity | 128GB |
| Interface | SD UHS-I |
| NAND Type | True SLC |
| Temperature | -40°C to +85°C |
| Data Retention | 10+ years specified, 50+ years extrapolated |
| Price | ~$200 each |

---

#### HIGH-STG-003: Implement ark-seal Archival Mode

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** 03-software-requirements.md, 04-technical-design.md

**Missing Content:**

**03-software-requirements.md:**
- New FR: Archival Seal Mode (write-once, /data read-only, SHA-256 manifests)
- Revise FR: User Data Import (disabled in archival mode)
- New power state: ARCHIVAL_SEALED

**04-technical-design.md:**
- New section §X: Archival Subsystem
- §X.1 ark-seal Implementation (sync, manifest generation, remount, flag)
- §X.2 Discovery Boot Sequence (TIME_LOST detection, integrity check)
- §X.3 Integrity Status Definitions (INTACT/DEGRADED/CRITICAL)

**ark-seal command flow:**
```
ark-seal --mode=archival
├── Verify all data synced (sync; sync)
├── Generate /data manifest: find /data -type f -exec sha256sum {} \;
├── Generate /archive manifest (SD)
├── Write manifests to /recovery/manifests/
├── Remount /data read-only
├── Set seal flag: touch /recovery/.sealed
├── Update displays with "SEALED" status
```

---

#### HIGH-STG-004: Implement Discovery Boot Sequence

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** 03-software-requirements.md, 04-technical-design.md

**Missing Content:**

**New FR: Discovery Boot Sequence**
- On boot after extended dormancy (TIME_LOST condition):
  - Run automatic integrity check against stored manifests
  - Attempt recovery from SD backup if NVMe errors detected
  - Display integrity status prominently on E-ink
  - Provide clear "what is this device" explanation for discoverer

**Discovery boot flow:**
```
On boot (systemd ark-integrity.service):
├── Check TIME_LOST condition (year < 2025)
├── If TIME_LOST:
│   ├── Display "KNOWLEDGE ARK RECOVERED" on E-ink
│   └── Run full integrity verification
├── Load manifest from /recovery/manifests/
├── Verify random sample (1% of files) for quick check
├── Display status: INTACT / DEGRADED / CRITICAL
└── If DEGRADED: Offer recovery from SD backup
```

---

### MEDIUM Issues (Vault Edition)

#### MED-STG-005: Add Conformal Coating Procedure

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** 02-assembly-specification.md

**Missing Content:**

New section §5.X Conformal Coating Application:
- Purpose: Humidity/corrosion protection for decade storage
- Material: Parylene C (MG Chemicals 422B or equivalent, ~$30)
- Components to coat: OPi5+ board, DC-DC converter, protection chain PCB
- Masking: Connectors, thermal interface, buttons, LEDs
- Procedure: IPA clean → mask → apply → 24hr cure → UV inspect → unmask
- Do NOT coat: M.2 slot, SD slot, USB ports, heatsink

Add to §2.1 Required Tools: Conformal coating applicator, UV light (optional)

---

#### MED-STG-006: Upgrade RTC to Li-SOCl2 Battery

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** 01-hardware-specification.md, 02-assembly-specification.md

**Current Content:**
- §7 Timekeeping: CR2032 (3V lithium coin cell)
- ~10 year shelf life

**Required Content:**
- Primary: Tadiran TL-5903 Li-SOCl2 (3.6V, 2.4Ah, 20-40 year shelf life)
- Backup: CR2032 × 2 (spares in lid foam for field replacement)
- Note: TL-5903 has different form factor—may require holder modification

**Update §3.1.X RTC Battery Installation in assembly spec**

---

#### MED-STG-007: Update Spare Parts Inventory for SLC

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** 01-hardware-specification.md, 02-assembly-specification.md

**Current §9 Spare Parts:**
```
| Item | Qty | Location |
|------|-----|----------|
| NVMe (cold spare) | 1 | Lid foam, anti-static bag |
| CR2032 | 3 | Lid foam |
| microSD 32GB | 1 | Lid foam |
```

**Required §9 Spare Parts:**
```
| Item | Qty | Location | Notes |
|------|-----|----------|-------|
| Swissbit X-76m 480GB SLC NVMe | 1 | Lid foam, Faraday bag | Pre-loaded with corpus |
| Swissbit S-56 128GB SLC SD | 1 | Lid foam, Faraday bag | Pre-loaded with corpus |
| CR2032 | 2 | Lid foam | Field-replaceable RTC backup |
| Tadiran TL-5903 | 1 | Lid foam | 40-year RTC primary spare |
```

---

#### MED-STG-008: Revise Storage Budget Calculations

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** 01-hardware-specification.md, 05-library-manifest.md

**Current §3.4 Partition Layout:**
```
/dev/nvme0n1 (512GB NVMe → ~470 GiB)
└── /data (404 GB)
```

**Required §3.4 Partition Layout:**
```
/dev/nvme0n1 (480GB SLC NVMe → ~447 GiB)
└── /data (381 GB)  ← Reduced from 404GB

/dev/mmcblk1 (128GB SLC SD → ~119 GiB)
└── /archive (119 GB) - Read-only archival corpus subset
```

**Storage Budget Impact:**
| Partition | Current | Revised | Delta |
|-----------|---------|---------|-------|
| /data | 404 GB | 381 GB | -23 GB |
| Library allocation | 230 GB | 230 GB | unchanged |
| Headroom | 174 GB | 151 GB | -23 GB |

Note: 381GB still exceeds library requirement (167GB current, 230GB budget).

---

### LOW Issues (Vault Edition)

#### LOW-STG-009: Add SLC/Archival Terminology to Glossary

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** appendix-b-glossary.md

**New Terms Required:**
| Term | Definition |
|------|------------|
| SLC NAND | Single-Level Cell NAND flash storing 1 bit per cell. Provides highest retention and endurance due to wide voltage margins. |
| Archival Seal | Write-once mode that remounts /data read-only and generates integrity manifests. Transforms device from active use to passive seed vault. |
| Discovery Boot | Boot sequence optimized for unknown future discoverer. Runs integrity check, displays status, provides orientation. |
| Integrity Manifest | SHA-256 checksums of all corpus files, generated at seal time, used for bitrot detection on boot. |
| Li-SOCl2 | Lithium Thionyl Chloride battery chemistry. 20-40 year shelf life, used for RTC in seed vault applications. |
| Passive Survivability | Design philosophy prioritizing device function after decades of unattended storage without maintenance. |

**Revised Terms:**
| Term | Revision |
|------|----------|
| Cold Spare | Update: "Pre-loaded SLC NVMe/SD with corpus, stored in Faraday bag" |

---

#### LOW-STG-010: Update Threat Model for SLC Architecture

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** appendix-a-threat-model.md

**Updates Required:**

**§1.5 Component Failure:**
| Current | Revised |
|---------|--------|
| Flash wear (NVMe): Industrial NVMe with power-loss protection | Flash retention (NVMe): True SLC NAND (Swissbit X-76m) with 50+ year extrapolated retention |

**§4 Failure Modes (Add):**
| Failure Mode | Cause | Detection | Mitigation |
|--------------|-------|-----------|------------|
| NVMe bitrot | Electron leakage over decades | Boot integrity check | SLC NAND provides 50+ year margin; SD backup |
| SD bitrot | Electron leakage | Boot integrity check | SLC NAND provides margin; NVMe is primary |
| Both storage fail | Dual independent failure | Boot fails or CRITICAL | Spare NVMe/SD in lid foam |
| RTC battery dead | 40+ year depletion | TIME_LOST on boot | Li-SOCl2 extends to 40yr; CR2032 spares |

**§5 Residual Risks (Add):**
| Risk | Reason | Acceptance |
|------|--------|------------|
| SLC retention < 50 years | Physics extrapolation, not tested | Dual-device redundancy; spares available |
| Discoverer damages device | Unknown future handler | Laminated instructions; spare parts in lid |
| Storage interface obsolete | M.2 NVMe may not exist in 2075 | Data is standard filesystem; adapter possible |

---

#### LOW-STG-011: Add Archival Procedures to Ops Manual

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** appendix-c-operations-manual.md

**New Section Required: §X Archival Seal Procedure**

**When to Seal:**
- After corpus loading complete
- Before long-term (1+ year) storage
- When preparing device as seed vault

**Procedure:**
1. Verify corpus integrity (run manual checksum verification)
2. Run `ark-seal --mode=archival`
3. Verify seal confirmation on E-ink
4. Prepare spare drives (clone and seal in Faraday bags)
5. Install conformal coating if not already done
6. Perform storage preparation (§7)
7. Seal case

**Unsealing:**
- Run `ark-unseal --confirm`
- WARNING: Breaks archival warranty; use only if device must be updated
- After unseal, re-seal with `ark-seal` when updates complete

**Update §7 Long-Term Storage:**
```
For Seed Vault Storage (1-50 years):
1. Ensure device is in ARCHIVAL_SEALED mode
2. Charge battery to 50-80% (optional—Na-ion tolerates 0V)
3. Verify RTC battery is Tadiran Li-SOCl2 (not CR2032)
4. Verify spare NVMe and SD in lid foam, Faraday bags, with desiccant
5. Verify conformal coating applied
6. Store in cool, dark, dry location (15-25°C ideal)
7. No maintenance required—device designed for passive survival
```

**Update §9 Lazarus Wake Procedure:**
Add subsection §9.X Discovery Scenario for unknown future handler.

---

#### LOW-STG-012: Revise 00-project-overview Assumptions

**Status:** RESOLVED  
**Resolved:** 2025-12-29  
**Discovered:** 2025-12-29  
**Affected Documents:** 00-project-overview.md

**Updates Required:**

**§3 Design Philosophy (Add):**
- New principle: "Passive Survivability" — device must function after 50 years sealed storage without maintenance
- Revise trade-off hierarchy: Storage subsystem exempt from cost ceiling for survivability

**§4 This Build: V1 Shield (Update):**
- Storage: Transcend MTE652T-I → Swissbit X-76m SLC
- BOM total: $2,712 → ~$4,615

**§6 Key Technical Decisions (Add):**
- New §6.X "Storage Longevity Strategy" explaining SLC selection
- Revise §6.3 to remove "user imports" as primary use case
- Add "write-once archival" framing

**§7 Future Variants (Update):**
- "Time Capsule" variant description — note that current build now incorporates seed vault storage

---

## BOM Impact Summary (Vault Edition)

### Storage Section Delta

| Component | Current | Revised | Delta |
|-----------|---------|---------|-------|
| Transcend MTE652T-I 512GB NVMe × 2 | $360 | $0 | -$360 |
| Swissbit X-76m 480GB SLC NVMe × 2 | $0 | $1,600 | +$1,600 |
| Kingston Industrial 32GB SD | $30 | $0 | -$30 |
| Swissbit S-56 128GB SLC SD × 2 | $0 | $400 | +$400 |
| CR2032 RTC battery | $3 | $3 (spares) | $0 |
| Tadiran TL-5903 Li-SOCl2 | $0 | $15 | +$15 |
| Conformal coating (Parylene C) | $0 | $30 | +$30 |

### BOM Totals

| Category | Current | Revised | Delta |
|----------|---------|---------|-------|
| Storage | $390 | $2,000 | +$1,610 |
| Timekeeping | $3 | $18 | +$15 |
| Protection | $0 | $30 | +$30 |
| **Subtotal Delta** | | | **+$1,655** |
| **Base BOM** | $2,358 | $4,013 | +$1,655 |
| Contingency (15%) | $354 | $602 | +$248 |
| **Total BOM** | **$2,712** | **$4,615** | **+$1,903** |

---

## Procurement Notes (Vault Edition)

### Primary Vendors

| Component | Manufacturer | Distributor | Part Number |
|-----------|--------------|-------------|-------------|
| Swissbit X-76m 480GB | Swissbit | Mouser, DigiKey | SFPC480GV3AA4TO-I-OC-A26-STD |
| Swissbit S-56 128GB | Swissbit | Mouser, DigiKey | SFSD128GN3AA4TO-I-OG-A21-STD |
| Tadiran TL-5903 | Tadiran | Mouser, DigiKey | TL-5903/S |
| Conformal coating | MG Chemicals | Amazon, DigiKey | 422B-55ML |

### Alternative Sources (Supply Chain Resilience)

| Component | Alternative | Notes |
|-----------|-------------|-------|
| Swissbit X-76m | ATP N700 Industrial SLC, Innodisk 3IE7 | Verify true SLC (not iSLC) |
| Swissbit S-56 | ATP Industrial SLC SD, Delkin Industrial | Verify true SLC |
| Tadiran TL-5903 | Saft LS14500 | Same chemistry, different form factor |

---

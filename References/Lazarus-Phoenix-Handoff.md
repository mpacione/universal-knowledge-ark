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

This document captures the complete technical decision process for solving the Universal Knowledge Ark's **decade-dormancy power system problem**. The original LiFePO4 battery architecture was discovered to have a fatal flaw that would brick the device after years of storage. Two research protocols (Lazarus and Phoenix) identified and validated a replacement architecture using sodium-ion batteries and boost-topology solar charging.

**Final Architecture Decision:**
- 8S4P Na-ion battery pack (Polyanion chemistry, 40Ah/960Wh)
- Genasun GVB-8-WP boost MPPT controller (accepts 8A current limit)
- 3× 50W 12V panels in parallel (150W total)
- Analog moving-coil voltmeter for status feedback
- Passive bootstrap circuit for unconditional 0V recovery
- Latching relay load disconnect for boot-loop prevention

**Key Outcome:** System can wake from true 0V after 10+ years dormancy, reach Emergency Mode in ~4-6 hours of sunlight, with zero silicon in the critical feedback path.

---

## Part 1: The Problem — Why LiFePO4 Fails at Decade Dormancy

### Original Specification

The Universal Knowledge Ark was designed with:
- 2× Bioenno BLF-1250A LiFePO4 batteries (50Ah each, 100Ah total, 1280Wh)
- Victron SmartSolar 75/15 MPPT controller
- 24V 50W solar panel

### The Fatal Flaw: BMS Quiescent Drain

Every LiFePO4 battery contains a Battery Management System (BMS) that monitors cell voltages and manages balancing. Even when the battery is "off," the BMS draws 1-5mA continuously.

**The math:**
- 1mA × 24 hours × 365 days × 10 years = 87.6 Ah drained
- 100Ah battery → completely depleted in ~10 years from BMS alone
- Actual failure occurs much sooner due to deep discharge damage

### Deep Discharge Electrochemistry

When LiFePO4 cells drop below ~2.5V, irreversible damage occurs:

1. **Copper dissolution:** The copper anode current collector oxidizes (Cu → Cu²⁺ + 2e⁻)
2. **Ion migration:** Dissolved copper ions migrate through electrolyte
3. **Dendrite formation:** On recharge, copper redeposits as metallic dendrites
4. **Internal short circuit:** Dendrites eventually pierce separator
5. **Thermal runaway:** Short circuit causes rapid heating, potential fire

**Result:** A LiFePO4 battery stored at 50% SoC will eventually self-discharge to dangerous levels. Attempting to recharge it risks thermal runaway. The Ark becomes a brick — or worse, a fire hazard.

### Why This Wasn't Caught Earlier

The 10-year dormancy requirement is extreme. Most solar battery systems assume:
- Regular use (daily cycling)
- Grid backup (can be recharged anytime)
- Human monitoring (user notices low voltage)

The Ark's mission profile — deploy once, forget for a decade, wake in emergency — is outside normal design parameters.

---

## Part 2: Lazarus Protocol — Sodium-Ion as the Solution

### Research Scope

The Lazarus Protocol was a deep-dive electrochemistry analysis to find a battery chemistry that can survive true 0V storage for 10+ years without degradation.

### Why Sodium-Ion Works at 0V

**The key difference: Aluminum current collectors on both electrodes.**

| Chemistry | Anode Collector | Cathode Collector | 0V Behavior |
|-----------|-----------------|-------------------|-------------|
| Li-ion/LiFePO4 | Copper | Aluminum | Copper dissolves below 2.5V |
| Na-ion | Aluminum | Aluminum | Aluminum stable at all potentials |

**At 0V, a Na-ion cell is electrochemically inert:**
- No voltage potential to drive parasitic reactions
- No copper to dissolve
- Cell acts as a passive high-impedance capacitor
- SEI layer may partially dissolve but regenerates on first recharge (<5% one-time capacity loss)

### Empirical Validation

**Hithium N162Ah test results:**
- 6 months at 0V storage
- Zero measurable capacity loss
- Normal cycling performance after recharge
- No thermal events, no swelling

**Faradion (acquired by Reliance) patents:**
- Explicitly claim 0V storage/shipping capability
- Commercial Na-ion cells shipped at 0V for safety

### Cathode Chemistry Selection

Not all Na-ion cathodes are equal for decade storage:

| Cathode Type | Capacity | 0V Stability | Moisture Sensitivity | Recommendation |
|--------------|----------|--------------|---------------------|----------------|
| Layered Oxide (NaNiMnO) | High | Poor | HIGH | ❌ Avoid |
| Prussian Blue Analogue | Medium | Medium | MEDIUM (zeolitic H₂O) | ❌ Avoid |
| **Polyanion (NVPF/NFPP)** | Medium | Excellent | LOW | ✅ **SELECTED** |

**Polyanion advantages:**
- Robust 3D phosphate crystal framework
- Minimal structural change during cycling
- Low moisture sensitivity (important for sealed enclosure)
- Hithium uses NFPP (sodium iron pyrophosphate) for utility storage — proven at scale

### Cell Selection

**HiNa 32140 cylindrical cells:**
- Chemistry: Polyanion (NFPP confirmed)
- Capacity: 10Ah per cell
- Voltage: 1.5V (empty) → 4.0V (full)
- Form factor: 32mm × 140mm cylindrical
- Cost: ~$7/cell
- Availability: AliExpress, direct from HiNa

**Pack configuration: 8S4P**
- 8 cells in series × 4 parallel strings = 32 cells
- Voltage range: 12V (empty) → 32V (full)
- Nominal: 24V
- Capacity: 40Ah (960Wh)
- Cost: 32 × $7 = ~$224

### Storage Protocol

For decade dormancy, the Na-ion pack is stored at **0V with terminals shorted**.

**Procedure:**
1. Discharge pack to 12V (0% SoC) via resistive load
2. Continue discharge to 0V (takes several days with low-current load)
3. Short positive and negative terminals with copper bus bar
4. Verify 0V with multimeter
5. Store indefinitely

**The short prevents any cell from drifting to reverse polarity** (which would damage that cell). All cells equalize at 0V.

---

## Part 3: Phoenix Protocol — MPPT Controller Analysis

### Research Scope

The Phoenix Protocol was a forensic analysis of solar charge controller architectures to find one that can wake a 0V battery without user intervention.

### The 0V Wake Paradox

**Standard MPPT controllers use buck topology (step-down):**
- Panel voltage (e.g., 44V) stepped down to battery voltage (e.g., 24V)
- Controller logic powered from battery side
- At 0V battery → controller brain is dead → can't start charging
- Chicken-and-egg problem

**Specific failures identified:**

| Controller | Architecture | 0V Behavior | Verdict |
|------------|--------------|-------------|---------|
| Victron SmartSolar | Buck | "Battery Not Connected" lockout | ❌ |
| EPEver Tracer | Buck | Dead at 0V | ❌ |
| Renogy Rover | Buck | Dead at 0V | ❌ |
| SOLAFANS (cheap Amazon) | Buck + fake MPPT | Dead, plus auto-voltage misdetection | ❌❌ |

### Boost Topology: The Solution

**Boost controllers step UP voltage (panel → battery):**
- Designed for low-voltage panels charging high-voltage batteries
- Controller logic can run from PV input (lower voltage, easier to regulate)
- Passive current path exists: PV+ → Inductor → Diode → Battery+
- Current flows whenever PV > battery, no active switching required

**At 0V battery with 18V panel:**
- Controller logic powers from 18V PV input
- Passive diode conducts immediately
- Battery voltage rises
- Once battery > few volts, active MPPT engages

### Genasun GVB-8 Discovery

**Key specifications:**
- Input: 5V-60V (works with low-voltage 12V panels)
- Output: Boost to 24V/36V/48V batteries (custom voltages available)
- Efficiency: 99% peak, >95% across range
- **Trickle Charge to Recover Dead (0V) Battery: YES** (datasheet explicit)
- Capacitors: Ceramic/tantalum (no electrolytics to dry out)
- Enclosure: IP68 potted (WP version), marine-grade
- Self-consumption: 0.2W (negligible)
- Track record: Round-the-world sailing races, Antarctic stations, solar aircraft

**Why Genasun wins:**
1. Boost topology naturally supports 0V wake
2. PV-powered logic removes battery dependency
3. No electrolytic capacitors (decade-stable)
4. Proven in extreme unattended deployments
5. Custom voltage profiles available for Na-ion

### Voltage Architecture Change

Original spec used 24V panels → 24V battery (buck territory).
Boost requires PV voltage < battery voltage.

**New architecture:**
- 12V nominal panels (Vmp ~18V, Voc ~22V)
- 24V nominal battery (12V-32V range)
- 18V panel boosts to 24-32V battery

This naturally supports the Genasun's operating mode.

### Current Limiting Behavior

**GVB-8 is rated for 8A output current.**

With 150W panel input at low battery voltage:
- 150W × 95% efficiency = 142W output
- At 12V battery: 142W / 12V = 11.8A → exceeds 8A limit
- Controller current-limits, operates at 8A × 12V = 96W
- ~46W of panel capacity wasted during 0V→18V phase

**Impact:** Adds ~2 hours to Lazarus wake sequence. Acceptable trade-off.

**Why not upgrade to higher-current controller?**
- Genasun doesn't make a GVB-12 (8A is their only boost model)
- ECO-WORTHY 12A boost exists but has reliability concerns (cheap caps, auto-voltage misdetection, fake MPPT reports)
- The 8A limit only affects first few hours; once battery reaches 18V, full power utilized

---

## Part 4: Final Architecture

### Block Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              SOLAR ARRAY                                    │
│                         3× 50W 12V Panels                                   │
│                    Parallel: Vmp=18V, Imp=8.4A                              │
│                           (150W total)                                      │
└─────────────────────────────────┬───────────────────────────────────────────┘
                                  │
          ┌───────────────────────┼───────────────────────┐
          │                       │                       │
    ┌─────┴─────┐          ┌──────┴──────┐         ┌──────┴──────┐
    │  Passive  │          │   Genasun   │         │   Analog    │
    │ Bootstrap │          │  GVB-8-WP   │         │  Voltmeter  │
    │ Diode+47Ω │          │ Boost MPPT  │         │   0-30V     │
    └─────┬─────┘          └──────┬──────┘         └──────┬──────┘
          │                       │                       │
          │                       │                       │
          └───────────────────────┴───────────────────────┘
                                  │
                      ┌───────────┴───────────┐
                      │    8S4P Na-ion Pack   │
                      │   40Ah / 960Wh        │
                      │   12V-32V range       │
                      │   + JK BMS (8S)       │
                      └───────────────────────┘
                                  │
                      ┌───────────┴───────────┐
                      │   Load Disconnect     │
                      │  Latching Relay +     │
                      │  TL431 Supervisor     │
                      │  (closes at >20V)     │
                      └───────────────────────┘
                                  │
                      ┌───────────┴───────────┐
                      │   Wide-Input DC-DC    │
                      │   12-32V → 5V @ 5A    │
                      └───────────────────────┘
                                  │
                      ┌───────────┴───────────┐
                      │     OPi5+ 32GB        │
                      │   + 512GB NVMe        │
                      │   + Peripherals       │
                      └───────────────────────┘
```

### Subsystem Details

#### 1. Passive Bootstrap Circuit

**Purpose:** Unconditional 0V recovery via Ohm's Law. If Genasun fails, this still works.

**Components:**
- 1N5408 silicon rectifier diode (1000V/3A, standard P-N junction)
- 47Ω 25W vitreous enamel wirewound resistor
- 2A slow-blow fuse

**NOT Schottky diode** — Schottky has high reverse leakage current (mA) that increases with temperature, could slowly drain battery during storage or fail thermally.

**Operation:**
- PV+ → Diode → Resistor → Battery+
- At 0V battery with 22V Voc panel: I = 22V / 47Ω = 0.47A
- Trickle charges at C/85 rate (very safe for Na-ion)
- Power dissipation: 22V × 0.47A = 10.3W (resistor handles 25W)
- Once battery reaches ~5V, Genasun takes over with active MPPT

#### 2. Genasun GVB-8-WP-Li-CV (Custom Voltage)

**Model:** GVB-8-Li-CV (Lithium Custom Voltage, Waterproof)

**Required custom programming:**
- Absorption voltage: 32.0V (4.0V/cell × 8S)
- Float voltage: 26.4V (3.3V/cell × 8S) — or disable float for Na-ion
- Chemistry: Closest to LiCoO₂/LiPo profile (4.0V/cell)

**Contact:** Sunforge LLC (Genasun distributor)
- Email: info@sunforgellc.com
- Request: "Custom 8S Na-ion profile, 32.0V absorption, 12-26V input range"
- Note: Minimum quantity may apply for waterproof version

**Price estimate:** $180-220 (custom adds ~$30 premium)

#### 3. Analog Voltmeter

**Purpose:** User feedback without any silicon in the path. Zero decade-degradation risk.

**Component:** Moving-coil panel meter, 0-30V DC

**Operation:**
- Mechanical needle deflection proportional to voltage
- User reads voltage, mentally maps to state:
  - <16V: Still charging, wait
  - 16-20V: Emergency Mode possible (try boot)
  - 20-24V: Full boot available
  - 24-32V: Comfortable operation
  - 32V: Fully charged

**Decade survivability:** Moving-coil meters use:
- Permanent magnets (stable indefinitely)
- Wire coil (no degradation when unpowered)
- Jeweled bearings (can seize if moisture intrudes — seal in enclosure)

**Cost:** ~$8-15

**Why not OLED/MCU?**
- MCU flash memory: 20-year retention spec, but untested at 10+ years unpowered
- Electrolytic caps in regulator: ESR drift from electrolyte diffusion
- Solder joints: Tin whisker risk on fine-pitch QFN packages
- Every component is a failure point; analog meter has none of these risks

#### 4. Load Disconnect

**Purpose:** Prevent boot-loop where computer tries to start at 5V, crashes voltage, prevents charging, infinite loop.

**Components:**
- TL431 precision shunt reference (set to trip at 20V)
- Resistor divider (sets threshold)
- Panasonic ADW1124HLW latching relay (24V coil, 10A contacts)

**Operation:**
1. Battery voltage sensed via resistor divider
2. When Vbat > 20V, TL431 triggers
3. Pulse fires relay SET coil, latches closed
4. Load now connected to battery
5. If voltage drops below 18V (hysteresis), pulse fires RESET coil, opens relay
6. Relay holds state without power (latching = bistable)

**Why latching relay?**
- Standard relay requires continuous coil power (wastes energy)
- Solid-state relay has leakage current
- Latching relay: one pulse to set, one pulse to reset, holds position indefinitely

#### 5. Battery Pack (8S4P Na-ion)

**Cells:** 32× HiNa 32140 (10Ah, Polyanion chemistry)

**Configuration:**
- 8S (series): 8 × 4.0V = 32V max, 8 × 1.5V = 12V min
- 4P (parallel): 4 × 10Ah = 40Ah capacity
- Total energy: 24V × 40Ah = 960Wh

**BMS:** JK BMS BD4A8S4P
- 8S configuration
- Active balancing (not just passive bleed)
- Bluetooth configuration via app
- Supports "Charge Wake-up" from 0V

**BMS Settings:**
- OVP (Over-Voltage Protection): 4.00V/cell (32.0V pack)
- UVP (Under-Voltage Protection): 1.50V/cell (12.0V pack)
- Balance start: 3.80V/cell
- Charge wake-up: ENABLED

**Assembly:**
- Spot-weld nickel strips (0.15mm × 8mm)
- 3D-printed cell holders for 32140 format
- Conformal coat all connections
- Terminate with XT60 connectors (gold-plated, won't oxidize)

#### 6. Solar Panels

**Configuration:** 3× 50W 12V panels in parallel

**Per-panel specs (typical Renogy 50W foldable):**
- Pmax: 50W
- Vmp: 17.6V
- Imp: 2.8A
- Voc: 21.6V
- Isc: 3.2A

**Parallel totals:**
- Pmax: 150W
- Vmp: 17.6V (unchanged)
- Imp: 8.4A (3× single panel)
- Voc: 21.6V (unchanged)
- Isc: 9.6A (3× single panel)

**Cold weather check:**
- Voc increases ~0.3%/°C below 25°C
- At -20°C: Voc = 21.6V × 1.135 = 24.5V
- Still under Genasun 26V input limit ✓

**Panel options:**
- Renogy 50W Foldable (~$60-80 each)
- BougeRV 50W Foldable (~$50-70 each)
- Or single 100-130W panel + one 50W (fewer units to deploy)

---

## Part 5: Lazarus Wake Sequence

### Timeline (150W panel, GVB-8, 40Ah pack, full sun)

| Hour | Battery V | SoC | Charge Mode | Status |
|------|-----------|-----|-------------|--------|
| 0 | 0V | 0% | — | User deploys panel, connects to Ark |
| 0-0.5 | 0→5V | — | Passive bootstrap | Diode conducts, trickle charges |
| 0.5 | 5V | — | Genasun wakes | Controller logic powers from PV |
| 0.5-2 | 5→12V | 0% | Active MPPT (8A limited) | Full boost operation |
| 2-4 | 12→16V | 5% | Active MPPT (8A limited) | Analog meter shows rising voltage |
| 4 | 16V | 5% | Active MPPT | **Emergency Mode available** |
| 4-6 | 16→20V | 20% | Active MPPT | Load disconnect closes |
| 6 | 20V | 20% | Active MPPT | **Full boot available** |
| 6-10 | 20→24V | 50% | Active MPPT (full power) | Comfortable operation |
| 10-18 | 24→32V | 100% | Taper | Full charge |

### User Experience

```
HOUR 0:   Retrieve Ark from storage
          Open enclosure, unfold 3 panels
          Connect panel cable (single weatherproof connector)
          Observe analog meter: 0V
          
HOUR 2:   Check meter: ~10V
          Too low to boot. Continue waiting.
          
HOUR 4:   Check meter: ~16V
          Try power button.
          System boots into EMERGENCY MODE (text retrieval only, no LLM)
          
HOUR 6:   Check meter: ~20V
          System auto-upgrades to FULL MODE (LLM available)
          
HOUR 10+: Normal operation
          Solar maintains charge during day
          Battery sustains overnight use
```

### Emergency Mode vs Full Mode

| Mode | Battery Threshold | Power Draw | Capabilities |
|------|-------------------|------------|--------------|
| Emergency | 16V (5% SoC) | ~3W | Text retrieval, medical guides, maps, static reference |
| Full | 20V (20% SoC) | ~10-15W | Full LLM inference, audio, all features |

Emergency Mode allows immediate value even with minimal charge. User can look up critical survival information while battery continues charging.

---

## Part 6: Bill of Materials

### Power System Components

| Item | Description | Qty | Unit Cost | Total |
|------|-------------|-----|-----------|-------|
| HiNa 32140 Na-ion cells | 10Ah, Polyanion, 32mm×140mm | 32 | $7 | $224 |
| JK BMS BD4A8S4P | 8S active balancing BMS | 1 | $45 | $45 |
| Genasun GVB-8-WP-Li-CV | Boost MPPT, custom 32V, IP68 | 1 | $200 | $200 |
| Renogy 50W 12V Foldable | Solar panel, Vmp 17.6V | 3 | $70 | $210 |
| 1N5408 diode | 1000V/3A silicon rectifier | 2 | $0.50 | $1 |
| 47Ω 25W resistor | Vitreous enamel wirewound | 1 | $5 | $5 |
| Analog voltmeter | 0-30V moving-coil panel meter | 1 | $12 | $12 |
| TL431 | Precision shunt reference | 2 | $0.50 | $1 |
| Panasonic ADW1124HLW | 24V latching relay, 10A | 1 | $8 | $8 |
| DC-DC converter | 12-32V → 5V @ 5A, wide input | 2 | $15 | $30 |
| XT60 connectors | Gold-plated, weatherproof | 4 | $2 | $8 |
| Nickel strip | 0.15mm × 8mm for spot welding | 1m | $5 | $5 |
| Cell holders | 3D printed, 32140 format | 8 | $2 | $16 |
| Fuses, wire, misc | 2A fuse, 10AWG wire, terminals | — | $20 | $20 |
| **Power System Subtotal** | | | | **$785** |

### Compute System Components (unchanged from original spec)

| Item | Description | Qty | Unit Cost | Total |
|------|-------------|-----|-----------|-------|
| Orange Pi 5 Plus 32GB | RK3588, 32GB RAM | 1 | $190 | $190 |
| 512GB NVMe SSD | Samsung 980 or similar | 1 | $50 | $50 |
| E-ink display | 7.8" 1872×1404, I2C | 1 | $80 | $80 |
| Audio amplifier | MAX98357A I2S, 3W | 1 | $5 | $5 |
| Speaker | 3W weatherproof | 1 | $8 | $8 |
| USB peripherals | Keyboard, cables | — | $30 | $30 |
| **Compute Subtotal** | | | | **$363** |

### Enclosure and Thermal (unchanged)

| Item | Description | Qty | Unit Cost | Total |
|------|-------------|-----|-----------|-------|
| Pelican 1557 Air | Waterproof case | 1 | $200 | $200 |
| Custom foam insert | Kaizen foam, laser cut | 1 | $40 | $40 |
| Cable glands | IP68, PG9 | 3 | $5 | $15 |
| Desiccant packs | Silica gel, rechargeable | 10 | $1 | $10 |
| **Enclosure Subtotal** | | | | **$265** |

### Total BOM

| Category | Cost |
|----------|------|
| Power System | $785 |
| Compute System | $363 |
| Enclosure | $265 |
| **Grand Total** | **$1,413** |

**Budget remaining:** $3,165 - $1,413 = **$1,752**

*(Note: Original full BOM included knowledge base preparation, documentation, assembly labor, and contingency. Power system changes result in net savings of ~$200 vs original LiFePO4 spec.)*

---

## Part 7: Validation Protocol

### Pre-Build Testing (Required)

| Test | Equipment | Procedure | Pass Criteria | Est. Time |
|------|-----------|-----------|---------------|-----------|
| 1. Cell verification | 4× HiNa sample cells | Request datasheet, confirm Polyanion chemistry | Datasheet states NFPP or Na₃V₂(PO₄)₂F₃ | 1 week ship |
| 2. 0V discharge | Resistive load (10Ω 50W) | Discharge cells to 0V | Cells reach 0V without swelling/venting | 1 day |
| 3. 0V storage | Copper bus bar, multimeter | Short terminals, monitor 72 hours | No thermal event, stable 0V | 3 days |
| 4. 0V recovery | Bench supply, 18V 2A | Charge from 0V to 3.0V | Voltage rises smoothly, no thermal event | 4 hours |
| 5. Capacity check | Charge/discharge cycler | Full cycle 1.5V→4.0V | Capacity >95% of rated | 8 hours |
| 6. Genasun 0V test | Genasun + bench supply | Apply 18V to PV input with 0V battery | Controller wakes, output rises | 1 hour |
| 7. Full system | All components | Simulate Lazarus wake sequence | 0V → 20V in <6 hours (scaled for test capacity) | 1 day |

**Total validation time:** ~2 weeks including shipping  
**Validation cost:** ~$100 (4 sample cells + misc components)

### Post-Build Verification

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| Enclosure seal | Submerge sealed case 1m, 30 min | No moisture intrusion |
| Thermal cycle | -20°C to +60°C, 10 cycles | All functions nominal |
| Drop test | 1m drop onto concrete (in case) | No damage, functions nominal |
| Full Lazarus simulation | Discharge to 0V, store 1 week, wake with panel | System reaches Full Mode in <8 hours |

---

## Part 8: Known Risks and Mitigations

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| HiNa cells unavailable | Low | High | Alternative: CATL or BYD Na-ion (verify Polyanion chemistry) |
| Genasun custom unavailable | Low | Medium | Alternative: Standard 48V model with series panels, or accept suboptimal float voltage |
| Cell capacity lower than rated | Medium | Low | Oversize pack slightly (8S5P = 50Ah) |
| BMS doesn't wake from 0V | Medium | Medium | Test before assembly; fallback: bypass BMS during Lazarus wake |
| Decade storage untested | High | Unknown | Extrapolated from 6-month data; include validation cells for periodic testing |
| Analog meter bearing seizes | Low | Low | Seal meter in enclosure; include spare |

---

## Part 9: Open Items / Next Steps

### Immediate Actions

1. **Contact Sunforge** — Confirm custom 32V Genasun GVB-8-WP-Li-CV availability, pricing, lead time
2. **Order sample cells** — 4× HiNa 32140 for validation testing
3. **Source analog meter** — Identify quality moving-coil 0-30V panel meter
4. **Finalize panel selection** — Confirm 3× Renogy 50W or equivalent

### Design Refinements Needed

1. **BMS bypass circuit** — Design manual override to bypass BMS FETs during Lazarus wake if BMS doesn't support 0V wake-up
2. **Cell holder CAD** — Design 3D-printable holders for 32140 cells in 8S4P configuration
3. **Wiring diagram** — Detailed schematic with wire gauges, fuse locations, connector pinouts
4. **Assembly guide** — Step-by-step instructions for pack assembly, spot welding, conformal coating

### Documentation Needed

1. **User manual update** — Lazarus wake procedure, voltage thresholds, expected timelines
2. **Quick reference card** — Laminated card with meter readings → system state mapping
3. **Troubleshooting guide** — What to do if system doesn't wake

---

## Part 10: Glossary

| Term | Definition |
|------|------------|
| **Lazarus Protocol** | Research project identifying Na-ion as solution for decade-dormancy storage |
| **Phoenix Protocol** | Research project identifying boost MPPT as solution for 0V battery wake |
| **0V Storage** | Storing battery fully discharged with terminals shorted; safe for Na-ion |
| **Boost MPPT** | Solar controller that steps UP voltage (panel < battery); contrast with buck (panel > battery) |
| **Polyanion** | Na-ion cathode chemistry using phosphate framework; most stable for long storage |
| **SEI Layer** | Solid Electrolyte Interface; protective film on anode; regenerates on Na-ion recharge |
| **BMS** | Battery Management System; monitors cells, manages balancing, protects from over/under voltage |
| **Quiescent drain** | Current drawn by electronics when "off"; the LiFePO4 killer |
| **Emergency Mode** | Low-power boot state for text retrieval when battery partially charged |
| **Load disconnect** | Relay that prevents computer from attempting boot until battery sufficient |

---

## Part 11: Reference Documents

### From This Session

- **Lazarus Protocol deep research** — Electrochemistry analysis of Na-ion 0V storage
- **Phoenix Protocol deep research** — MPPT controller architecture forensics

### External References

- Genasun GVB-8 datasheet: https://sunforgellc.com/product/gvb-8/
- HiNa Technology: https://www.hinabattery.com/
- JK BMS documentation: https://www.jkbms.com/
- Hithium Na-ion white paper (0V storage validation)
- Faradion patents on 0V shipping

---

## Document Control

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2024-12-27 | Claude + Matt Pacione | Initial handoff document |

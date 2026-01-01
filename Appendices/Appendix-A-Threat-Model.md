---
version: "5.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
tags:
  - spec
  - universal-knowledge-ark
  - security
---

# Appendix A: Threat Model & Protection Strategy

**Version:** 5.0 (Vault Edition)

## 1. Threat Categories

### 1.1 Electromagnetic Pulse (EMP)

**Source:** Nuclear detonation (E1/E2/E3) or dedicated EMP weapon.

**E1 Component:** Fast pulse (nanoseconds), damages semiconductors through induced voltage spikes. Penetrates cables acting as antennas.

**E2 Component:** Similar to lightning, lasts microseconds. Standard surge protection effective.

**E3 Component:** Slow geomagnetic disturbance (seconds to minutes). Affects long conductors (power grid, pipelines). Not a direct threat to portable electronics.

**Protection Strategy:**
- Faraday shielding when sealed (>40dB attenuation)
- Surge protection on all external ports (TVS diodes)
- No permanent external antennas or cables
- Storage posture: all cables disconnected, case sealed

### 1.2 Coronal Mass Ejection (CME)

**Source:** Solar flare ejecting plasma toward Earth.

**Effect:** Geomagnetically induced currents in long conductors. Similar to E3 component.

**Threat to This Device:** Minimal. CME primarily affects grid infrastructure, not battery-operated portables with short cables.

**Protection Strategy:** Same as EMP E1/E2.

### 1.3 Physical Damage

**Threats:**
- Drop impact
- Crushing
- Puncture
- Water intrusion

**Protection Strategy:**
- Pelican 1557 Air case: IP67, crushproof, drop-tested
- Polycarbonate storm window over fragile E-ink
- Kaizen foam absorbs shock around batteries
- Components secured with VHB + straps (not Velcro)

### 1.4 Environmental Degradation

**Threats:**
- Temperature extremes
- Humidity/moisture
- UV exposure
- Oxidation/corrosion

**Protection Strategy:**
- Industrial-temp components (-40°C to +85°C for NVMe)
- Indicating silica gel desiccant (rechargeable)
- Dielectric grease on exposed contacts
- Case in shade during operation
- Periodic storage check (annual recommended)

### 1.5 Component Failure

**Threats:**
- Flash retention loss (NVMe/SD)
- Battery degradation
- RTC battery depletion
- Connector fatigue
- Display damage

**Protection Strategy:**
- **True SLC NAND** (Swissbit X-76m NVMe, S-56 SD): 50+ year extrapolated retention at 25°C due to wide voltage margins (1 bit/cell). Contrast with TLC (3 bits/cell) which fails in 1-3 years unpowered.
- Na-ion polyanion chemistry (0V-safe aluminum collectors, decade dormancy capable)
- **Li-SOCl2 RTC battery** (Tadiran TL-5903): 20-40 year shelf life vs 3-5 years for CR2032
- **Conformal coating** (MG Chemicals 422B) protects PCBs from humidity/corrosion
- Zero hinge wiring (no flex fatigue)
- Cold spares (SLC NVMe + SD) stored in Faraday bags in lid
- Factory Reset Image backup for software recovery

### 1.6 Data Corruption

**Threats:**
- Filesystem corruption from power loss
- Bit rot over years
- Software bugs

**Protection Strategy:**
- OverlayFS (root read-only)
- SQLite WAL mode for database
- Factory Reset Image restore capability
- Lifeboat OS on separate eMMC

## 2. Protection Layers

### 2.1 Faraday Shielding

**Implementation:**
- Ripstop silver Faraday fabric lining entire case interior
- 3M 1181 conductive tape on all seams (20mm overlap minimum)
- Lid-to-base fabric overlap creates seal when latched

**Target Attenuation:** >40dB (adequate for E1/E2 components)

**Validation Test:** Place smartphone inside sealed case, call it. Should go directly to voicemail. If phone rings, locate and repair leak.

**Limitations:**
- Protection active only when sealed
- Any penetration (cable, antenna) breaks shielding
- Faraday only blocks EMP; does not stop physical damage

### 2.2 Surge Protection

**External Input Protection:**

| Port | Components | Function |
|------|------------|----------|
| XT60 (Solar) | TVS 1.5KE18CA | Clamp surge to 18V |
| Binding Posts | Schottky 15SQ045 + TVS | Reverse polarity + surge |
| USB Ports | TPD2E001 | ESD protection |

**Circuit Topology:**
```
Input → [Fuse] → [MOV] → [TVS] → [Ferrite] → Protected Bus
```

**Function:**
1. Fuse limits current
2. MOV absorbs bulk energy
3. TVS clamps residual spikes
4. Ferrite attenuates high-frequency

### 2.3 Power Chain Protection

| Component | Location | Protection |
|-----------|----------|------------|
| DPST Kill Switch | Faceplate | Isolates both solar and battery |
| 15A Breaker | Fuse block | Battery overcurrent |
| 10A Fuse | Fuse block | Solar/aux overcurrent |
| 5A Fuse | Fuse block | System logic overcurrent |
| Load Disconnect | Power chain | Boot-loop prevention (18V/20V) |
| KSD9700 | On heatsink | Thermal cutoff at 85°C |

### 2.4 Data Protection

| Layer | Mechanism |
|-------|-----------|
| Root filesystem | Read-only with OverlayFS |
| User data | Separate partition, SQLite WAL |
| Recovery | Factory Reset Image on nvme0n1p4 |
| Fallback | Lifeboat OS on eMMC |

## 3. Storage Posture

When storing for extended periods (months to years):

### 3.1 Preparation

1. Charge battery to 50-60% (optional—Na-ion tolerates 0V storage indefinitely; higher charge = faster reactivation)
2. Disconnect display cable, store in cavity
3. Verify desiccant indicator is active (orange/blue)
4. Apply dielectric grease to binding posts and XT60
5. Seal case fully (all latches engaged)
6. Store in cool, dark location (15-25°C ideal)

### 3.2 Periodic Maintenance

| Interval | Action |
|----------|--------|
| 6 months | Visual inspection (no action needed) |
| 1 year | Voltage check, regenerate desiccant if needed |
| 2 years | Top-up charge if <30%, full system test |
| 3+ years | No recharge mandatory—use Lazarus wake ([[Appendix-C-Operations-Manual#9. Lazarus Wake Procedure|§9]]) if 0V |

### 3.3 Reactivation

1. Open case, allow 30 minutes for pressure equalization
2. Check battery voltage via analog voltmeter. If 0V, use Lazarus wake ([[Appendix-C-Operations-Manual#9. Lazarus Wake Procedure|Ops Manual §9]])
3. Connect display cable
4. Power on, verify TIME LOST warning if applicable
5. Sync time via GPS or manual entry
6. Run diagnostic (System Menu → Diagnostics)

## 4. Failure Modes & Mitigations

| Failure Mode | Cause | Detection | Mitigation |
|--------------|-------|-----------|------------|
| Winter Lockout | Cold battery voltage sag | Won't boot despite good charge | Hybrid LVD (18V software / 16V hardware) |
| Solar Fry | Disconnecting battery with solar connected | MPPT destroyed | DPST kill switch |
| Screen Crunch | Impact on E-ink | Cracked display | Polycarbonate storm window |
| Ground Loop Whine | Audio noise | Audible buzz | Inline transformer isolator |
| Boot Loop | Brownout during boot | Repeated restarts | Load disconnect hysteresis |
| Time Amnesia | RTC battery dead | Year < 2025 on boot | Li-SOCl2 battery (40-year), GPS dongle, manual set |
| Thermal Saturation | Sealed case overheating | Throttling, shutdown | Fever Breaker protocol |
| Data Corruption | Power loss during write | Boot failure | OverlayFS, Factory Reset Image |
| NVMe Bitrot | Electron leakage over decades | Boot integrity check (Discovery Boot) | SLC NAND provides 50+ year margin; SD backup; cold spares |
| SD Bitrot | Electron leakage over decades | Boot integrity check | SLC NAND margin; NVMe is primary copy |
| Both Storage Fail | Dual independent failure | Boot fails or CRITICAL integrity | Cold spare NVMe/SD in lid foam Faraday bags |
| RTC Battery Dead | 40+ year depletion | TIME_LOST on boot | Li-SOCl2 extends to 40yr; device still functional |

## 5. Residual Risks

These risks cannot be fully mitigated by design:

| Risk | Reason | Acceptance |
|------|--------|------------|
| Direct blast/fire | Physical limits | Device survives EMP, not explosion |
| Extended darkness | Solar-dependent | 2-3 day autonomy; relocate or ration |
| All models obsolete | AI field advances | 8B models are frozen at build time |
| Knowledge stale | Static corpus | Ingest new documents via USB |
| User incapacitation | Human factor | Laminated quick-start on case |
| SLC retention < 50 years | Physics extrapolation, not tested | Dual-device redundancy; spares available |
| Discoverer damages device | Unknown future handler | Laminated instructions; spare parts in lid |
| Storage interface obsolete | M.2 NVMe may not exist in 2075 | Data is standard filesystem; adapter possible |

## Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0 | Dec 2025 | Initial threat model |
| 4.0 | Dec 2025 | Extracted to reference appendix |
| 4.1 | Dec 2025 | MED-THR-001: Updated for Lazarus-Phoenix—Na-ion terminology, 0V storage, Lazarus wake references, corrected voltage thresholds. |
| **5.0** | **Dec 2025** | **VAULT EDITION (LOW-STG-010): Updated §1.5 Component Failure for SLC NAND storage (Swissbit X-76m/S-56), Li-SOCl2 RTC battery, conformal coating, cold spares in Faraday bags. Added 4 new failure modes to §4.** |

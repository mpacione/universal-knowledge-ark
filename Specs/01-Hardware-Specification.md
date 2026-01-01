---
version: "4.0.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
source: "~/universal-knowledge-ark/specs/01-hardware-specification.md"
tags:
  - spec
  - universal-knowledge-ark
  - hardware
---

# Hardware Specification

**Version:** 4.0.0 (Vault Edition)

## Summary

Complete bill of materials and hardware architecture for the Universal Knowledge Ark.

## Key Components

| Component | Selection | Cost |
|-----------|-----------|------|
| Compute | Orange Pi 5+ 32GB (RK3588) | $189 |
| Storage | Swissbit X-76m 480GB SLC NVMe × 2 | $1,520 |
| Battery | Na-ion 8S4P 40Ah (960Wh) | ~$600 |
| Solar | 3× Renogy 50W + Genasun GVB-8 | ~$400 |
| Display | 10.3" E-ink + 1.3" OLED | ~$300 |
| Enclosure | Pelican 1557 Air + Faraday | ~$350 |

**Total BOM:** $4,153 base + $623 contingency = **$4,776**

## Sections

1. System Architecture (5-layer cyberdeck)
2. Compute Module (Orange Pi 5+ 32GB)
3. Storage Subsystem (SLC NAND for 50-year retention)
4. Power Management (Lazarus-Phoenix architecture)
5. Display & I/O
6. Protection Systems (Faraday, thermal cutoffs)
7. Bill of Materials

## Dependencies

- [[02-Assembly-Specification]] — Build procedures reference this BOM
- [[03-Software-Requirements]] — Power states defined here
- [[04-Technical-Design]] — Implementation uses these specs
- [[Appendix-E-NaIon-Pack-Assembly]] — Battery build guide

---

## 1. System Architecture

### 1.1 Block Diagram

```
                              SOLAR ARRAY                        SCAVENGER INPUT
                         3× 50W 12V Parallel                     (Binding Posts)
                        Vmp=18V, Imp=8.4A                        18-30V DC
                              │                                       │
                              │ XT60                                  │ Banana
                              ▼                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           POWER INPUT SECTION                               │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐ │
│  │   Passive    │   │   Genasun    │   │    Analog    │   │   15A Fuse   │ │
│  │  Bootstrap   │   │  GVB-8-WP    │   │  Voltmeter   │   │  + 1N5408    │ │
│  │ Diode + 47Ω  │   │ Boost MPPT   │   │   0-30V DC   │   │  (Backfeed)  │ │
│  └──────┬───────┘   └──────┬───────┘   └──────┬───────┘   └──────┬───────┘ │
└─────────┼──────────────────┼──────────────────┼──────────────────┼─────────┘
          │                  │                  │                  │
          └──────────────────┴──────────────────┴──────────────────┘
                                       │
                                       ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         BATTERY SYSTEM (24V Nominal)                        │
│                    8S4P Na-ion Polyanion (HiNa 32140)                       │
│                   40Ah / 960Wh (12V empty → 32V full)                       │
│                         + JK BMS BD4A8S4P                                   │
└──────────────────────────────┬──────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                    LOAD DISCONNECT (Latching Relay)                         │
│               TL431 Supervisor: Opens <18V, Closes >20V                     │
└──────────────────────────────┬──────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│              DPST KILL SWITCH (Solar + Battery)                             │
└──────────────────────────────┬──────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        PROTECTION CHAIN                                     │
│       [NTC Thermistor] → [Fuse Block] → [INA219] → [KSD9700 85°C]          │
└──────────────────────────────┬──────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                       POWER DISTRIBUTION                                    │
│              Wide-Input DC-DC: 12-32V → 5V @ 5A                             │
└────┬─────────────────┬─────────────────┬───────────────┬────────────────────┘
     │                 │                 │               │
     ▼                 ▼                 ▼               ▼
┌─────────┐     ┌─────────────┐   ┌───────────┐   ┌───────────┐
│  OPi 5+ │◄───►│   STORAGE   │   │  DISPLAY  │   │   AUDIO   │
│  32GB   │     │ NVMe + eMMC │   │ 10.3"E-ink│   │ ReSpeaker │
└────┬────┘     └─────────────┘   │ + IT8951  │   │ + TPA2016 │
     │                            └───────────┘   └───────────┘
     │
     ├──► OLED Status Display (I2C)
     ├──► DS3231 RTC (I2C)
     ├──► Industrial D-Pad + Buttons (GPIO)
     └──► USB: Keyboard, GPS Dongle
```

### 1.2 Component Hierarchy

| Subsystem | Primary Component | Backup/Redundancy |
|-----------|-------------------|-------------------|
| Compute | Orange Pi 5+ (32GB) | — |
| Storage | Swissbit X-76m 480GB SLC NVMe | eMMC (Lifeboat OS), cold spare SLC NVMe, SLC SD archive |
| Power | Na-ion 8S4P 40Ah/960Wh (0V-safe) | Solar recharge, passive bootstrap, binding posts |
| Display | Waveshare 10.3" E-ink + IT8951 | OLED status display |
| Input | USB Keyboard + Industrial D-pad | Physical buttons (ESC, Home, Power) |
| Audio | ReSpeaker Mic Array + TPA2016D2 Amp | — |
| Enclosure | Pelican 1557 Air (Orange) | Gaskets replaceable |

### 1.3 Cyberdeck Architecture (5 Layers)

```
┌─────────────────────────────────────────────────────────────────┐
│ LID: E-ink display + IT8951 + storm window + USB port           │
├─────────────────────────────────────────────────────────────────┤
│ CAVITY: Keyboard sleeve, 3× solar panels, cables, GPS, USB light│
├─────────────────────────────────────────────────────────────────┤
│ DECK FACEPLATE: D-pad, buttons, OLED, voltmeter, XT60, USB      │
├─────────────────────────────────────────────────────────────────┤
│ DECK UNDERSIDE: OPi 5+, NVMe, DC-DC, amp, RTC, protection chain │
├─────────────────────────────────────────────────────────────────┤
│ BASEMENT: Na-ion 8S4P pack, Genasun MPPT, spares in Kaizen foam │
└─────────────────────────────────────────────────────────────────┘
```

### 1.4 Signal Flow

```
--- Query Flow ---
User Input → D-pad/Keyboard → OPi 5+ → Inference (NPU) → E-ink Update
Voice Input → ReSpeaker (USB) → OPi 5+ → STT → Inference → TTS → Speaker

--- Power Flow ---
Solar → Genasun Boost MPPT → Battery (24V nom) → Load Disconnect → Kill Switch
     → Protection Chain → DC-DC (12-32V→5V) → Subsystems
Passive Bootstrap: Solar → Diode + 47Ω → Battery (0V recovery fallback)
Scavenger → Fuse + Diode → Battery (parallel input path, 18-30V)

--- Power Control Flow ---
Power Button → PMIC (hardware) → OPi 5+ wake/sleep
INA219 (I2C) → ark-power → Voltage < 18.0V → Graceful Shutdown → systemd poweroff
Load Disconnect → Opens at <18V (prevents boot-loop)
KSD9700 85°C → Hardware cutoff (thermal emergency, no software involved)
Analog Voltmeter → User reads battery state (zero silicon feedback)
```

---

## 2. Compute Module

### 2.1 Primary: Orange Pi 5 Plus (32GB)

| Parameter | Specification |
|-----------|---------------|
| Model | Orange Pi 5 Plus |
| SoC | Rockchip RK3588 (8-core: 4× Cortex-A76 @ 2.4GHz + 4× Cortex-A55 @ 1.8GHz) |
| NPU | 6 TOPS (RKLLM inference acceleration) |
| RAM | 32GB LPDDR4x |
| GPU | Mali-G610 MP4 (not used for inference) |
| Storage | M.2 NVMe PCIe 3.0 x4 + onboard 32GB eMMC |
| USB | 2× USB 3.0, 2× USB 2.0 |
| GPIO | 40-pin header (I2C, SPI, UART, GPIO) |
| Ethernet | 2× Gigabit (internal access only) |
| Audio | 3.5mm jack |
| Power Input | 5V/4A USB-C |
| Power Draw | 3-4W idle, 8-10W active inference, 12-15W peak |
| Operating Temp | 0°C to +70°C |
| Dimensions | 100 × 62 × 25mm (with heatsink) |

**System Operating Range:** 0°C to +60°C. The OPi 5+ limits the lower bound (won't boot below 0°C); the Na-ion batteries limit the upper bound (reduced performance above 60°C). Battery storage range is wider (-20°C to +60°C), so the device can survive cold storage but must warm above 0°C before boot.

### 2.2 Selection Rationale

| Requirement | OPi 5+ Capability |
|-------------|-------------------|
| Local LLM inference | 6 TOPS NPU via RKLLM, runs 7B quantized models |
| RAG vector operations | 32GB RAM holds embedding index in memory |
| Decade storage | No carrier board complexity, integrated design |
| Offline operation | No cloud dependencies, all processing local |
| Boot redundancy | Onboard eMMC for Lifeboat OS recovery |

### 2.3 Thermal Management

| Component | Specification |
|-----------|---------------|
| Primary Heatsink | Orange Pi 5 Plus Aluminum Heatsink (MPN: OP5PLUS-HEATSINK), 60×60mm base, ~1.0°C/W thermal resistance |
| Thermal Interface | Pyrolytic graphite pad, 0.1mm (100μm) thickness, 700-1600 W/mK, decade-stable (no dry-out) |
| Secondary Path | Thermal pad to aluminum deck plate (~36cm² contact area spreads to 1170cm² deck) |
| Throttle Point | Software throttle at 70°C |
| Warning Point | User alert at 75°C |
| Shutdown Point | Software shutdown at 80°C |
| Hardware Cutoff | KSD9700 at 85°C (last resort) |

### 2.4 Power via USB-C Breakout

| Component | Specification |
|-----------|---------------|
| Breakout Board | Adafruit USB Type C Breakout Board (MPN: #4090), 5.1KΩ CC resistors for 5V/1.5A sink mode |
| Purpose | Field-replaceable power cable without soldering |
| Cable | Standard USB-C, spare in lid foam |

---

## 3. Storage Subsystem

### 3.1 Storage Philosophy: Passive Survivability

**Design Principle:** For true 50-year "seed vault" survivability, the Ark relies on **physics-based retention** (SLC NAND) rather than active maintenance (quarterly scrubs, PAR2 repair). This is the "Vault Edition" architecture.

**Why SLC NAND:**
- TLC NAND (3 bits/cell): 8 voltage levels, narrow margins, 1-2 year retention unpowered
- MLC NAND (2 bits/cell): 4 voltage levels, 3-5 year retention unpowered  
- **SLC NAND (1 bit/cell):** 2 voltage levels, wide margins, **10+ years specified, 50+ years extrapolated**

Arrhenius modeling of electron leakage at 25°C storage suggests SLC NAND retains data integrity for 50+ years. This is extrapolation beyond manufacturer specs, but physics supports the margin. Dual-device redundancy (NVMe + SD) plus cold spares provide defense-in-depth.

### 3.2 Storage Architecture

| Media | Purpose | Model | Location | Retention |
|-------|---------|-------|----------|----------|
| NVMe (installed) | Primary corpus + OS | Swissbit X-76m 480GB SLC | OPi 5+ M.2 slot | 50+ years |
| NVMe (cold spare) | Pre-loaded corpus clone | Swissbit X-76m 480GB SLC | Lid foam, Faraday bag | 50+ years |
| SD (archival) | Critical corpus subset | Swissbit S-56 128GB SLC | OPi 5+ SD slot | 50+ years |
| SD (cold spare) | Pre-loaded backup clone | Swissbit S-56 128GB SLC | Lid foam, Faraday bag | 50+ years |
| eMMC (onboard) | Lifeboat OS recovery | OPi 5+ internal 32GB | Onboard | 10+ years |

**Boot Priority:** NVMe (primary) → SD (archival) → eMMC (Lifeboat). Configured via U-Boot in SPI flash.

**Cold Spare Strategy:** Both spare drives are pre-loaded with complete corpus and sealed in Faraday bags with desiccant. If primary storage fails integrity check on boot, user can swap to spare with zero data loss.

### 3.3 Primary Storage: Swissbit X-76m 480GB SLC NVMe

| Parameter | Specification |
|-----------|---------------|
| Model | Swissbit X-76m |
| Part Number | SFPC480GV3AA4TO-I-OC-A26-STD |
| Capacity | 480GB |
| Interface | PCIe Gen3 x4, NVMe 1.3 |
| Form Factor | M.2 2280 |
| **NAND Type** | **True SLC (1 bit/cell)** |
| Temperature Range | -40°C to +85°C (industrial) |
| Endurance | 60,000 P/E cycles |
| Power Draw | 3.5W active, 50mW idle |
| Power-Loss Protection | Yes (capacitor-backed) |
| **Data Retention** | **10+ years specified, 50+ years extrapolated** |
| MTBF | 3,000,000 hours |
| Price | ~$800 each |

### 3.4 Archival Storage: Swissbit S-56 128GB SLC SD

| Parameter | Specification |
|-----------|---------------|
| Model | Swissbit S-56 |
| Part Number | SFSD128GN3AA4TO-I-OG-A21-STD |
| Capacity | 128GB |
| Interface | SD UHS-I |
| Form Factor | Full-size SD |
| **NAND Type** | **True SLC (1 bit/cell)** |
| Temperature Range | -40°C to +85°C (industrial) |
| **Data Retention** | **10+ years specified, 50+ years extrapolated** |
| Price | ~$200 each |

**Archival SD Purpose:** Contains critical corpus subset (medical, repair, agriculture, reference) that fits in 128GB. Provides independent backup on different storage controller. Mounted read-only at /archive after archival seal.

### 3.5 Selection Rationale

| Requirement | Swissbit SLC Capability |
|-------------|-------------------------|
| 50-year passive survivability | True SLC physics (wide voltage margins) |
| Zero maintenance | No quarterly scrubs or PAR2 repair needed |
| Field conditions | -40°C to +85°C operating |
| Data integrity | Power-loss protection + SLC retention |
| Industrial pedigree | Swissbit supplies aerospace, defense, medical |

### 3.6 Partition Layout

```
/dev/nvme0n1 (480GB SLC NVMe → ~447 GiB formatted)
├── nvme0n1p1  /boot      (512 MB)  - Boot partition, FAT32
├── nvme0n1p2  /          (20 GB)   - Root filesystem, EXT4, OverlayFS base
├── nvme0n1p3  /var       (10 GB)   - Logs, runtime data, EXT4
├── nvme0n1p4  /recovery  (35 GB)   - Factory Reset Image + integrity manifests
└── nvme0n1p5  /data      (381 GB)  - Knowledge base corpus, EXT4
                          ─────────
                          ~446.5 GB total

/dev/mmcblk1 (128GB SLC SD → ~119 GiB formatted)
└── mmcblk1p1  /archive   (119 GB)  - Critical corpus subset, EXT4, READ-ONLY

/dev/mmcblk0 (32GB eMMC)
└── mmcblk0p1  /          (32 GB)   - Lifeboat OS (minimal recovery)
```

**Note:** "480GB" SLC NVMe provides ~447 GiB raw capacity. After formatting with EXT4 (~1% overhead) and reserved blocks, ~446 GiB is usable.

**Archival Seal Mode:** After corpus loading, `ark-seal --mode=archival` remounts /data and /archive as read-only, generates SHA-256 integrity manifests, and stores manifests in /recovery/manifests/ (triple redundancy: NVMe, SD, eMMC).

### 3.7 Data Protection

| Layer | Mechanism |
|-------|-----------|
| **Primary retention** | **SLC NAND physics (50+ year passive retention)** |
| Root filesystem | Read-only base with OverlayFS |
| Corpus data | Read-only after archival seal |
| Integrity verification | SHA-256 manifests checked on boot |
| Factory image | Stored in /recovery partition |
| Recovery | Lifeboat OS on separate eMMC media |
| Cold backup | Pre-loaded SLC NVMe spare in Faraday bag |
| Archival backup | Pre-loaded SLC SD spare in Faraday bag |

**PAR2 Status:** PAR2 parity files retained as defense-in-depth but no longer primary retention strategy. SLC physics provides the 50-year margin; PAR2 provides additional repair capability if partial degradation occurs.

### 3.8 Storage Budget

**Authoritative storage allocation** (see 05-library-manifest.md §4 for library details):

| Partition | Allocation | Contents |
|-----------|------------|----------|
| / (root) | 20 GB | OS 8GB, LLM 4GB, models 2GB, apps 500MB, 33% buffer |
| /var | 10 GB | Logs (7-day rotation), cache, temp |
| /recovery | 35 GB | Factory image ~20GB, integrity manifests ~1GB |
| /data | 381 GB | Library 167GB, expansion 214GB |
| /archive (SD) | 119 GB | Critical corpus subset (medical, repair, reference) |
| **NVMe Total** | **~446 GB** | Fits 447 GiB formatted capacity |
| **SD Total** | **~119 GB** | Independent archival backup |

**Library breakdown** (from 05-library-manifest.md):

| Component | Size |
|-----------|------|
| Raw content (Wikipedia, iFixit, WikiHow, OpenStax, manuals) | 55 GB |
| Processed (chunks, embeddings, index, metadata, images) | 97 GB |
| **Library subtotal** | **152 GB** |
| + 10% headroom | 15 GB |
| **Library total** | **167 GB** |

**Storage Budget Impact (Vault Edition):**

| Metric | TLC (Prior) | SLC (Current) | Delta |
|--------|-------------|---------------|-------|
| NVMe Capacity | 512GB | 480GB | -32GB |
| /data Partition | 404GB | 381GB | -23GB |
| Library Requirement | 167GB | 167GB | unchanged |
| Headroom | 237GB | 214GB | -23GB |

**Note:** 381GB /data still exceeds library requirement (167GB) with comfortable margin for future expansion. The 23GB reduction is acceptable trade-off for 50-year passive survivability.

---

## 4. Power Management

### 4.1 Architecture Overview

The **Lazarus-Phoenix** power architecture is designed for **decade-long dormancy** followed by field activation. It solves two critical problems that would brick conventional systems:

1. **Lazarus Problem:** LiFePO4 BMS quiescent drain (1-5mA) causes deep discharge → copper anode dissolution → dendrites → thermal runaway risk after ~10 years storage
2. **Phoenix Problem:** Standard buck MPPT controllers can't wake a 0V battery (chicken-and-egg: controller needs battery power to charge battery)

**Solution:** Sodium-ion battery (0V-safe chemistry) + boost MPPT (PV-powered logic) + passive bootstrap (Ohm's Law fallback)

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
    └─────┬─────┘          └──────┬──────┘         └─────────────┘
          │                       │
          └───────────────────────┤
                                  │
                      ┌───────────┴───────────┐
                      │    8S4P Na-ion Pack   │
                      │   40Ah / 960Wh        │
                      │   12V-32V range       │
                      │   + JK BMS (8S)       │
                      └───────────┬───────────┘
                                  │
                      ┌───────────┴───────────┐
                      │   Load Disconnect     │
                      │  Latching Relay +     │
                      │  TL431 Supervisor     │
                      │  (closes at >20V)     │
                      └───────────┬───────────┘
                                  │
                      ┌───────────┴───────────┐
                      │   Wide-Input DC-DC    │
                      │   12-32V → 5V @ 5A    │
                      └───────────┬───────────┘
                                  │
                      ┌───────────┴───────────┐
                      │     OPi5+ 32GB        │
                      │   + 512GB NVMe        │
                      │   + Peripherals       │
                      └───────────────────────┘
```

### 4.2 Solar Panels

**Selected: 3× Renogy 50W 12V Foldable Panels (Parallel)**

| Parameter | Per Panel | Parallel Total |
|-----------|-----------|----------------|
| Power | 50W | 150W |
| Vmp | 17.6V | 17.6V (unchanged) |
| Imp | 2.8A | 8.4A |
| Voc | 21.6V | 21.6V (unchanged) |
| Isc | 3.2A | 9.6A |
| Type | Monocrystalline | — |
| Folded Dimensions | ~300 × 200 × 25mm | — |
| Weight | ~1.0 kg each | ~3.0 kg total |

**Cold Weather Voc Check:**
- Voc increases ~0.3%/°C below 25°C
- At -20°C: Voc = 21.6V × 1.135 = 24.5V
- Within Genasun 26V input limit ✓

**Parallel Wiring Rationale:**
- Boost MPPT requires Vpanel < Vbattery (for step-up operation)
- 12V panels (18V Vmp) boost to 24V battery nominal
- Parallel configuration increases current, not voltage
- Single panel failure: 66% capacity remains operational

**Storage:** Three panels stored in case cavity (stacked or folded). Single weatherproof XT60 Y-harness connects all panels.

### 4.3 MPPT Controller

**Selected: Genasun GVB-8-WP-Li-CV (Custom Voltage)**

| Parameter | Specification |
|-----------|---------------|
| Model | Genasun GVB-8-WP-Li-CV |
| Topology | **Boost** (step-up) — NOT buck |
| Input Range | 5V-26V |
| Output Range | Up to 48V (custom programmed for 32V) |
| Max Output Current | 8A |
| Efficiency | 99% peak, >95% across range |
| **0V Battery Wake** | **YES** (explicit datasheet claim: "Trickle Charge to Recover Dead (0V) Battery: YES") |
| Capacitors | Ceramic/tantalum (no electrolytics — decade-stable) |
| Enclosure | IP68 potted (WP = Waterproof version) |
| Self-Consumption | 0.2W |
| Dimensions | 80 × 50 × 25mm |
| Location | Basement layer |

**Custom Programming Required:**
- Absorption voltage: 32.0V (4.0V/cell × 8S)
- Float voltage: 26.4V (3.3V/cell × 8S) or disabled
- Contact: Sunforge LLC (info@sunforgellc.com)
- Request: "Custom 8S Na-ion profile, 32.0V absorption, 12-26V input range"

**Why Boost Topology Works at 0V:**
1. Controller logic powers from **PV input** (18V), not battery
2. Passive diode path: PV+ → Inductor → Diode → Battery+
3. Current flows whenever Vpanel > Vbattery, no active switching required at 0V
4. Once battery reaches ~5V, active MPPT engages for maximum efficiency

**Current Limiting Behavior:**
With 150W input at low battery voltage:
- At 12V battery: 150W × 0.95 / 12V = 11.9A → exceeds 8A limit
- Controller current-limits to 8A × 12V = 96W utilized
- ~54W panel capacity temporarily unused (acceptable)
- Full 150W utilized once battery exceeds ~18V

### 4.4 Battery System

**Selected: 8S4P HiNa 32140 Sodium-Ion (Polyanion Chemistry)**

| Parameter | Specification |
|-----------|---------------|
| Cell Type | HiNa 32140 cylindrical |
| Chemistry | **Polyanion (NFPP — sodium iron pyrophosphate)** |
| Cell Capacity | 10Ah |
| Cell Voltage | 1.5V (empty) → 4.0V (full) |
| Cell Dimensions | 32mm diameter × 140mm length |
| Configuration | 8S4P (32 cells total) |
| Pack Voltage | 12V (empty) → 32V (full), **24V nominal** |
| Pack Capacity | 40Ah (960Wh) |
| Cycle Life | 3000+ cycles to 80% DoD |
| Operating Temp | -20°C to +60°C |
| Charge Temp | 0°C to +45°C |
| **0V Storage** | **SAFE** — aluminum collectors on both electrodes |

**Why Sodium-Ion Survives Decade Dormancy:**

| Chemistry | Anode Collector | 0V Behavior |
|-----------|-----------------|-------------|
| Li-ion / LiFePO4 | **Copper** | Copper dissolves below ~2.5V → dendrites → thermal runaway |
| Na-ion (Polyanion) | **Aluminum** | Aluminum stable at all potentials → electrochemically inert |

At 0V, a Na-ion Polyanion cell acts as a passive high-impedance capacitor. No voltage potential exists to drive parasitic reactions. Terminals can be shorted indefinitely with no damage.

**Validation:** Hithium (Na-ion manufacturer) published 6-month 0V storage test with zero capacity loss. SEI layer regenerates on first charge with <5% one-time capacity reduction.

**Cathode Chemistry Selection:**

| Type | 0V Stability | Moisture Sensitivity | Decision |
|------|--------------|---------------------|----------|
| Layered Oxide | Poor | HIGH | ❌ Rejects |
| Prussian Blue | Medium | MEDIUM | ❌ Rejects |
| **Polyanion (NFPP)** | **Excellent** | **LOW** | **✅ SELECTED** |

**BMS: JK BMS BD4A8S4P**

| Parameter | Specification |
|-----------|---------------|
| Configuration | 8S (8 cells in series) |
| Balancing | Active (transfers energy between cells, not passive bleed) |
| Interface | Bluetooth (app configuration) |
| **0V Wake-up** | **Supported** — charges from depleted state |
| OVP | 4.00V/cell (32.0V pack) |
| UVP | 1.50V/cell (12.0V pack) |
| Balance Start | 3.80V/cell |
| Quiescent Current | <1mA (acceptable for Na-ion 0V storage) |

**Pack Assembly:**
- Spot-weld 0.15mm × 8mm pure nickel strips
- 3D-printed cell holders for 32140 format (2×4 arrangement per 4P group)
- Conformal coat all solder/weld connections
- XT60 connectors (gold-plated, oxidation-resistant)
- Fish paper insulation between series groups

### 4.5 Passive Bootstrap Circuit

**Purpose:** Unconditional 0V recovery via Ohm's Law. Backup if Genasun controller fails after decade storage.

```
PV+ ──┬── 1N5408 ──┬── 47Ω 25W ──┬── Battery+
      │            │             │
      │            │             │
      └── Genasun PV+ ───────────┘
```

| Component | Specification | Rationale |
|-----------|---------------|-----------|
| Diode | 1N5408 (1000V/3A silicon rectifier) | **NOT Schottky** — Schottky has high reverse leakage (mA) that worsens with temperature |
| Resistor | 47Ω 25W vitreous enamel wirewound | Limits current, handles power dissipation, decade-stable |
| Fuse | 2A slow-blow | Protection against short circuit |

**Operation at 0V Battery:**
- Panel Voc = 22V (typical)
- Current = 22V / 47Ω = 0.47A
- Charge rate = C/85 (very safe trickle for Na-ion)
- Power dissipation = 22V × 0.47A = 10.3W (resistor rated 25W, adequate margin)
- Once battery reaches ~5V, Genasun takes over with active MPPT

**Why Silicon Diode (NOT Schottky):**
Schottky diodes have high reverse leakage current (often mA range) that increases exponentially with temperature. Over decade storage in a potentially hot enclosure (60°C+), this would slowly drain the battery or cause thermal failure. Silicon rectifiers (1N5408) have nA-range leakage — orders of magnitude safer.

### 4.6 Analog Status Voltmeter

**Purpose:** User feedback with zero silicon in the measurement path. Zero decade-degradation risk.

**Selected: 0-30V DC Moving-Coil Panel Meter**

| Parameter | Specification |
|-----------|---------------|
| Type | Moving-coil (d'Arsonval mechanism) |
| Range | 0-30V DC |
| Mechanism | Permanent magnet + wire coil + jeweled bearings |
| Power Required | None (passive mechanical indication) |
| Decade Stability | Excellent — magnets stable indefinitely, coil doesn't degrade unpowered |
| Failure Mode | Bearing seizure if moisture intrudes — seal meter in enclosure |
| Location | Deck faceplate, visible when lid open |

**Voltage-to-State Mapping (user reference label):**

| Meter Reading | Battery State | System Capability |
|---------------|---------------|-------------------|
| 0V | Depleted/Dormant | Charging via passive bootstrap |
| 5-12V | Lazarus wake | Genasun active, charging |
| 12-16V | Low | Still charging, wait |
| **16-20V** | **Emergency Mode** | **Text retrieval available (~3W)** |
| **20-24V** | **Full Mode** | **Full LLM inference available** |
| 24-32V | Comfortable | Extended runtime, full features |
| 32V | Full | Fully charged |

**Why NOT Digital Display (OLED/MCU) for Primary Status:**
- MCU flash memory: 20-year retention spec, but untested at 10+ years unpowered
- Electrolytic caps in voltage regulators: ESR drift from electrolyte diffusion
- Solder joints on fine-pitch packages: Tin whisker risk
- Every silicon component is a potential decade failure point
- Analog meter has **zero** silicon — purely mechanical

**Note:** The OLED status display (§6.2) provides detailed digital readout during normal operation. The analog meter is the **fallback** that works even if all electronics have failed.

### 4.7 Load Disconnect

**Purpose:** Prevent boot-loop where computer tries to start, crashes battery voltage, prevents charging. Ensures battery reaches usable charge level before load is connected.

```
Battery+ ──┬── [TL431 Sense] ── Resistor Divider ── Battery-
           │
           └── [Latching Relay NO] ── Load (DC-DC → Computer)
```

| Component | Specification |
|-----------|---------------|
| Voltage Sense | TL431 precision shunt reference (2.5V internal ref) |
| Threshold | **Opens <18V, Closes >20V** (2V hysteresis) |
| Switch | Panasonic ADW1124HLW latching relay |
| Relay Specs | 24V coil, 10A contacts, bistable (latching) |

**Resistor Divider Calculation:**
- TL431 triggers at 2.5V reference
- For 20V trip: R1/R2 ratio = (20V - 2.5V) / 2.5V = 7:1
- R1 = 70kΩ, R2 = 10kΩ (standard values)
- Hysteresis via positive feedback resistor

**Operation:**
1. Battery voltage sensed via resistor divider
2. When Vbat rises above 20V, TL431 triggers
3. Pulse fires relay SET coil, latches closed
4. Load now connected to battery — computer can boot
5. If voltage drops below 18V (heavy load + low charge), pulse fires RESET coil
6. Relay opens, disconnects load — battery can recharge without boot-loop

**Why Latching Relay:**
- Standard relay requires continuous coil power (wastes energy)
- Solid-state relay has leakage current (defeats purpose)
- **Latching relay:** One pulse to set, one pulse to reset, holds position indefinitely with zero power

### 4.8 Power Input Options

| Input | Connector | Purpose |
|-------|-----------|---------|
| Solar Primary | XT60 | 3× panels via Y-harness → MPPT PV input |
| Scavenger | Binding Posts (banana jack) | External 18-30V sources → Battery bus (via blocking diode) |

**Scavenger Input Circuit:**

```
Binding Post (+) → 15A ATO Fuse → 1N5408 Diode → Battery+ Bus
Binding Post (-) → Battery- Bus
```

| Protection | Component | Function |
|------------|-----------|----------|
| Overcurrent | 15A ATO fuse (in fuse block) | Limits charge current |
| Backfeed | 1N5408 silicon diode | Prevents internal battery from discharging to external source |
| Reverse polarity | Diode (reverse-biased) | Blocks current if connected backwards |

**Scavenger Input Specifications:**

| Parameter | Value |
|-----------|-------|
| Voltage Range | **18V - 30V** (must exceed Na-ion pack voltage) |
| Max Current | 15A (fuse limited) |
| Compatible Sources | Two car batteries in series (~25V), generator, solar direct bypass |

**⚠️ IMPORTANT:** Scavenger voltage must exceed battery pack voltage to charge. A single 12V car battery will NOT work — need two in series (~25V) or equivalent.

**Labeling (on faceplate near binding posts):**
```
⚠️ SCAVENGER INPUT ⚠️
18-30V ONLY • 15A MAX
NOT 12V — NEED 24V+
RED = POSITIVE (+)
BLACK = NEGATIVE (-)
```

### 4.9 Kill Switch

**Selected: Safety Toggle Switch DPST 20A with Guard**

| Parameter | Specification |
|-----------|---------------|
| Type | DPST (Double-Pole Single-Throw), ON-OFF |
| Rating | 20A @ 125VAC, 15A @ 250VAC |
| Panel Cutout | 1/2" (12.7mm) diameter |
| Guard | Flip-up safety cover (red) |
| Terminals | Screw or spade |
| Function | Disconnects both solar AND battery simultaneously |
| Location | Deck faceplate |

**DPST Rationale:** Prevents "Solar Fry" failure mode where MPPT is destroyed by voltage spike when solar connected but battery disconnected.

### 4.10 Protection Chain

| Component | Function | Specification |
|-----------|----------|---------------|
| NTC Thermistor | Inrush current limiting | 5D-9 (5Ω cold, <0.1Ω hot) |
| 15A Manual Reset Breaker | Battery overcurrent | ATO footprint, push-button reset |
| 10A Blade Fuse (Red) | Solar/aux overcurrent | ATO/ATC |
| 5A Blade Fuse (Tan) | Logic overcurrent | ATO/ATC |
| KSD9700 | Thermal protection | Opens at 85°C, auto-resets at ~65°C |

**Fuse Block:** 4-way automotive ATO/ATC block. Spare fuses in lid foam.

**Negative Bus:** 6-position 30A barrier terminal block. All negative/return wires terminate here in star topology.

**Note:** The Galley Power LVD module from previous spec is **REMOVED** — replaced by the TL431 + latching relay load disconnect (§4.7) which provides smarter boot-loop prevention.

### 4.11 DC-DC Converter

**Selected: Wide-Input Isolated DC-DC Converter**

| Parameter | Specification |
|-----------|---------------|
| Input Range | **12-32V DC** (matches Na-ion full voltage range) |
| Output | 5V DC, 5A continuous (25W) |
| Efficiency | >90% |
| Isolation | Yes (galvanic) |
| Capacitors | Solid polymer (decade-stable) |
| Location | Deck underside |

**Requirement Change:** The original Meanwell SD-15A-5 (9.2-18V input) cannot handle the 24V nominal Na-ion system. Wide-input converter required to handle 12V (empty) to 32V (full) range.

### 4.12 Power Budget

**5V Rail Power Draw (unchanged from original spec):**

| Component | Active (W) | Idle (W) |
|-----------|------------|----------|
| OPi 5+ | 10 | 4 |
| NVMe SSD | 3 | 0.05 |
| E-ink + IT8951 | 1.2 | 0.1 |
| OLED Status | 0.1 | 0.1 |
| ReSpeaker | 1 | 0.5 |
| Amplifier | 2 | 0.1 |
| DS3231 RTC | 0.001 | 0.001 |
| Misc (LEDs, GPIO) | 0.2 | 0.1 |
| **5V Rail Total** | **~18W** | **~5W** |

**DC-DC Conversion (at 90% efficiency):**

| Mode | 5V Load | Battery Draw | Loss |
|------|---------|--------------|------|
| Active Query | 18W | 20W | 2W |
| Idle | 5W | 5.6W | 0.6W |
| Standby | 2.5W | 2.8W | 0.3W |

### 4.13 Runtime Calculations

- Battery capacity: 960Wh
- Usable (80% DoD to ~16V): 768Wh

| Mode | Battery Draw | Runtime |
|------|--------------|---------|
| Active Query (with voice) | 20W | ~38 hours |
| Idle (display static) | 5.6W | ~137 hours (~6 days) |
| Standby | 2.8W | ~274 hours (~11 days) |

**Trade-off Note:** Reduced from original 1280Wh (LiFePO4) to 960Wh (Na-ion). Trade-off accepted for decade dormancy capability — the original LiFePO4 would be **dead or dangerous** after 10 years storage.

### 4.14 Charging Times

**From 0V (Lazarus Wake Sequence):**

| Phase | Duration | Battery Voltage | Notes |
|-------|----------|-----------------|-------|
| 0-0.5h | Passive bootstrap | 0V → 5V | Diode + resistor trickle (~0.5A) |
| 0.5-2h | Genasun active (8A limited) | 5V → 12V | Controller wakes, boost charging |
| 2-4h | Genasun active (8A limited) | 12V → 16V | Approaching Emergency Mode |
| **~4h** | **Emergency Mode available** | **16V (5% SoC)** | **Text retrieval possible** |
| 4-6h | Genasun active | 16V → 20V | Load disconnect closes |
| **~6h** | **Full Mode available** | **20V (20% SoC)** | **Full LLM inference** |
| 6-18h | Full MPPT charging | 20V → 32V | Comfortable to full |

**From Partial Charge:**

| Source | Rated | Actual (65% yield) | 20%→100% Time |
|--------|-------|-------------------|---------------|
| 150W panels (full sun) | 150W | ~100W | ~8 hours |
| 150W panels (partial sun) | 150W | ~50W | ~16 hours |
| Scavenger (25V @ 10A) | — | ~250W | ~3 hours |

### 4.15 Operating Modes

| Mode | Voltage Threshold | Power Draw | Capabilities |
|------|-------------------|------------|--------------|
| Dormant | 0V | 0W | Stored at 0V, terminals shorted |
| Lazarus Wake | 0V → 16V | — | Charging only, no boot |
| **Emergency Mode** | **16V (5% SoC)** | **~3W** | Text retrieval, static reference, no LLM |
| **Full Mode** | **20V (20% SoC)** | **~10-18W** | Full LLM inference, all features |
| Comfortable | 24-32V | ~10-18W | Extended runtime, full features |

**Emergency Mode Value:** Allows immediate access to critical survival information (medical guides, repair instructions, reference tables) while battery continues charging. User doesn't have to wait 6+ hours for Full Mode.

### 4.16 Decade Storage Protocol

**Preparation (entering long-term storage):**
1. Discharge pack to 12V (0% SoC) via resistive load or normal use
2. Continue discharge to 0V using low-current load (~100mA) — takes several days
3. Verify 0V with multimeter on each cell
4. Short positive and negative pack terminals with copper bus bar (equalizes all cells)
5. Verify pack reads 0.0V and remains stable
6. Seal entire Ark in Mylar bag with fresh desiccant
7. Store in cool, dark location
8. **Can remain dormant indefinitely** — no chemical degradation at 0V

**Why Short Terminals:**
Prevents any individual cell from drifting to reverse polarity (which would damage that cell). All cells equalize at 0.0V. Bus bar can remain permanently installed.

**Reactivation (Lazarus Wake):**
1. Remove from Mylar bag
2. Open case, remove terminal shorting bar
3. Deploy solar panels, connect to XT60 jack
4. Observe analog voltmeter: should begin rising from 0V
5. Wait for 16V (Emergency Mode) or 20V (Full Mode)
6. Press power button to boot
7. First boot may take slightly longer (SEI layer reformation)

### 4.17 Pre-Build Validation Protocol

**Before committing to full build, validate key assumptions:**

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| Cell verification | Request HiNa datasheet | Confirms Polyanion NFPP chemistry |
| 0V discharge | Discharge 4 sample cells to 0V | No swelling, no venting, stable |
| 0V storage | Short terminals, monitor 72 hours | Remains at 0V, no thermal event |
| 0V recovery | Charge from 0V to 3.0V with bench supply | Smooth voltage rise, no anomalies |
| Capacity check | Full cycle 1.5V → 4.0V | >95% rated capacity |
| Genasun 0V test | Apply 18V PV input with 0V load | Controller wakes, output rises |
| Full system | Simulate Lazarus wake (scaled) | 0V → 20V in <6 hours |

**Estimated validation time:** ~2 weeks including shipping
**Estimated validation cost:** ~$100 (sample cells + shipping)

---

## 5. Enclosure & Thermal

### 5.1 Enclosure Selection

**Selected: Pelican 1557 Air Case (Orange)**

| Parameter | Specification |
|-----------|---------------|
| Model | Pelican 1557 Air |
| Exterior Dimensions | 487 × 401 × 267 mm (19.18" × 15.79" × 10.50") |
| Interior Dimensions | 440 × 330 × 248 mm (17.33" × 13.00" × 9.75") |
| Lid Depth | 51 mm (2.00") |
| Base Depth | 196 mm (7.70") |
| Weight (empty) | 3.0 kg (6.6 lbs) |
| Internal Volume | 1.27 ft³ (0.04 m³) |
| Material | HPX² Polymer (proprietary lightweight polypropylene blend) |
| IP Rating | IP67 |
| Pressure Equalization | Automatic Gore-Tex valve |
| Color | Orange (high visibility for emergency location) |
| Buoyancy | 80 lbs |
| Temp Range | -60°F to +160°F (-51°C to +71°C) |

**Selection Rationale:**
- **196mm base depth** provides clearance for Na-ion pack + deck structure
- **1.27 ft³ internal volume** (93% more than Pelican 1500) allows comfortable component layout
- **3.0 kg empty weight** (lighter than Pelican 1500's 3.2 kg) due to HPX² polymer
- **Orange color** ensures high visibility for emergency retrieval scenarios
- **Air series** optimized for lighter weight without sacrificing protection

### 5.2 Faraday Shielding

**Selected: Mission Darkness TitanRF Faraday Fabric**

| Component | Specification |
|-----------|---------------|
| Primary | Mission Darkness TitanRF copper/nickel fabric lining entire interior |
| Material | Copper/nickel plated polyester ripstop |
| Certifications | MIL-STD-188-125, IEEE 299-2006 |
| Attenuation | 80-100+ dB (exceeds 40dB requirement) |
| Seams | 3M 1181 conductive tape, 20mm overlap minimum |
| Lid-Base Seal | Fabric overlap creates continuous shield when latched |

**Validation Test:** Place smartphone inside sealed case, call it. Should go directly to voicemail. If phone rings, locate and repair leak.

### 5.3 Enclosure Modifications

| Modification | Location | Purpose |
|--------------|----------|---------|
| XT60 panel mount | Deck faceplate | Solar input |
| Binding posts | Deck faceplate | Scavenger power input |
| Analog voltmeter | Deck faceplate | Battery status (0-30V) |
| USB-A panel mounts (×3) | Deck faceplate, angled | Display source, Keyboard, Aux |
| D-pad cutout | Deck faceplate | Navigation |
| Button cutouts (×3) | Deck faceplate | Power, ESC, Home |
| OLED window | Deck faceplate | Digital status display |
| Speaker grill | Deck faceplate | Audio output |
| Mic hole | Deck faceplate | ReSpeaker |
| Display window | Lid | 10.3" E-ink viewing |

### 5.4 Internal Layout

**Basement Layer (bottom):**
- Na-ion 8S4P battery pack in Kaizen foam pocket
- Genasun GVB-8-WP MPPT controller
- Passive bootstrap circuit board
- Spare parts storage (Kaizen foam)

**Deck Underside (middle-bottom):**
- OPi 5+ with heatsink (mounted inverted)
- NVMe SSD
- Wide-input DC-DC converter
- TPA2016D2 amplifier
- DS3231 RTC module
- USB-C breakout board
- Fuse block with breaker
- Protection chain components (NTC, KSD9700)
- Load disconnect (latching relay + TL431)
- INA219 power monitor
- Wiring harness

**Deck Faceplate (middle-top):**
- Industrial D-pad
- Buttons (Power, ESC, Home)
- OLED status display
- **Analog voltmeter (0-30V)**
- XT60 connector
- Binding posts
- USB-A ports (angled)
- Speaker behind grill
- ReSpeaker mic array

**Cavity (open space when lid open, 47mm height):**
- Keyboard in neoprene sleeve (~280×100×20mm)
- **3× foldable solar panels** (~300×200×25mm each, stacked)
- Coiled USB display cable
- GPS dongle
- USB LED light
- Scavenger cables

**Lid:**
- 10.3" E-ink display
- IT8951 controller
- Polycarbonate storm window (with air gap)
- USB panel mount for display connection
- Lid foam with spares pockets

### 5.5 Deck Support Structure

| Component | Specification |
|-----------|---------------|
| Corner Posts | PVC Schedule 40 pipe sections, ~25mm OD |
| Post Height | **147mm** (floor to deck underside) |
| Post Installation | Through bored holes in foam, resting on case floor |
| Deck Plate | 2mm 6061-T6 aluminum |
| Mounting | Gravity-loaded, posts contact case floor directly |
| Deck Attachment | 4× M4 knurled thumbscrews (tool-free removal) |
| Backup | 5mm holes for zip-tie attachment if thumbscrews lost |
| Lateral Stability | Foam wedges at deck edges |

**Vertical Stack (from floor):**

| Layer | Height | Cumulative |
|-------|--------|------------|
| Case floor | 0mm | 0mm |
| Na-ion pack (8S4P) | ~110mm | 110mm |
| Clearance above battery | 7mm | 117mm |
| Electronics zone | 30mm | 147mm |
| Deck posts (top) | — | 147mm |
| Deck plate | 2mm | 149mm |
| Cavity | 47mm | 196mm |
| Case rim | — | 196mm |

**Foam Bores:** 4× holes (~30mm diameter) bored through Kaizen foam at post locations, allowing posts to rest directly on case floor. Foam retained around posts for lateral damping.

### 5.6 Thermal Strategy

| Layer | Mechanism |
|-------|-----------|
| Primary | Passive conduction: OPi heatsink → pyrolytic graphite pad → aluminum deck plate |
| Secondary | Thermal pad to case floor via standoffs |
| Ventilation | Case open during heavy use (recommended) |
| Software | Fever Breaker protocol (throttle 70°C, warn 75°C, shutdown 80°C) |
| Hardware | KSD9700 thermal cutoff at 85°C (auto-resets at ~65°C) |

### 5.7 Environmental Protection

| Threat | Protection |
|--------|------------|
| Dust | IP67 gaskets, sealed connectors |
| Water (liquid) | IP67 rating |
| Water (vapor) | Indicating silica gel desiccant (rechargeable) |
| Shock | Kaizen foam (basement), closed-cell foam (deck edges) |
| EMP | Full Faraday fabric lining, >40dB attenuation |
| UV | Black case, display window UV filter |
| Long-term vapor | Mylar bag for storage >12 months |

**Foam Specifications:**

| Foam Type | Location | Specification |
|-----------|----------|---------------|
| Kaizen Foam | Basement (battery pocket, MPPT, spares) | 57mm thick, 30 kg/m³ density, polyethylene closed-cell, self-healing cut edges |
| Kaizen Foam | Lid (spares pockets) | 30mm thick, same material, layered for pocket depth |
| Closed-Cell PE | Deck edges (lateral constraint) | 10mm thick, 45 kg/m³ density, polyethylene, wedge-cut for snug fit |
| Neoprene | Keyboard sleeve | 3mm thick, ~60 kg/m³, water-resistant, sewn sleeve |

**Kaizen Foam Rationale:** Polyethylene closed-cell foam is preferred over polyurethane for decade-scale storage. PE doesn't off-gas, won't crumble, and maintains cushioning properties indefinitely. Self-healing property allows clean cuts for component pockets.

### 5.8 Desiccant System

| Component | Specification |
|-----------|---------------|
| Type | Indicating silica gel |
| Indicator | Orange (dry) → Pink/Green (saturated) |
| Quantity | 3× refillable mesh pouches |
| Regeneration | Bake at 120°C for 2-3 hours |
| Location | Distributed in case interior |

### 5.9 Storm Window

| Component | Specification |
|-----------|---------------|
| Material | Polycarbonate sheet, 3mm thick |
| Dimensions | 245 × 190mm (7.5mm border around display) |
| Mounting | Adhesive-only (silicone sealant + VHB tape corners) |
| Purpose | Absorbs impact, prevents screen damage if lid closed forcefully |
| UV Filter | Integrated or applied film |

**⚠️ No fasteners penetrate the lid.** Mechanical fasteners would breach the internal Faraday shielding. Silicone + VHB provides sufficient bond strength (>150 psi combined). See 02-assembly-specification.md §4.5 for installation procedure.

---

## 6. Display & I/O

### 6.1 Primary Display

**Selected: Waveshare 10.3" E-ink + IT8951 Controller**

| Parameter | Specification |
|-----------|---------------|
| Size | 10.3 inches diagonal |
| Resolution | 1872 × 1404 pixels |
| Colors | Black/White, 16-level grayscale |
| Refresh | Full: 450ms, Partial: 260ms |
| Controller | IT8951 (USB interface) |
| Interface | USB to OPi 5+ |
| Power | 1.2W during refresh, ~0 static (with IT8951 sleep) |
| Viewing Angle | >170° |
| Sunlight Readable | Yes |

**IT8951 Power Management:** Software must call sleep mode after each update to maintain near-zero static power.

### 6.2 OLED Status Display

**Selected: 1.3" SSD1306 128×64 I2C OLED**

| Parameter | Specification |
|-----------|---------------|
| Size | 1.3" diagonal |
| Resolution | 128×64 pixels |
| Controller | SSD1306 |
| Interface | I2C (address 0x3C) |
| Voltage | 3.3V-5V |
| Power | ~20mA active, ~0 static |
| Module Size | ~36×36×4mm |
| Purpose | Battery voltage, SOC, boot status, system state |
| Always Visible | Yes (even before E-ink boots) |

**Display Format:**
```
BAT: 24.5V (65%)
SYS: READY
```

**Note:** OLED provides detailed digital status during operation. Analog voltmeter (§4.6) provides decade-stable fallback.

### 6.3 Physical Controls

| Control | Type | Function | Location |
|---------|------|----------|----------|
| Nav Up | C&K AP Series pushbutton, IP67 | Navigate up | Deck faceplate (D-pad cluster) |
| Nav Down | C&K AP Series pushbutton, IP67 | Navigate down | Deck faceplate (D-pad cluster) |
| Nav Left | C&K AP Series pushbutton, IP67 | Navigate left | Deck faceplate (D-pad cluster) |
| Nav Right | C&K AP Series pushbutton, IP67 | Navigate right | Deck faceplate (D-pad cluster) |
| Nav Select | C&K AP Series pushbutton, IP67 | Select/confirm | Deck faceplate (D-pad cluster, center) |
| Power | C&K AP Series pushbutton, IP67 | On/off, long-press shutdown | Deck faceplate |
| ESC | C&K AP Series pushbutton, IP67 | Back, cancel | Deck faceplate |
| Home | C&K AP Series pushbutton, IP67 | Return to home screen | Deck faceplate |

**D-Pad Implementation:** Navigation uses 5 discrete IP67 pushbuttons arranged in cross pattern (up/down/left/right with center select). This approach provides:
- Field repairability (single button failure doesn't kill entire input)
- Commodity parts from multiple manufacturers
- Proven IP67 sealing per button
- Superior tactile feedback vs membrane

**Selected Switch Family:** C&K AP Series Industrial Pushbutton
- IP67 sealed (panel-mount with gasket)
- 1,000,000 cycle life
- Operating temp: -40°C to +85°C
- SPST-NO momentary
- 12mm panel cutout
- Multiple cap colors available (order separately)
- DigiKey/Mouser stocked

**ESD/EMP Protection:** Each GPIO input line includes a TVS diode array for transient suppression.

| Component | Specification |
|-----------|---------------|
| TVS Array | PESD5V0S4UF (4-channel) or equivalent |
| Clamping Voltage | 5.0V (matches GPIO logic level) |
| Peak Current | 7A (8/20µs pulse) |
| Capacitance | <1pF (no signal degradation) |
| Package | SOT-665 (tiny, fits on breakout PCB) |
| Quantity | 2× (covers 8 GPIO lines) |
| Location | Small breakout PCB near OPi 5+ GPIO header |

### 6.4 USB Keyboard

| Parameter | Specification |
|-----------|---------------|
| Type | Compact USB keyboard |
| Storage | Neoprene sleeve in cavity |
| Purpose | Primary text input for queries |
| Connection | USB-A port on deck faceplate |

### 6.5 Audio Input

**Selected: ReSpeaker USB Mic Array v2.0**

| Parameter | Specification |
|-----------|---------------|
| Model | Seeed ReSpeaker USB Mic Array v2.0 |
| Microphones | 4× MEMS |
| DSP | XMOS XVF-3000 |
| Features | AEC, beamforming, noise suppression (all in hardware) |
| Interface | USB (UAC compliant) |
| Mounting | Deck faceplate with silicone gasket |

### 6.6 Audio Output

| Component | Specification |
|-----------|---------------|
| Amplifier | SparkFun TPA2016D2 Class D, 2.8W |
| Speaker | 3W full-range, 8Ω, Ø50mm |
| Ground Loop Isolator | 3.5mm inline transformer |
| Capacitors | Solid polymer (decade-stable) |

**Signal Path:**
```
OPi 3.5mm → Ground Loop Isolator → TPA2016D2 → Speaker
```

### 6.7 External Ports

| Port | Type | Location | Purpose | Notes |
|------|------|----------|---------|-------|
| Solar Input | XT60 | Deck faceplate | Panel connection | 150W capacity |
| Scavenger Input | Binding posts (banana jack) | Deck faceplate | External 18-30V sources | NOT 12V |
| Display | USB-A (angled) | Deck faceplate | IT8951 source port | 500mm coiled cable to lid |
| Keyboard | USB-A (angled) | Deck faceplate | Primary text input | — |
| Aux USB | USB-A (angled) | Deck faceplate | GPS dongle, USB light | — |

### 6.8 Internal Interfaces

| Interface | Connection | Purpose |
|-----------|------------|---------|
| I2C Bus 1 | OPi GPIO → DS3231, OLED, INA219 | RTC, status display, power monitor |
| USB 3.0 #1 | OPi → Deck USB-A #1 → Coiled cable → IT8951 | E-ink display (zero-hinge wiring) |
| USB 2.0 #1 | OPi → ReSpeaker | Microphone array |
| USB 2.0 #2 | OPi → Keyboard | Text input |
| 3.5mm Audio | OPi → TPA2016D2 | Speaker output |
| GPIO | OPi → D-pad, buttons | Physical controls |
| USB-C | Breakout board → OPi | 5V power |

### 6.9 Power Monitoring

**Selected: Adafruit INA219 STEMMA QT**

| Parameter | Specification |
|-----------|---------------|
| Model | Adafruit INA219 (part #904) |
| Chip | Texas Instruments INA219 |
| Voltage Range | 0-26V DC (**Note: Pack can reach 32V — see below**) |
| Current Range | ±3.2A (with 0.1Ω onboard shunt) |
| Resolution | 12-bit ADC (voltage 4mV, current 0.8mA) |
| Accuracy | ±1% voltage, ±1% current |
| Interface | I2C (address 0x40) |
| Power Draw | 1mA standby |
| Location | Deck underside, after load disconnect |

**Voltage Range Note:** The Na-ion pack reaches 32V at full charge, but INA219 is rated for 26V max. **Solution:** Place INA219 **after** load disconnect relay. When relay is open (charging from 0-20V), INA219 sees 0V. When relay closes (>20V), INA219 sees 20-32V — slightly over spec but within typical 36V absolute max. Alternatively, use voltage divider on Vin+ (2:1 ratio, calibrate in software).

**Shunt Placement (High-Side Sensing):**
```
Load Disconnect Output → [INA219 Vin+] ─── 0.1Ω Shunt ─── [INA219 Vin-] → KSD9700 → DC-DC
```

### 6.10 Zero-Hinge Wiring

**Design Principle:** No cables routed through case hinge. Eliminates flex fatigue failure mode.

**Display Connection Architecture:**
```
OPi USB 3.0 #1 → Deck USB-A Panel Mount #1 (angled)
                           │
                           │ Coiled USB cable (500mm extended, ~150mm coiled)
                           │ Stored in cavity when lid closed
                           │
                           ▼
                   Storm Window Access Port
                           │
                           ▼
                   IT8951 Controller → E-ink Display
```

---

## 7. Timekeeping

### 7.1 RTC Module

**Selected: DS3231 (Adafruit #3013 or ChronoDot)**

| Parameter | Specification |
|-----------|---------------|
| Model | DS3231SN (genuine Maxim IC) |
| Module | Adafruit DS3231 Precision RTC (#3013) or Macetech ChronoDot v2.1 |
| Type | Temperature-compensated crystal oscillator (TCXO) |
| Accuracy | ±2ppm (±1 minute/year) |
| Interface | I2C (address 0x68) |
| **Primary Battery** | **Tadiran TL-5903 Li-SOCl2 (20-40 year shelf life)** |
| Backup Battery | CR2032 (field-replaceable, spares in lid foam) |
| Operating Temp | -40°C to +85°C |
| Location | Deck underside near OPi GPIO |

### 7.2 RTC Battery: Tadiran TL-5903 Li-SOCl2

| Parameter | Specification |
|-----------|---------------|
| Model | Tadiran TL-5903/S |
| Chemistry | Lithium Thionyl Chloride (Li-SOCl2) |
| Voltage | 3.6V nominal |
| Capacity | 2.4Ah |
| **Shelf Life** | **20-40 years (manufacturer spec)** |
| Self-Discharge | <1% per year |
| Temperature Range | -55°C to +85°C |
| Form Factor | AA size (14.7mm × 50.5mm) |
| Price | ~$15 |

**Why Li-SOCl2 for Seed Vault:**
- CR2032: ~10 year shelf life (adequate for active use, insufficient for 50-year dormancy)
- Li-SOCl2: 20-40 year shelf life with <1%/year self-discharge
- Provides RTC timekeeping throughout decade+ dormancy period
- On wake, system knows approximate date/time without GPS fix

**Installation Note:** TL-5903 is AA-size, not coin cell. Requires external battery holder wired to DS3231 Vbat pin. CR2032 socket on module serves as field-replaceable backup. See 02-assembly-specification.md for wiring procedure.

### 7.3 GPS Time Sync

**Selected: VK-162 USB GPS Dongle**

| Parameter | Specification |
|-----------|---------------|
| Model | VK-162 (or equivalent G-Mouse style) |
| Chipset | u-blox 7 |
| Antenna | Integrated ceramic patch |
| Cold Start | <30 seconds (open sky) |
| Interface | USB (serial over USB, 9600 baud) |
| Purpose | Time recovery and precise sync |
| Storage | Lid foam |

**TIME_LOST Condition:** If RTC reports year < 2025 on boot, system enters TIME_LOST mode. Discovery boot sequence displays "KNOWLEDGE ARK RECOVERED" and runs integrity check. GPS sync or manual date entry required to exit TIME_LOST.

### 7.4 Backup Batteries

| Item | Quantity | Location | Purpose |
|------|----------|----------|--------|
| Tadiran TL-5903 (installed) | 1 | External holder, wired to DS3231 | Primary 40-year RTC |
| Tadiran TL-5903 (spare) | 1 | Lid foam | 40-year RTC backup |
| CR2032 (spares) | 2 | Lid foam, sealed | Field-replaceable RTC backup |

---

## 8. Bill of Materials

### 8.1 Core Components

| Item | MPN | Qty | Unit Price | Total | Supplier |
|------|-----|-----|------------|-------|----------|
| Orange Pi 5 Plus (32GB) | OPI5P-32GB | 1 | $150 | $150 | OrangePi |
| Swissbit X-76m 480GB SLC NVMe | SFPC480GV3AA4TO-I-OC-A26-STD | 2 | $800 | $1,600 | Mouser/Digi-Key |
| Swissbit S-56 128GB SLC SD | SFSD128GN3AA4TO-I-OG-A21-STD | 2 | $200 | $400 | Mouser/Digi-Key |
| Waveshare 10.3" E-ink Display | SKU 24085 | 1 | $200 | $200 | Waveshare |
| IT8951 Controller Board | SKU 17608 | 1 | $50 | $50 | Waveshare |
| Pelican 1557 Air Case (Orange) | 015570-0000-150 | 1 | $242 | $242 | Pelican |
| **Subtotal** | | | | **$2,642** |

### 8.2 Audio Components

| Item | MPN | Qty | Unit Price | Total |
|------|-----|-----|------------|-------|
| ReSpeaker USB Mic Array v2.0 | 107990055 | 1 | $70 | $70 |
| SparkFun TPA2016D2 Amplifier | BOB-11685 | 1 | $10 | $10 |
| 3W Speaker 8Ω Ø50mm | — | 1 | $5 | $5 |
| Ground Loop Isolator (3.5mm) | — | 1 | $8 | $8 |
| **Subtotal** | | | | **$93** |

### 8.3 Power System Components (Lazarus-Phoenix Architecture)

| Item | MPN | Qty | Unit Price | Total |
|------|-----|-----|------------|-------|
| HiNa 32140 Na-ion cells (10Ah, Polyanion) | — | 32 | $7 | $224 |
| JK BMS BD4A8S4P (8S active balancing) | BD4A8S4P | 1 | $45 | $45 |
| Genasun GVB-8-WP-Li-CV (custom 32V) | GVB-8-WP-Li-CV | 1 | $200 | $200 |
| Renogy 50W 12V Foldable Panel | — | 3 | $70 | $210 |
| Wide-Input DC-DC (12-32V → 5V @ 5A) | — | 1 | $30 | $30 |
| Analog Voltmeter 0-30V (moving-coil) | — | 1 | $12 | $12 |
| TL431 Precision Shunt Reference | TL431 | 2 | $0.50 | $1 |
| Panasonic ADW1124HLW Latching Relay | ADW1124HLW | 1 | $8 | $8 |
| 1N5408 Silicon Rectifier Diode | 1N5408 | 2 | $0.50 | $1 |
| 47Ω 25W Vitreous Enamel Resistor | — | 1 | $5 | $5 |
| DPST Toggle Switch 20A with Guard | — | 1 | $10 | $10 |
| 4-Way ATO Fuse Block | — | 1 | $8 | $8 |
| 15A Manual Reset Breaker (ATO) | — | 1 | $6 | $6 |
| ATO Fuses (10A red, 5A tan) | — | 1 set | $3 | $3 |
| NTC Thermistor | 5D-9 | 1 | $2 | $2 |
| KSD9700 Thermal Cutoff (85°C) | KSD9700-85 | 1 | $3 | $3 |
| USB-C Breakout Board (Adafruit) | #4090 | 1 | $3 | $3 |
| XT60 Panel Mount | — | 1 | $5 | $5 |
| XT60 Connectors (panel wiring) | — | 4 | $2 | $8 |
| Binding Posts (Red/Black pair) | — | 1 | $5 | $5 |
| Nickel Strip 0.15mm × 8mm (spot welding) | — | 1m | $5 | $5 |
| 3D Printed Cell Holders (32140 format) | — | 8 | $2 | $16 |
| 10AWG Silicone Wire (red) | — | 3m | $2/m | $6 |
| 10AWG Silicone Wire (black) | — | 3m | $2/m | $6 |
| 18AWG Hookup Wire (assorted) | — | 10m | $1/m | $10 |
| Ferrules Assortment | — | 1 kit | $10 | $10 |
| 6-Position Barrier Terminal Block 30A | — | 1 | $4 | $4 |
| 2A Slow-Blow Fuse (bootstrap circuit) | — | 2 | $0.50 | $1 |
| Tadiran TL-5903 Li-SOCl2 (RTC) | TL-5903/S | 2 | $15 | $30 |
| AA Battery Holder (for TL-5903) | — | 1 | $2 | $2 |
| Conformal Coating (Parylene C kit) | MG Chemicals 422B | 1 | $30 | $30 |
| Faraday Bags (for cold spares) | — | 4 | $5 | $20 |
| **Subtotal** | | | | **$929** |

### 8.4 Control Components

| Item | MPN | Qty | Unit Price | Total |
|------|-----|-----|------------|-------|
| C&K AP Series IP67 Pushbutton (nav) | AP4D200TZBE | 5 | $6 | $30 |
| C&K AP Series IP67 Pushbutton (func) | AP4D200TZBE | 3 | $6 | $18 |
| C&K AP Caps Black (nav, function) | 481D02000 | 8 | $1 | $8 |
| C&K AP Caps Red (Power) | 481D03000 | 1 | $1 | $1 |
| Adafruit INA219 STEMMA QT | #904 | 1 | $10 | $10 |
| OLED Display 1.3" I2C SSD1306 128×64 | — | 1 | $10 | $10 |
| DS3231 RTC Module (Adafruit #3013) | #3013 | 1 | $13 | $13 |
| USB GPS Dongle (u-blox 7) | VK-162 | 1 | $15 | $15 |
| TVS Diode Array PESD5V0S4UF (4-ch) | PESD5V0S4UF | 2 | $1.50 | $3 |
| **Subtotal** | | | | **$108** |

### 8.5 Enclosure & Mechanical

| Item | MPN | Qty | Unit Price | Total |
|------|-----|-----|------------|-------|
| Aluminum Heatsink (OPi 5+ official) | OP5PLUS-HEATSINK | 1 | $12 | $12 |
| Pyrolytic Graphite Thermal Pad 0.1mm (30×30mm) | — | 2 | $10 | $20 |
| Mission Darkness TitanRF Faraday Fabric | TitanRF | 2 m² | $15/m² | $30 |
| 3M 1181 Conductive Tape | 1181 | 1 roll | $20 | $20 |
| 6061-T6 Aluminum Sheet 2mm (12"×18") | — | 1 | $28 | $28 |
| Polycarbonate Sheet (storm window) | — | 1 | $15 | $15 |
| Kaizen Foam Sheet 57mm (PE closed-cell) | — | 1 | $20 | $20 |
| Kaizen Foam Sheet 30mm (lid layer) | — | 1 | $12 | $12 |
| Closed-Cell PE Foam 10mm | — | 1 | $8 | $8 |
| Dense Foam Blocks (lateral constraint) | — | 1 pack | $8 | $8 |
| PVC Schedule 40 Pipe 1" (for posts) | — | 1 | $5 | $5 |
| Tinned Copper Braid 10mm (grounding straps) | — | 1m | $8 | $8 |
| M4 Ring Terminals (crimp) | — | 8 | $0.25 | $2 |
| M4 Star Washers (SS) | — | 4 | $0.10 | $1 |
| Waterproof Cable Glands | — | 4 | $2 | $8 |
| Indicating Silica Gel (1 lb) | — | 1 | $12 | $12 |
| Mesh Desiccant Pouches | — | 3 | $2 | $6 |
| Mylar Resealable Bag (Pelican-sized) | — | 1 | $15 | $15 |
| Neoprene Keyboard Sleeve | — | 1 | $10 | $10 |
| **Subtotal** | | | | **$240** |

### 8.6 Cables & Connectors

| Item | MPN | Qty | Unit Price | Total |
|------|-----|-----|------------|-------|
| USB-A Panel Mount (straight) | — | 3 | $5 | $15 |
| Coiled USB Cable (500mm, display) | — | 1 | $6 | $6 |
| USB-C Cable (spare) | — | 2 | $5 | $10 |
| USB LED Gooseneck Light (5000-6500K) | — | 1 | $8 | $8 |
| Alligator Clip to Banana Plug Cable | — | 1 | $8 | $8 |
| Right-angle USB-A Cable (internal) | — | 2 | $4 | $8 |
| XT60 Y-Harness (3-way, for panels) | — | 1 | $8 | $8 |
| **Subtotal** | | | | **$63** |

### 8.7 Mounting Hardware

| Item | Specification | Qty | Unit Price | Total |
|------|---------------|-----|------------|-------|
| M2.5×11mm Standoff (M-F, brass) | OPi 5+ mounting | 8 | $0.50 | $4 |
| M2.5×6mm Pan Head Screw (SS) | OPi, OLED, RTC | 20 | $0.10 | $2 |
| M2.5 Hex Nut (SS) | General | 20 | $0.05 | $1 |
| M3×8mm Pan Head Screw (SS) | Amp, fuse block, DC-DC | 20 | $0.10 | $2 |
| M3 Hex Nut (SS) | General | 20 | $0.05 | $1 |
| M3×6mm Flat Washer (SS) | Under screw heads | 20 | $0.05 | $1 |
| M4×10mm Pan Head Screw (SS) | Deck faceplate, panel mounts | 8 | $0.15 | $1 |
| M4 Hex Nut (SS) | Deck faceplate | 8 | $0.08 | $1 |
| M4 Flat Washer (SS) | Deck faceplate | 16 | $0.08 | $1 |
| M2×3mm Screw (M.2 SSD) | NVMe retention | 4 | $0.25 | $1 |
| Nylon Spacer Assortment | PCB standoffs | 1 kit | $6 | $6 |
| **Subtotal** | | | | **$21** |

### 8.8 Spares & Consumables

| Item | MPN | Qty | Unit Price | Total |
|------|-----|-----|------------|-------|
| ATO Fuses (10A, 5A assortment) | — | 10 | $0.50 | $5 |
| CR2032 Batteries | — | 2 | $1 | $2 |
| Wago 221-412 (2-port) | 221-412 | 10 | $0.80 | $8 |
| Wago 221-413 (3-port) | 221-413 | 5 | $1 | $5 |
| Wago 221-415 (5-port) | 221-415 | 5 | $1.20 | $6 |
| Heat Shrink Assortment | — | 1 kit | $8 | $8 |
| Spiral Wrap (1/4" and 1/2") | — | 2 packs | $4 | $8 |
| Dielectric Grease | — | 1 tube | $5 | $5 |
| Desiccant (silica gel, extra) | — | 2 packs | $5 | $10 |
| **Subtotal** | | | | **$57** |

### 8.9 Total Cost Summary

| Category | Total |
|----------|-------|
| Core Components (incl. SLC storage) | $2,642 |
| Audio Components | $93 |
| Power System (Lazarus-Phoenix + Vault) | $929 |
| Control Components | $108 |
| Enclosure & Mechanical | $240 |
| Cables & Connectors | $63 |
| Mounting Hardware | $21 |
| Spares & Consumables | $57 |
| **Grand Total** | **$4,153** |
| + 15% Contingency | $623 |
| **Budget Total** | **$4,776** |

**Vault Edition BOM Impact:**

| Change | Prior | Current | Delta |
|--------|-------|---------|-------|
| NVMe (Transcend TLC → Swissbit SLC) | $280 | $1,600 | +$1,320 |
| SD (Kingston pSLC → Swissbit SLC) | $15 | $400 | +$385 |
| RTC Battery (CR2032 → Li-SOCl2) | $4 | $32 | +$28 |
| Conformal Coating | $0 | $30 | +$30 |
| Faraday Bags | $0 | $20 | +$20 |
| **Storage/Protection Delta** | | | **+$1,783** |

**Philosophy:** Storage subsystem is exempt from strict cost ceiling. 50-year passive survivability is non-negotiable for seed vault mission.

### 8.10 Spare Parts Inventory (In Case)

For 50-year seed vault operation, stored in lid foam:

| Item | Qty | Location | Rationale |
|------|-----|----------|----------|
| Swissbit X-76m 480GB SLC NVMe | 1 | Faraday bag, lid foam | Pre-loaded corpus clone, 50-year retention |
| Swissbit S-56 128GB SLC SD | 1 | Faraday bag, lid foam | Pre-loaded archive clone, 50-year retention |
| Tadiran TL-5903 Li-SOCl2 | 1 | Lid foam | 40-year RTC battery spare |
| CR2032 Batteries | 2 | Lid foam, sealed | Field-replaceable RTC backup |
| ATO Fuses (10A, 5A) | 10 | Lid foam | Consumable |
| USB-C Cable | 1 | Lid foam | Power cable wear |
| Wago 221 Assortment | 20 | Lid foam | Field wiring repairs |
| Indicating Silica Gel (spare) | 2 bags | Lid foam | Desiccant replacement |
| GPS Dongle | 1 | Lid foam | Time recovery after TIME_LOST |
| USB LED Gooseneck Light | 1 | Lid foam | Night operations |
| Alligator Whip Cable | 1 | Lid foam | Power scavenging |
| Dielectric Grease | 1 tube | Lid foam | Connector maintenance |
| 1N5408 Diode (spare) | 1 | Lid foam | Bootstrap circuit repair |
| TL431 (spare) | 1 | Lid foam | Load disconnect repair |

**Cold Spare Preparation:** Both SLC drives (NVMe and SD) must be pre-loaded with complete corpus before sealing in Faraday bags. Include desiccant packet in each bag. Label with date sealed and SHA-256 manifest hash.

---

## 9. Assembly Reference

See **02-assembly-specification.md** for:
- Required tools (including spot welder for Na-ion pack)
- Workspace setup
- Step-by-step assembly procedures
- Battery pack assembly procedure
- Wiring diagrams
- Validation tests

---

## 10. Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0.0 | Dec 2025 | Initial specification (RPi CM5 based) |
| 2.0.0 | Dec 2025 | Complete rewrite: OPi 5+, 10.3" display, corrected storage/power/protection chain |
| 2.1.0-2.16.0 | Dec 2025 | Incremental fixes: partition math, INA219, scavenger input, deck structure, LVD module, battery orientation, negative bus, solar panel changes |
| 2.27.0 | Dec 2025 | Pre-Lazarus final: All 120 issues resolved, BUILD phase ready |
| 3.0.0 | Dec 2025 | MAJOR: Lazarus-Phoenix power architecture. Na-ion 8S4P, Genasun boost MPPT, passive bootstrap, analog voltmeter, TL431 load disconnect. Budget $2,712. |
| **4.0.0** | **Dec 2025** | **VAULT EDITION: Storage architecture revision for 50-year passive survivability.** Replaced Transcend TLC NVMe with Swissbit X-76m SLC NVMe (×2). Replaced Kingston pSLC SD with Swissbit S-56 SLC SD (×2). Added Tadiran TL-5903 Li-SOCl2 RTC battery (40-year). Added conformal coating (Parylene C). Added Faraday bags for cold spares. New storage philosophy: physics-based retention (SLC NAND) vs. active maintenance. New features: ark-seal archival mode, discovery boot sequence, integrity manifests. Budget increased $2,712 → $4,776 (+$2,064). Philosophy: Storage exempt from cost ceiling for seed vault mission. |

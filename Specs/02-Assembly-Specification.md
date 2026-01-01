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

## 1. Required Tools

### 1.1 Essential Tools

| Tool | Specification | Purpose |
|------|---------------|---------|
| Screwdriver set | Phillips #0, #1, #2; Flat 3mm, 5mm | Component mounting |
| Hex key set | 2mm, 2.5mm, 3mm, 4mm | Enclosure hardware |
| Wire strippers | 10-22 AWG | Cable preparation |
| Crimping tool | Ferrule crimper | Ferrule connector assembly |
| Soldering iron | 40W, temperature controlled | Permanent connections |
| Multimeter | DC voltage, continuity, resistance | Testing |
| Bench power supply | Adjustable 0-30V, 5A (e.g., NICE-POWER) | Load disconnect/INA219 testing, Na-ion voltage simulation |
| Spot welder | kWeld or similar, 0.15mm nickel strip | Na-ion cell pack assembly |
| Heat gun | 150-400°C adjustable | Heat shrink |
| Drill | Variable speed, 10mm chuck | Enclosure mods |
| Rotary tool (Dremel) | With cutting discs | Fine panel work |
| Flush cutters | For wire and zip ties | Trimming |

### 1.2 Drill Bits & Accessories

| Item | Size | Purpose |
|------|------|---------|
| Step drill | 4-22mm | Panel holes for buttons, connectors |
| Hole saw | 50mm | Speaker grill opening |
| Forstner bit | 25mm | OLED rectangular start hole (optional) |
| Center punch | — | Hole marking |
| Deburring tool | — | Clean hole edges |
| Masking tape | — | Drill guides, surface protection |
| File set | Flat, round, half-round | Fine shaping |

### 1.3 Consumables

| Item | Quantity | Purpose |
|------|----------|---------|
| Isopropyl alcohol | 500mL | Cleaning, prep for adhesives |
| Pyrolytic graphite pad | 60×60mm | OPi 5+ thermal interface |
| Thermal pads (various) | 5 sheets | Secondary thermal paths |
| Dielectric grease | 1 tube | Connector protection |
| Electrical tape | 2 rolls | Insulation |
| Cable ties (assorted) | 100 pack | Cable management |
| Heat shrink (assorted) | 100 pcs | Connection protection |
| 3M VHB tape | 1 roll | Storm window corners, component mounting (not for batteries) |
| Kapton tape | 1 roll | High-temp insulation |
| Ferrules (assorted) | 200 pcs | Screw terminal connections |
| **Conformal Coating (MG Chemicals 422B)** | **1 kit** | **Humidity/corrosion protection for 50-year survivability** |
| **UV inspection light** | **1** | **Conformal coating verification** |
| **Faraday bags (storage)** | **4** | **Cold spare NVMe/SD storage** |

### 1.4 Faraday Shielding Materials

| Item | Quantity | Purpose |
|------|----------|---------|
| Ripstop silver Faraday fabric | 2 m² | Case interior lining |
| 3M 1181 conductive tape | 1 roll (25mm × 16m) | Seam sealing |
| Conductive adhesive | 1 tube | Corners, overlaps |

### 1.5 Acceptable Substitutions

| Primary | Acceptable Substitute |
|---------|----------------------|
| Ferrule crimper | Quality needle-nose pliers (not ideal) |
| Heat gun | Hair dryer on high (slower) |
| Step drill | Series of standard bits |
| Rotary tool | Careful work with file |

---

## 2. Workspace Setup

### 2.1 ESD Precautions

**Required:**
- ESD wrist strap connected to ground
- ESD mat or anti-static work surface
- Components in anti-static bags until installation
- Grounding point for all work

**Best Practices:**
- Touch grounded metal before handling PCBs
- Keep components away from synthetic materials
- Humidity >40% reduces static risk
- Ground OPi 5+ heatsink before handling

### 2.2 Workspace Requirements

| Requirement | Minimum | Recommended |
|-------------|---------|-------------|
| Work surface | 90 × 120 cm | 120 × 150 cm |
| Lighting | 500 lux | 1000 lux + task light |
| Ventilation | Open window | Fume extraction for soldering |
| Power outlets | 4 | 6 |
| Temperature | 15-30°C | 20-25°C |

### 2.3 Organization

Prepare labeled containers for:
- Enclosure hardware (standoffs, screws for components)
- Electronic fasteners (M3, M2.5 screws, standoffs)
- Connectors (XT60, Wago 221, ferrules)
- Cable management (ties, clips, spiral wrap)
- Protection components (fuses, breaker, load disconnect relay, KSD9700, TL431, resistor divider R1/R2, pulse capacitor)
- Foam materials (posts pads, lateral constraint blocks)
- Spares (for lid foam pocket)

---

## 3. Component Prep

### 3.1 Pre-Assembly Testing

**Test each component before installation:**

| Component | Test Procedure | Expected Result |
|-----------|----------------|-----------------|
| Orange Pi 5+ | Boot from SD card with test image | LED activity, HDMI output |
| Swissbit X-76m SLC NVMe (×2) | Connect via USB adapter, verify capacity | 480GB capacity recognized, SMART healthy |
| Swissbit S-56 SLC SD (×2) | Insert in reader, verify capacity | 128GB capacity recognized |
| E-ink Display + IT8951 | Connect via USB, run test | All pixels refresh, no dead pixels |
| OLED Status Display | Connect to Arduino/test MCU | Displays test pattern |
| Genasun GVB-8-WP | Apply 18V via bench supply to PV input, 0V load | Controller powers from PV, attempts charging |
| HiNa 32140 Na-ion Cells (x32) | Measure individual cell voltage | 3.0-4.0V each (storage charge) |
| JK BMS BD4A8S4P | Connect to test cells, check Bluetooth app | BMS communicates, shows cell voltages |
| Load Disconnect (TL431 + relay) | Apply 15V, then 22V via bench supply | Relay opens at <18V, closes at >20V |
| ReSpeaker Mic Array | Connect to PC, record audio | Clear capture, LED ring lights |
| TPA2016D2 + Speaker | 3.5mm audio in, play test tone | Clear audio, no distortion |
| DS3231 RTC | Connect to test MCU, read time | Time persists after power cycle |
| INA219 Power Monitor | Connect to test MCU, apply 24V via bench supply | Voltage reading ±0.1V of multimeter reference |
| Nav buttons (×5) | Continuity test each button | Momentary closure, tactile click, no bounce |
| Buttons (×3) | Continuity test | Momentary closure, tactile click |
| Wide-Input DC-DC | Apply 24V in, measure out | 5.0V ± 0.1V at output |
| Analog Voltmeter 0-30V | Apply 12V, 24V via bench supply | Needle deflects to correct reading |

### 3.2 Firmware & Configuration

**Before assembly:**

1. **Orange Pi 5+ Boot Configuration**

   The OPi 5+ uses SPI flash to store U-Boot, which controls boot device priority. For this project:
   - **Primary boot:** NVMe (Daily Driver OS + library)
   - **Fallback boot:** eMMC (Lifeboat OS recovery)
   - **SD card:** Disabled in normal operation (prevents accidental boot conflicts)

   **Step 1: Flash U-Boot to SPI (if not already present)**
   
   Boot from SD card with Armbian or Orange Pi Debian, then:
   ```bash
   # Check if SPI already has bootloader
   sudo dd if=/dev/mtdblock0 bs=1 count=16 2>/dev/null | hexdump -C
   # If all 0xFF, SPI is empty and needs flashing
   
   # Use armbian-config or orangepi-config
   sudo armbian-config
   # Navigate: System → Install → Install/Update the bootloader on SPI Flash
   ```

   **Step 2: Prepare NVMe (Daily Driver)**
   
   - Flash Armbian or Orange Pi Debian directly to NVMe (see §3.3)
   - Verify NVMe boots successfully with SD card removed

   **Step 3: Prepare eMMC (Lifeboat OS)**
   
   Boot from NVMe, then install minimal recovery OS to eMMC:
   ```bash
   # Option A: Use armbian-config
   sudo armbian-config
   # Navigate: System → Install → Install to/update eMMC
   # Select "Install" (not "Boot from eMMC")
   
   # Option B: Manual dd (use minimal Armbian CLI image)
   # Download armbian minimal image first
   sudo dd if=Armbian_minimal.img of=/dev/mmcblk0 bs=4M status=progress
   sync
   ```

   **Step 4: Configure U-Boot Boot Order**
   
   U-Boot's default scan order is typically: SD → eMMC → NVMe. We need NVMe first:
   ```bash
   # Edit U-Boot environment (from running NVMe system)
   sudo fw_setenv boot_targets "nvme0 mmc1 mmc0"
   # nvme0 = NVMe (primary)
   # mmc1 = eMMC (fallback/Lifeboat)
   # mmc0 = SD card (emergency only, usually not inserted)
   
   # Verify
   sudo fw_printenv boot_targets
   ```

   **Step 5: Verify Boot Fallback**
   
   1. Power off, remove NVMe temporarily
   2. Power on — system should boot from eMMC (Lifeboat)
   3. Verify Lifeboat boots to login prompt
   4. Power off, reinstall NVMe
   5. Power on — system should boot from NVMe (Daily Driver)

   **Boot Priority Summary:**

   | Priority | Device | Contents | When Used |
   |----------|--------|----------|------------|
   | 1 | NVMe | Daily Driver OS + library | Normal operation |
   | 2 | eMMC | Lifeboat OS (minimal recovery) | NVMe failure/corruption |
   | 3 | SD card | Emergency recovery (not normally inserted) | Manual recovery only |

6. **Genasun GVB-8-WP MPPT Configuration**
   
   The Genasun GVB-8-WP is a fixed-voltage boost MPPT controller. Unlike Victron, it has no Bluetooth, no app, and no user-adjustable settings — voltage thresholds are factory-programmed.
   
   **Custom Programming (ordered from Genasun):**
   - Input: Solar 18V nominal (3× 6V panels in series)
   - Output absorption voltage: 32.0V (Na-ion 8S × 4.0V/cell)
   - Output float voltage: 26.4V (Na-ion 8S × 3.3V/cell)
   
   **Verification Procedure (bench test before installation):**
   1. Apply 18V DC to PV+ and PV- terminals (lab power supply)
   2. Leave BAT+ and BAT- open (simulates 0V battery)
   3. Controller should attempt to output programmed absorption voltage
   4. Measure BAT+ to BAT- with multimeter: should show ~32V open-circuit
   5. If incorrect, return to Genasun for reprogramming
   
   **0V Wake Capability:**
   - Genasun boost topology supports charging from 0V battery state
   - Combined with passive bootstrap circuit (§6.8), enables decade dormancy recovery
   - No minimum battery voltage required to begin charging
   
   **Cross-reference:** Hardware Specification §4.3 (Genasun specs), §4.5 (Passive Bootstrap)

3. **DS3231 RTC with Li-SOCl2 Primary**
   - Wire external Tadiran TL-5903 holder to Vbat pin (40-year primary)
   - Install CR2032 in module socket (field-replaceable backup)
   - Set initial time via I2C

### 3.3 NVMe Preparation (Swissbit X-76m SLC)

**Storage Philosophy:** The Vault Edition uses SLC NAND for 50-year passive survivability. Unlike TLC drives requiring quarterly maintenance, SLC physics provides data retention without active intervention.

1. Connect Swissbit X-76m to OPi 5+ or test PC via USB adapter
2. Verify SLC drive identity:
   ```bash
   # Confirm manufacturer and model
   sudo smartctl -i /dev/nvme0n1 | grep -E "Model|Serial|Firmware"
   # Should show: Swissbit SFPC480GV3AA4TO-I-OC-A26-STD
   ```
3. Create partition layout per Hardware Spec §3.6:
   - p1: /boot (512MB, FAT32)
   - p2: / (20GB, EXT4)
   - p3: /var (10GB, EXT4)
   - p4: /recovery (35GB, EXT4)
   - p5: /data (381GB, EXT4)
3. **Verify drive health** (choose one method):
   
   **Option A: SMART Extended Self-Test (Recommended for NVMe)**
   ```bash
   # Run extended self-test (~10-30 minutes for 512GB NVMe)
   sudo smartctl -t long /dev/nvme0n1
   # Check results after completion
   sudo smartctl -a /dev/nvme0n1
   ```
   
   **Option B: Full Write Test with dd (~15-30 minutes)**
   ```bash
   # Write zeros to entire drive (DESTRUCTIVE)
   sudo dd if=/dev/zero of=/dev/nvme0n1 bs=4M status=progress
   # Then verify readability
   sudo dd if=/dev/nvme0n1 of=/dev/null bs=4M status=progress
   ```
   
   **Note on badblocks:** Traditional `badblocks -wsv` is not recommended for SSDs. It uses small block sizes that cause excessive write amplification and wear. SSDs handle bad block remapping internally via firmware. Use SMART self-tests instead.
4. Verify SMART health: `smartctl -a /dev/nvme0n1`
5. Record serial number for tracking
6. Install OS, applications, LLM model, and knowledge library
7. Verify full system boot and library access
8. Proceed to §3.3.1 for cold spare imaging

### 3.3.1 Cold Spare NVMe Imaging (Faraday Bag Storage)

The cold spare SLC NVMe must contain a **full system clone** identical to the primary drive. Unlike the /recovery partition (which restores OS only), the cold spare includes the complete knowledge library—enabling immediate operation after a drive swap without requiring library rebuild.

**Vault Edition Storage:** Cold spares are sealed in Faraday bags with desiccant for 50-year passive retention. SLC NAND physics provides the retention margin; Faraday bag provides EMP protection.

**Initial Imaging Procedure:**

1. **Verify primary system is complete:**
   - OS boots successfully
   - All applications functional
   - Knowledge library accessible (test: Survival Menu → any topic)
   - Run diagnostics (System → Diagnostics)

2. **Create source image:**
   ```bash
   # Option A: dd with compression (simpler)
   sudo dd if=/dev/nvme0n1 bs=4M status=progress | gzip > /tmp/ark-factory.img.gz
   
   # Option B: Clonezilla (faster, smarter compression)
   ocs-sr -q2 -c -j2 -z1p -i 4096 -sfsck -senc -p poweroff savedisk ark-factory nvme0n1
   ```

3. **Write to cold spare:**
   - Connect cold spare NVMe via USB 3.0 adapter
   - Verify correct device: `lsblk` (confirm 512GB capacity, note device name)
   - **⚠️ Triple-check device name before proceeding—dd destroys data**
   ```bash
   # Option A: Restore dd image
   gunzip -c /tmp/ark-factory.img.gz | sudo dd of=/dev/sdX bs=4M status=progress
   sync
   
   # Option B: Restore Clonezilla image
   ocs-sr -g auto -e1 auto -e2 -r -j2 -c -scr -p poweroff restoredisk ark-factory sdX
   ```

4. **Verify cold spare boots:**
   - Power off OPi 5+
   - Swap drives: remove primary from M.2 slot, install cold spare
   - Boot and verify:
     - System reaches login prompt
     - /data partition mounts
     - Library accessible (Survival Menu → any topic)
   - Run `smartctl -a /dev/nvme0n1` — confirm no errors
   - Power off

5. **Restore primary and seal spare in Faraday bag:**
   - Swap primary NVMe back into M.2 slot
   - Boot primary to confirm normal operation
   - Generate SHA-256 manifest of cold spare contents:
     ```bash
     # Record manifest hash for integrity verification
     sudo sha256sum /dev/sdX > cold-spare-manifest.txt
     ```
   - Label Faraday bag:
     - Serial number
     - `IMAGED: YYYY-MM-DD`
     - `SHA256: [first 16 chars of hash]`
   - Insert desiccant packet into bag
   - Seal cold spare NVMe in Faraday bag
   - Store in lid foam pocket

**Image Contents:**

| Partition | Contents | Size |
|-----------|----------|------|
| /boot | Bootloader, kernel | 512 MB |
| / | OS, applications, LLM model | 20 GB |
| /var | Empty (logs cleared before imaging) | 10 GB |
| /recovery | Factory Reset Image + integrity manifests | 35 GB |
| /data | Full library + embeddings | 381 GB |

**Time Estimate:** 45-90 minutes (depending on data volume and USB 3.0 speed)

**Periodic Updates:** See appendix-c-operations-manual.md §6.5 for annual cold spare update procedure.

### 3.3.2 Archival SD Preparation (Swissbit S-56 SLC)

The archival SD card contains a critical corpus subset mounted read-only at /archive. This provides independent backup on a different storage controller.

**Preparation Procedure:**

1. **Verify SLC SD identity:**
   - Insert Swissbit S-56 into SD reader
   - Confirm capacity: 128GB (119 GiB formatted)
   - Check label for Swissbit S-56 part number: SFSD128GN3AA4TO-I-OG-A21-STD

2. **Format and populate:**
   ```bash
   # Create single EXT4 partition
   sudo parted /dev/mmcblk1 mklabel gpt
   sudo parted /dev/mmcblk1 mkpart archive ext4 0% 100%
   sudo mkfs.ext4 -L ARCHIVE /dev/mmcblk1p1
   
   # Mount and copy critical corpus subset
   sudo mount /dev/mmcblk1p1 /mnt/archive
   sudo cp -r /data/corpus/medical /mnt/archive/
   sudo cp -r /data/corpus/repair /mnt/archive/
   sudo cp -r /data/corpus/agriculture /mnt/archive/
   sudo cp -r /data/corpus/reference /mnt/archive/
   sudo umount /mnt/archive
   ```

3. **Generate integrity manifest:**
   ```bash
   sudo mount -o ro /dev/mmcblk1p1 /mnt/archive
   find /mnt/archive -type f -exec sha256sum {} \; > archive-manifest.txt
   sudo cp archive-manifest.txt /mnt/archive/MANIFEST.sha256
   sudo umount /mnt/archive
   ```

4. **Prepare cold spare SD:**
   - Repeat steps 1-3 for second Swissbit S-56
   - Seal in Faraday bag with desiccant
   - Label: Serial, date, SHA256 of manifest
   - Store in lid foam pocket

**Cross-reference:** Hardware Specification §3.4 (Archival Storage specs)

### 3.3.3 Conformal Coating Procedure (Vault Edition)

Conformal coating provides humidity and corrosion protection for 50-year passive survivability. Applied to critical PCBs before final assembly.

#### 3.3.3.1 Purpose & Materials

**Why Conformal Coating:**
- Humidity ingress causes corrosion over decade+ timescales
- Salt air, condensation, and outgassing can degrade solder joints
- Coating creates moisture barrier extending component life

**Selected Coating:** MG Chemicals 422B Silicone Conformal Coating
- Type: Silicone-based (flexible, wide temp range)
- Cure time: 24 hours at room temperature
- Temperature range: -65°C to +200°C
- Moisture resistance: Excellent
- UV tracer: Yes (fluoresces under UV for inspection)

#### 3.3.3.2 Components to Coat

| Component | Coat? | Notes |
|-----------|-------|-------|
| OPi 5+ board (underside) | ✅ YES | Avoid M.2 slot, SD slot, all connectors |
| DC-DC converter | ✅ YES | Avoid adjustment pots if present |
| Protection chain PCB | ✅ YES | Full coverage acceptable |
| TL431 load disconnect PCB | ✅ YES | Full coverage acceptable |
| INA219 module | ✅ YES | Avoid I2C connector |
| DS3231 RTC module | ✅ YES | Avoid battery socket, I2C connector |
| OLED display module | ❌ NO | Display sensitive to coating |
| ReSpeaker mic array | ❌ NO | Microphone membranes sensitive |
| Heatsink | ❌ NO | Reduces thermal conductivity |
| Connectors (all) | ❌ NO | Prevents electrical contact |
| M.2 slot | ❌ NO | NVMe must remain removable |
| SD slot | ❌ NO | SD must remain removable |
| USB ports | ❌ NO | Prevents connection |

#### 3.3.3.3 Masking Procedure

**Before coating, mask these areas with Kapton tape:**

1. **OPi 5+ board:**
   - M.2 NVMe slot (entire slot area)
   - SD card slot
   - All USB ports (4×)
   - GPIO header pins
   - USB-C power input
   - 3.5mm audio jack
   - Ethernet ports (2×)
   - HDMI ports (2×)
   - Heatsink mounting area

2. **Other modules:**
   - I2C connectors on INA219, DS3231
   - Any adjustment potentiometers
   - Test points (if access needed later)

#### 3.3.3.4 Coating Application

**Workspace Requirements:**
- Well-ventilated area (fume extraction recommended)
- Temperature: 15-30°C
- Humidity: <70% RH
- Clean, dust-free work surface

**Procedure:**

1. **Clean surfaces:**
   - Wipe all PCBs with isopropyl alcohol (99%)
   - Allow to dry completely (5-10 minutes)
   - Inspect for flux residue, fingerprints, debris

2. **Apply masking:**
   - Apply Kapton tape to all exclusion areas (see §3.3.3.3)
   - Press edges firmly to prevent coating seepage
   - Double-check all connectors are masked

3. **Apply coating:**
   - Shake can thoroughly (2 minutes)
   - Hold 15-20cm from surface
   - Apply thin, even coat (2-3 passes)
   - Coat both sides of through-hole components
   - Ensure solder joints are covered
   - Avoid pooling or drips

4. **Cure:**
   - Allow 24 hours cure time at room temperature
   - Do not handle or assemble until fully cured
   - Coating should be dry to touch after 30 minutes

5. **Remove masking:**
   - Carefully peel Kapton tape
   - Inspect edges for coating creep
   - Trim any coating that entered masked areas with exacto knife

#### 3.3.3.5 UV Inspection

**Verification Procedure:**

1. Darken workspace
2. Illuminate coated PCB with UV light (365-395nm)
3. Coating should fluoresce bright blue/white
4. Check for:
   - ✅ Complete coverage of solder joints
   - ✅ No missed areas (dark spots)
   - ✅ No pinholes or thin spots
   - ✅ Masked areas remain uncoated
5. Touch up any missed spots with brush application
6. Re-cure 24 hours if touch-up applied

**Pass Criteria:**
- 100% coverage of target components
- No coating on masked exclusion areas
- No drips, pooling, or thick spots
- Uniform fluorescence under UV

**Cross-reference:** Hardware Specification §8.3 (BOM: MG Chemicals 422B)

### 3.4 Cable Preparation

Pre-cut, strip, and crimp all cables per the tables below. Total cable count: 60 segments.

#### 3.4.1 Ferrule Size Reference

| AWG | Ferrule Color | Ferrule Size | Strip Length |
|-----|---------------|--------------|--------------|
| 10 | Yellow | 6.0mm² | 12mm |
| 12 | Yellow | 4.0mm² | 10mm |
| 14 | Blue | 2.5mm² | 10mm |
| 16 | Red | 1.5mm² | 8mm |
| 18 | Black | 1.0mm² | 8mm |
| 20 | Black | 0.75mm² | 8mm |
| 22 | Orange | 0.5mm² | 8mm |
| 24 | White | 0.34mm² | 6mm |

**Note:** Colors follow DIN 46228 standard. Ferrule length typically 8-12mm; use longer ferrules for high-vibration connections.

#### 3.4.2 Power Cables — 24V Main Path (Lazarus-Phoenix)

Pre-cut cables for the main protection chain. For complete routing details, node references, and termination specifications, see **§6.8.2 Wire Schedule** (authoritative source).

**Main Protection Chain (W01-W10):**

| Seg | Description | AWG | Color | Length | Terminations |
|-----|-------------|-----|-------|--------|---------------|
| W01 | Na-ion Pack+ to Battery bus | 10 | Red | 150mm | XT60 ↔ Ring 10mm |
| W02 | Na-ion Pack- to Negative bus | 10 | Black | 150mm | XT60 ↔ Ferrule |
| W03 | Battery bus to Load disconnect | 10 | Red | 100mm | Ring 10mm ↔ Ferrule |
| W04 | Load disconnect to Kill switch | 10 | Red | 80mm | Ferrule ↔ Spade 6.3mm |
| W05 | Kill switch to NTC thermistor | 10 | Red | 80mm | Spade 6.3mm ↔ Solder |
| W06 | NTC to Fuse block | 10 | Red | 100mm | Solder ↔ Ferrule |
| W07 | 15A breaker to INA219 Vin+ | 12 | Red | 100mm | Ferrule ↔ Screw term |
| W08 | INA219 Vin- to KSD9700 | 12 | Red | 80mm | Screw term ↔ Solder |
| W09 | KSD9700 to DC-DC +Vin | 12 | Red | 100mm | Solder ↔ Ferrule |
| W10 | DC-DC -Vin to Negative bus | 12 | Black | 100mm | Ferrule ↔ Ferrule |

**Load Disconnect Circuit (W11-W17):**

| Seg | Description | AWG | Color | Length | Terminations |
|-----|-------------|-----|-------|--------|---------------|
| W11 | Battery bus to R1 (70kΩ) | 18 | Red | 50mm | Ferrule ↔ Solder |
| W12 | R1/R2 junction to TL431 Ref | 18 | Red | 30mm | Solder ↔ Solder |
| W13 | R2 (10kΩ) to Negative bus | 18 | Black | 50mm | Solder ↔ Ferrule |
| W14 | TL431 Cathode to Relay SET | 18 | Red | 50mm | Solder ↔ Solder |
| W15 | TL431 Anode to Negative bus | 18 | Black | 50mm | Solder ↔ Ferrule |
| W16 | Relay COM to Battery bus | 10 | Red | 80mm | Spade 4.8mm ↔ Ring 10mm |
| W17 | Relay NO to Load disconnect out | 10 | Red | 80mm | Spade 4.8mm ↔ Ferrule |

**Analog Voltmeter (W18-W19):**

| Seg | Description | AWG | Color | Length | Terminations |
|-----|-------------|-----|-------|--------|---------------|
| W18 | Battery bus to Voltmeter + | 18 | Red | 200mm | Ring 10mm ↔ Spade 2.8mm |
| W19 | Negative bus to Voltmeter - | 18 | Black | 200mm | Ferrule ↔ Spade 2.8mm |

**5V Load Distribution (W20-W23):**

| Seg | Description | AWG | Color | Length | Terminations |
|-----|-------------|-----|-------|--------|---------------|
| W20 | DC-DC +Vout to USB-C breakout | 18 | Orange | 150mm | Ferrule ↔ Screw term |
| W21 | DC-DC -Vout to USB-C breakout | 18 | Brown | 150mm | Ferrule ↔ Screw term |
| W22 | DC-DC +Vout to TPA2016 VIN | 18 | Orange | 200mm | Ferrule ↔ Solder |
| W23 | DC-DC -Vout to TPA2016 GND | 18 | Brown | 200mm | Ferrule ↔ Solder |

**Solar Input Path (W24-W27):**

| Seg | Description | AWG | Color | Length | Terminations |
|-----|-------------|-----|-------|--------|---------------|
| W24 | XT60+ (panel) to Genasun PV+ | 12 | Red/White | 300mm | XT60 ↔ Ferrule |
| W25 | XT60- (panel) to Genasun PV- | 12 | Blk/White | 300mm | XT60 ↔ Ferrule |
| W26 | Genasun BAT+ to Battery bus | 10 | Red | 200mm | Ferrule ↔ Ring 10mm |
| W27 | Genasun BAT- to Negative bus | 10 | Black | 200mm | Ferrule ↔ Ferrule |

**Passive Bootstrap (W28-W30):**

| Seg | Description | AWG | Color | Length | Terminations |
|-----|-------------|-----|-------|--------|---------------|
| W28 | Genasun PV+ to 1N5408 Anode | 18 | Red/White | 50mm | Ferrule ↔ Solder |
| W29 | 1N5408 Cathode to 47Ω resistor | 18 | Red | 50mm | Solder ↔ Solder |
| W30 | 47Ω resistor to Battery bus | 18 | Red | 80mm | Solder ↔ Ring 10mm |

**Scavenger Input Path (W31-W34):**

| Seg | Description | AWG | Color | Length | Terminations |
|-----|-------------|-----|-------|--------|---------------|
| W31 | Binding post+ to 15A fuse (scav) | 12 | Red | 150mm | Banana stud ↔ Ferrule |
| W32 | 15A fuse out to Schottky anode | 12 | Red | 80mm | Ferrule ↔ Spade 6.3mm |
| W33 | Schottky cathode to Battery bus | 12 | Red | 100mm | Spade 6.3mm ↔ Ring 10mm |
| W34 | Binding post- to Negative bus | 12 | Black | 150mm | Banana stud ↔ Ferrule |

**XT60 Soldering Note:** XT60 connectors require soldering (no crimping option exists). Pre-tin both wire and connector cup, then reflow together. Use a heatsink clip on adjacent plastic during soldering to prevent melting.

**NTC Thermistor:** Pre-wired 100mm leads, 16 AWG. Solder inline per W05-W06 junction.

#### 3.4.3 I2C Bus Cables (W31-W42)

All I2C devices share a single bus (I2C1 on OPi 5+). Daisy-chain wiring from OPi → DS3231 → OLED → INA219.

| Seg | From | To | AWG | Color | Length | From Term | To Term |
|-----|------|-----|-----|-------|--------|-----------|----------|
| W31 | OPi Pin 3 (SDA) | DS3231 SDA | 22 | Green | 150mm | Dupont F | Dupont F |
| W32 | OPi Pin 5 (SCL) | DS3231 SCL | 22 | Yellow | 150mm | Dupont F | Dupont F |
| W33 | DS3231 SDA | OLED SDA | 22 | Green | 100mm | Dupont F | Dupont F |
| W34 | DS3231 SCL | OLED SCL | 22 | Yellow | 100mm | Dupont F | Dupont F |
| W35 | OLED SDA | INA219 SDA | 22 | Green | 150mm | Dupont F | STEMMA QT |
| W36 | OLED SCL | INA219 SCL | 22 | Yellow | 150mm | Dupont F | STEMMA QT |
| W37 | OPi 3.3V (Pin 1) | DS3231 VCC | 22 | Red | 150mm | Dupont F | Dupont F |
| W38 | DS3231 VCC | OLED VCC | 22 | Red | 100mm | Dupont F | Dupont F |
| W39 | OLED VCC | INA219 VCC | 22 | Red | 150mm | Dupont F | STEMMA QT |
| W40 | OPi GND (Pin 9) | DS3231 GND | 22 | Black | 150mm | Dupont F | Dupont F |
| W41 | DS3231 GND | OLED GND | 22 | Black | 100mm | Dupont F | Dupont F |
| W42 | OLED GND | INA219 GND | 22 | Black | 150mm | Dupont F | STEMMA QT |

**I2C Addresses:** DS3231=0x68, OLED=0x3C, INA219=0x40

**Wire gauge rationale:** 22AWG is selected for all I2C connections based on:
- Signal integrity: I2C operates at 100-400kHz; 22AWG provides adequate shielding and low capacitance for runs up to 150mm
- Current capacity: I2C pull-up current is <3mA; 22AWG rated for 920mA is vastly oversized (intentional for durability)
- Mechanical robustness: Thicker gauge resists fatigue from lid open/close cycles better than 24-28AWG alternatives
- Standardization: Matches signal wiring gauge throughout project, reducing inventory complexity

**Alternative:** Use pre-made STEMMA QT/Qwiic cables for INA219 segment if available.

#### 3.4.4 GPIO Button Cables (W43-W50)

All buttons use internal pull-up resistors (enabled in software). Active-low switching to ground.

| Seg | From | To | AWG | Color | Length | From Term | To Term |
|-----|------|-----|-----|-------|--------|-----------|----------|
| W43 | Nav Up button | OPi Pin 7 | 22 | Blue | 200mm | Spade 2.8mm | Dupont F |
| W44 | Nav Down button | OPi Pin 11 | 22 | Blue | 200mm | Spade 2.8mm | Dupont F |
| W45 | Nav Left button | OPi Pin 13 | 22 | Blue | 200mm | Spade 2.8mm | Dupont F |
| W46 | Nav Right button | OPi Pin 15 | 22 | Blue | 200mm | Spade 2.8mm | Dupont F |
| W47 | Nav Center button | OPi Pin 16 | 22 | Blue | 200mm | Spade 2.8mm | Dupont F |
| W48 | ESC button | OPi Pin 18 | 22 | Blue | 200mm | Spade 2.8mm | Dupont F |
| W49 | Home button | OPi Pin 22 | 22 | Blue | 200mm | Spade 2.8mm | Dupont F |
| W50 | Button ground bus | OPi GND (Pin 14) | 20 | Black | 250mm | Daisy-chain | Dupont F |

**Button Ground Daisy-Chain:**
```
Pin 14 (GND) ─┬─ Nav Up ─┬─ Nav Down ─┬─ Nav Left ─┬─ Nav Right ─┬─ Center ─┬─ ESC ─┬─ Home
              │          │            │            │             │          │       │
            spade      spade       spade        spade         spade      spade   spade
```

Use single 20AWG wire with 7× spade taps soldered at intervals, or 7× short jumpers from terminal strip.

#### 3.4.5 Audio Cables (W51-W55)

| Seg | From | To | AWG | Color | Length | From Term | To Term |
|-----|------|-----|-----|-------|--------|-----------|----------|
| W51 | OPi 3.5mm jack | Ground loop isolator in | — | — | 150mm | 3.5mm M | 3.5mm M |
| W52 | Isolator out (tip) | TPA2016D2 L+ | 22 | White | 100mm | Cut cable | Solder |
| W53 | Isolator out (sleeve) | TPA2016D2 L- | 22 | White/Blk | 100mm | Cut cable | Solder |
| W54 | TPA2016D2 OUT+ | Speaker + | 22 | Red | 150mm | Solder | Solder |
| W55 | TPA2016D2 OUT- | Speaker - | 22 | Black | 150mm | Solder | Solder |

**Notes:**
- W51: Use pre-made 3.5mm stereo patch cable (male-male)
- W52-W53: Cut one end of patch cable, strip shield and conductors
- Mono speaker: Use left channel only (tip + sleeve), leave right unconnected
- Ground loop isolator eliminates 60Hz hum from shared USB/analog grounds

#### 3.4.6 USB Cables (W56-W60)

| Seg | From | To | Length | Type | Notes |
|-----|------|-----|--------|------|-------|
| W56 | OPi USB 3.0 #1 | Deck USB-A panel #1 (angled) | 800mm | USB-A M to panel F | Display cable (coiled); routes through cavity to storm window access port |
| W57 | OPi USB 3.0 #2 | USB panel mount #1 (deck) | 200mm | USB-A M to panel F | Keyboard port |
| W58 | OPi USB 2.0 #1 | ReSpeaker | 150mm | USB-A M to Micro-B | Mic array (direct) |
| W59 | OPi USB 2.0 #2 | USB panel mount #2 (deck) | 200mm | USB-A M to panel F | Aux port |
| W60 | USB panel mount #3 (deck) | Internal extension | 200mm | Panel F to USB-A F | Spare |

**Cable Selection Notes:**
- W56: Coiled USB 3.0 cable recommended (extends to 1m, retracts to 300mm). Stored in cavity.
- W57-W59: Use right-angle USB-A plugs if clearance is tight
- All internal USB cables: 28/28 AWG minimum for data, 24/28 for power+data

#### 3.4.7 External Cables & BMS Leads

These cables are external to the enclosure or supplied with components. Verify/prepare before installation.

**Solar Panel Y-Harness:**

| Item | Specification | Notes |
|------|---------------|-------|
| Panel interconnect | 3× MC4 parallel Y-harness | Combines three 50W panels |
| Panel-to-enclosure | MC4 to XT60 adapter, 3m | Weatherproof outdoor run |
| Wire gauge | 12 AWG minimum | Handles 8.4A Imp |
| Connector gender | MC4 male from panels → Y-harness female | Standard solar convention |

**Preparation:**
1. Verify MC4 connectors mate properly with panels
2. Test continuity through Y-harness (all 3 inputs to single output)
3. Measure open-circuit voltage: ~22V (3× panels in parallel at Voc)
4. Attach XT60 male to enclosure end (solder + heat shrink)
5. Coil and secure for storage in cavity

**BMS Balance Leads:**

| Item | Specification | Notes |
|------|---------------|-------|
| Balance harness | 9-wire JST-XH (B0-B8) | Supplied with JK BMS or pack |
| Length | ~300mm typical | May need extension for routing |
| Cell tap order | B0=Pack-, B1=Cell 1+, ... B8=Cell 8+ | Verify with BMS datasheet |

**Preparation:**
1. If building pack (appendix-e): solder balance taps per cell group
2. If pre-assembled pack: verify harness reaches BMS mounting location
3. Test continuity: each Bn wire to corresponding cell group positive
4. Secure excess length with Kapton tape (avoid pinching)

**BMS Communication (Optional):**

| Item | Specification | Notes |
|------|---------------|-------|
| Bluetooth antenna | Internal to JK BMS | No external cable needed |
| UART/RS485 | 4-pin JST (if enabled) | For wired monitoring—typically unused |

**Cross-reference:** Hardware Spec §4.6 (JK BMS), appendix-e-naion-pack-assembly.md (balance lead installation)

### 3.5 Ferrule All Screw Terminal Connections

**Mandatory:** Every wire entering a screw terminal must have a ferrule crimped on the end. This prevents:
- Strand breakage from screw pressure
- Short circuits from stray strands
- Loosening over time/vibration

---

## 4. Enclosure Modification

### 4.1 Pelican 1557 Air Overview

| Dimension | Value |
|-----------|-------|
| Exterior | 487 × 401 × 267 mm (19.18" × 15.79" × 10.50") |
| Interior | 440 × 330 × 248 mm (17.33" × 13.00" × 9.75") |
| Lid depth | 51mm (2.00") — for display + IT8951 + storm window |
| Base depth | 196mm (7.70") — batteries + 49mm clearance for deck structure |
| Weight (empty) | 3.0 kg (6.6 lbs) |
| Color | Orange (high visibility) |

### 4.2 Deck Faceplate Layout

**Faceplate Material:** 6061-T6 aluminum sheet, 2mm thick

| Parameter | Value |
|-----------|-------|
| Material | 6061-T6 aluminum (heat-treatable, corrosion-resistant) |
| Thickness | 2.0mm (reduced from 3mm for vertical clearance; still rigid for button presses) |
| Dimensions | 425 × 315mm (fits 440×330mm interior with ~7mm clearance) |
| Finish | Mill finish (bare) — optional clear anodize for aesthetics |
| Weight | ~950g |
| Supplier | OnlineMetals.com, MetalsDepot.com, Amazon |

**Why Aluminum:**
1. **Thermal spreader** — Conducts heat from OPi 5+ heatsink to larger surface area
2. **Rigidity** — Won't flex under repeated button presses or drop impact
3. **Tappable** — M3 threads hold securely in 3mm thickness (8+ threads engagement)
4. **Decade-stable** — No delamination, warping, or degradation
5. **EMI continuity** — Conductive surface supports Faraday shielding

**Cutting:**
- Metal supplier can cut to size (recommended)
- Or DIY with jigsaw + metal blade, angle grinder, or hacksaw + file
- Deburr all edges with file or sandpaper

Create template on cardboard before drilling. All measurements from **left edge** and **top edge** of faceplate area.

```
┌───────────────────────────────────────────────────────────────────────┐
│                          DECK FACEPLATE                               │
│                                                                       │
│      ○                                         ┌───┐ ┌───┐ ┌───┐     │
│    ○ ○ ○   ○OLED    [SPEAKER    ○MIC          │PWR│ │ESC│ │HOM│ ○RST│
│      ○     (rect)    GRILL]                   └───┘ └───┘ └───┘     │
│   (NAV)                                                              │
│                                                                       │
│   ○XT60   ●─● BINDING   ○USB-A   ○USB-A   ○USB-A                    │
│   (solar)    POSTS      (kybd)   (aux)    (aux2)                     │
│                                                                       │
└───────────────────────────────────────────────────────────────────────┘
```

**Hole Schedule:**

| Feature | From Left | From Top | Size | Type |
|---------|-----------|----------|------|------|
| Nav Up | 40mm | 20mm | 12mm dia | Step drill |
| Nav Left | 25mm | 35mm | 12mm dia | Step drill |
| Nav Select (center) | 40mm | 35mm | 12mm dia | Step drill |
| Nav Right | 55mm | 35mm | 12mm dia | Step drill |
| Nav Down | 40mm | 50mm | 12mm dia | Step drill |
| OLED window | 90mm | 30mm | 30×15mm | Rectangular cutout |
| Speaker grill | 160mm | 35mm | 50mm dia | Hole saw + mesh |
| Mic hole | 220mm | 35mm | 15mm dia | Step drill |
| Power button | 280mm | 30mm | 16mm dia | Step drill |
| ESC button | 310mm | 30mm | 16mm dia | Step drill |
| Home button | 340mm | 30mm | 16mm dia | Step drill |

| XT60 | 40mm | 80mm | 22mm dia | Step drill |
| Binding post (red) | 80mm | 80mm | 10mm dia | Step drill |
| Binding post (black) | 100mm | 80mm | 10mm dia | Step drill |
| USB-A #1 | 160mm | 80mm | 20×8mm | Rectangular file |
| USB-A #2 | 200mm | 80mm | 20×8mm | Rectangular file |
| USB-A #3 | 240mm | 80mm | 20×8mm | Rectangular file |
| Analog voltmeter | 280mm | 80mm | 45×35mm | Rectangular cutout |

### 4.3 Lid Panel Layout

**Note:** The lid shell itself is NOT drilled or modified (preserves Faraday shielding and IP67 seal). All lid-side components mount to the storm window or internal foam.

| Feature | Position | Size | Purpose | Mounting |
|---------|----------|------|---------|----------|
| Display window | Centered | 230×175mm | 10.3" E-ink viewing | Storm window (adhesive to lid rim) |
| Display cable access port | Bottom right of storm window | 25×12mm cutout | IT8951 USB connection | Recessed USB-A receptacle in storm window frame |

### 4.4 Deck Faceplate Drilling

**⚠️ CRITICAL:** All drilling is performed on the **aluminum deck faceplate only**, NOT the Pelican case shell. The case shell (lid and base) must remain unmodified to preserve IP67 seal and Faraday shielding integrity.

1. **Prep:**
   - Remove case foam/contents
   - Clean surface with IPA
   - Apply masking tape over drill area

2. **Mark:**
   - Transfer template to tape
   - Center punch all hole locations
   - Double-check measurements

3. **Drill:**
   - Pilot drill 3mm at all center punches
   - Step drill to final size (go slow)
   - Use hole saw for speaker grill
   - Use rotary tool for rectangular cutouts

4. **Finish:**
   - Deburr all edges (inside and out)
   - File rectangular cutouts smooth
   - Remove masking tape
   - Clean with IPA

### 4.5 Display Window Installation

**⚠️ CRITICAL CONSTRAINT:** No fasteners may penetrate the case shell (lid or base). Any hole through the HPX² polymer would breach the internal Faraday shielding. The storm window uses **adhesive-only mounting**.

**Materials:**
- Polycarbonate sheet: 245 × 190mm (7.5mm border around display)
- Closed-cell foam strip: 5mm thick, 10mm wide, ~850mm total length
- Silicone sealant: Clear or black, RTV type (primary bond)
- 3M VHB tape: 1mm thick, 12mm wide strips (corner reinforcement)
- UV-filtering film (optional): Cut to window size

**Procedure:**

1. **Cut polycarbonate:**
   - Cut to 245 × 190mm (allows 7.5mm border around 230×175mm display)
   - Sand edges lightly with 220-grit for adhesion
   - Clean both sides with IPA

2. **Apply UV filter (optional):**
   - Apply UV-filtering film to inner surface
   - Squeegee out bubbles
   - Trim edges flush

3. **Create foam spacer frame:**
   - Cut 5mm closed-cell foam strips
   - Apply around display perimeter (inside the polycarbonate border)
   - Creates air gap that absorbs impact if lid slammed

4. **Position display assembly:**
   - Place E-ink display + IT8951 board in lid
   - Ensure proper alignment with window cutout
   - Secure IT8951 with foam blocks or VHB tape (no screws into PCB or lid)

5. **Apply corner reinforcement:**
   - Cut 4× VHB tape strips (25mm each)
   - Apply to lid surface at corners of window opening
   - Do not remove backing yet

6. **Apply sealant:**
   - Run continuous bead of silicone around window perimeter on lid
   - Bead should be 3-4mm diameter
   - Silicone provides primary structural bond + weatherseal

7. **Install storm window:**
   - Remove VHB backing at corners
   - Position polycarbonate over opening
   - Press firmly onto VHB corners first (instant grip)
   - Press perimeter into silicone bead
   - Apply even pressure around edges

8. **Cure:**
   - Apply masking tape strips across window to hold position during cure
   - Allow silicone 24h to cure before removing tape
   - Do not close lid forcefully during cure

**Bond Strength:** RTV silicone provides ~150-200 psi tensile strength when cured. Combined with VHB tape (~90 psi), the adhesive bond exceeds the force required to crack 3mm polycarbonate. Mechanical fasteners are unnecessary.

**Storm Window Air Gap:** The 5mm foam spacer creates air gap between polycarbonate and E-ink panel. This absorbs impact if case is dropped or lid closed forcefully.

### 4.6 Faraday Shielding Installation

**Perform BEFORE installing any components.**

The Faraday cage consists of three elements working together:
1. **Ripstop silver fabric** lining the case interior (lid, base, walls)
2. **Aluminum deck faceplate** (conductive, forms inner shield layer)
3. **Grounding straps** connecting deck to case fabric (EMI continuity)

#### 4.6.1 Fabric Installation

1. **Cut fabric:**
   - Base interior: 440 × 300mm
   - Lid interior: 440 × 300mm
   - Four walls: 440 × 160mm (×2), 300 × 160mm (×2)

2. **Install base:**
   - Clean interior with IPA
   - Apply fabric with spray adhesive
   - Press firmly, remove air bubbles
   - Overlap edges onto walls by 20mm

3. **Install walls:**
   - Overlap with base fabric by 20mm
   - Overlap adjacent wall pieces by 20mm
   - Seal all overlaps with 3M 1181 tape

4. **Install lid:**
   - Cut opening for display window
   - Apply fabric
   - Seal edges around display cutout with 3M 1181 tape
   - Overlap onto lid rim by 20mm

5. **Seal lid-base interface:**
   - Fabric on lid rim overlaps with fabric on case rim when closed
   - This creates continuous shield at closure

#### 4.6.2 Deck-to-Case Grounding

The aluminum deck must be electrically bonded to the case’s Faraday fabric to maintain shielding continuity. Without this, the deck acts as an isolated conductor (potential antenna).

**Grounding Method:**

| Component | Specification | Qty |
|-----------|---------------|-----|
| Grounding strap | Tinned copper braid, 10mm wide, 150mm length | 4 |
| Ring terminals | M4, crimp-on | 8 |
| Deck attachment | M4 screw through deck edge (tapped hole) | 4 |
| Fabric attachment | 3M 1181 conductive tape over braid end | 4 |

**Installation:**

1. Crimp ring terminal on one end of each braid
2. Drill and tap 4× M4 holes near deck corners (not through case)
3. Attach ring terminal to deck with M4 screw + star washer (cuts oxide layer)
4. Route braid down to case wall
5. Press braid flat against Faraday fabric on wall
6. Secure with 3M 1181 tape (full coverage over braid)
7. Ensure tape makes solid contact with both braid and fabric

**Strap Locations:**

| Strap | Deck Position | Wall Target |
|-------|---------------|-------------|
| 1 | Front-left corner | Left wall |
| 2 | Front-right corner | Right wall |
| 3 | Rear-left corner | Left wall |
| 4 | Rear-right corner | Right wall |

**Verification:** After installation, use multimeter continuity mode to confirm <1Ω between any point on deck and any point on case fabric.

#### 4.6.3 Panel-Mount Connector Shielding

Connectors mounted through the aluminum deck inherently maintain EMI continuity if their metal shells contact the deck. For plastic-bodied connectors, additional grounding is required.

| Connector | Body Material | Shielding Method |
|-----------|---------------|------------------|
| XT60 | Nylon (plastic) | Wrap base with 3M 1181 tape, bond to deck |
| Binding posts (banana) | Plastic insulator | Metal mounting nut contacts deck — OK as-is |
| USB-A panel mounts | Metal shell | Metal shell contacts deck — OK as-is |
| C&K AP buttons | Metal body, IP67 | Metal shell contacts deck — OK as-is |
| Speaker grill | Aluminum mesh | Bond mesh to deck with conductive adhesive |
| Mic hole | Open (15mm) | Faraday fabric tube insert, or accept minor leak |
| OLED window | Non-conductive | Behind deck (internal), not a penetration |

**XT60 Shielding Procedure:**

The XT60 connector has a nylon body that breaks EMI continuity. Apply a conductive wrap:

1. Cut 50mm length of 3M 1181 conductive tape
2. Wrap around XT60 body (below deck, on connector shoulder)
3. Extend tape onto deck surface around hole perimeter
4. Press firmly for adhesion
5. Verify continuity from tape to deck with multimeter

**Mic Hole Consideration:**

The 15mm microphone hole is an intentional acoustic opening. Options:
1. **Accept minor leak** — 15mm hole attenuates RF above ~10GHz; lower frequencies still shielded by deck
2. **Mesh insert** — Fine stainless steel mesh (100+ mesh count) with conductive bond to deck
3. **Faraday fabric tube** — Roll fabric into 15mm tube, bond to deck hole perimeter

Recommendation: Accept minor leak. EMP E1 pulse (high-frequency content) is the threat; deck aluminum provides sufficient attenuation for 15mm aperture.

#### 4.6.4 Pressure Equalization Valve

The Pelican 1557 Air has an automatic pressure equalization valve on the **exterior front face** of the case body (near the latches). This valve uses a Gore-Tex membrane that allows air to pass while blocking water.

**Important:** The valve is an external feature molded into the case shell. It is NOT affected by interior Faraday fabric lining. Do not attempt to cover, seal, or modify the valve — it must remain clear for pressure equalization during altitude/temperature changes.

**Verification:** After Faraday installation, confirm the valve (small circular housing with visible membrane) is unobstructed on the case exterior.

#### 4.6.5 Shielding Test

1. Install all connectors and grounding straps
2. Close case with smartphone inside
3. Call the phone
4. **Pass:** Phone goes directly to voicemail (no ring)
5. **Fail:** Phone rings → locate leak with AM radio (static increases near leak)
6. Repair leaks with additional 3M 1181 tape or conductive adhesive

**Target attenuation:** >40dB (adequate for EMP E1/E2 protection)

### 4.7 Weatherproofing Verification

**Architecture Clarification:** The Pelican case shell (lid and base) remains **completely unmodified** - no drilling, no penetrations. All connectors mount to the internal aluminum deck faceplate. When closed, the original Pelican lid gasket seals against the case rim, maintaining IP67 rating.

**Pre-Assembly Baseline (Recommended):**
Before any internal work, verify the unmodified Pelican case meets IP67:
1. Close empty case (no deck, no components)
2. Submerge in 100mm water for 30 minutes
3. Open and inspect for moisture
4. If leak found, case gasket is defective (replace or return)

**Post-Assembly Verification (Required):**
After all internal assembly is complete:

1. **Verify deck clears lid closure:**
   - Deck must sit below case rim level
   - Nothing protrudes above rim that would prevent gasket seal

2. **Check lid gasket condition:**
   - Inspect for debris, damage, or deformation
   - Clean gasket and rim with IPA
   - Apply thin film of silicone grease (optional, aids seal longevity)

3. **Water test:**
   - Close case (electronics inside are protected by unmodified shell)
   - Submerge to 100mm depth for 30 minutes
   - Open and inspect for any moisture inside case

4. **If leak found:**
   - Leak is in case gasket (not deck components)
   - Inspect gasket for damage or debris
   - Contact Pelican for replacement gasket if needed

**Note:** Internal deck connectors (XT60, USB, buttons) do not affect case weatherproofing - they are entirely inside the sealed enclosure.

---

## 5. Internal Layout

### 5.1 5-Layer Cyberdeck Architecture

```
LAYER 1 - LID
┌─────────────────────────────────────────────────────────────────────┐
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                                                             │   │
│  │                    10.3" E-INK DISPLAY                      │   │
│  │                    (behind storm window)                    │   │
│  │                                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  [IT8951 Controller]        [Foam padding]        [USB Panel Mount]│
│                                                                     │
│  ════════════════════════════════════════════════════════════════  │
│  LID FOAM: GPS dongle, USB light, spare USB-C cable, CR2032s,      │
│            cold spare NVMe (anti-static bag), Wago 221 assortment, │
│            spare fuses, desiccant (spare), alligator whip cable    │
│  ════════════════════════════════════════════════════════════════  │
└─────────────────────────────────────────────────────────────────────┘

LAYER 2 - CAVITY (open space when lid open)
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│   [Keyboard in neoprene sleeve]                                    │
│                                                                     │
│   [Folded solar panel #1]  [Folded solar panel #2]                 │
│                                                                     │
│   [Coiled USB display cable]                                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

LAYER 3 - DECK FACEPLATE (user-facing controls)
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  [D-PAD]  [OLED]  [SPEAKER GRILL]  [MIC]  [PWR] [ESC] [HOM]         │
│                                                                     │
│  [XT60]  [BINDING POSTS]  [USB-A ×3]                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

LAYER 4 - DECK UNDERSIDE (electronics)
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────────┐ │
│  │   OPi 5+     │  │   NVMe SSD   │  │   POWER DISTRIBUTION    │ │
│  │  (inverted   │  │  (on carrier │  │                         │ │
│  │   w/heatsink)│  │   standoffs) │  │  [FUSE BLOCK]           │ │
│  │              │  │              │  │  [BREAKER]              │ │
│  └──────────────┘  └──────────────┘  │  [LVD MODULE]           │ │
│                                       │  [NTC THERMISTOR]       │ │
│  ┌──────────────┐  ┌──────────────┐  │  [KSD9700]              │ │
│  │ Meanwell     │  │  TPA2016D2   │  └──────────────────────────┘ │
│  │ SD-15A-5     │  │  Amplifier   │                               │
│  │ DC-DC        │  │  + wiring    │  ┌──────────────┐            │
│  └──────────────┘  └──────────────┘  │  DS3231 RTC  │            │
│                                       │  + USB-C     │            │
│  ┌──────────────────────────────┐    │  Breakout    │            │
│  │     WIRING HARNESS           │    └──────────────┘            │
│  └──────────────────────────────┘                                 │
│                                                                     │
│  ══════════════════ DECK SUPPORT POSTS ════════════════════════   │
└─────────────────────────────────────────────────────────────────────┘

LAYER 5 - BASEMENT (power system)
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  ┌──────────────────────────────────────┐  ┌───────────────────┐  │
│  │       NA-ION 8S4P BATTERY            │  │  GENASUN          │  │
│  │   40Ah / 960Wh (24V nominal)         │  │  GVB-8-WP         │  │
│  │   32× HiNa 32140 + JK BMS            │  │  Boost MPPT       │  │
│  │   (spot-welded pack in foam pocket)  │  │  (18V→32V)        │  │
│  └──────────────────────────────────────┘  └───────────────────┘  │
│                                                                     │
│  ════════════════════════════════════════════════════════════════  │
│  KAIZEN FOAM: Cut pockets for battery, MPPT, spares compartment    │
│  ════════════════════════════════════════════════════════════════  │
│                                                                     │
│  [Passive Bootstrap]  [Analog Voltmeter 0-30V]  [Desiccant ×2]     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.2 Deck Construction

The deck is a single rigid aluminum panel that serves three functions:
1. **Structural platform** — Separates basement (battery) from electronics
2. **User-facing faceplate** — All controls, ports, and displays mount through it
3. **Thermal spreader** — Conducts heat from OPi 5+ heatsink to larger radiating surface

**Material:** 6061-T6 aluminum sheet, 2mm thick (see §4.2 for specifications; reduced from 3mm for vertical clearance)

**Dimensions:** 425 × 315mm (cut to fit 440×330mm interior with ~7mm clearance per side)

**Support System (No Case Penetration):**

The deck rests on PVC posts sitting on Kaizen foam — **no screws or fasteners penetrate the case floor**. This preserves the continuous Faraday shielding.

| Component | Specification |
|-----------|---------------|
| Posts | 4× PVC Schedule 40 pipe sections, ~25mm OD |
| Post height | **147mm** (floor to deck underside; see vertical stack below) |
| Post location | Near corners, inset ~30mm from deck edges |
| Post base | Resting on neoprene or polyethylene foam pads (vibration damping) |
| Lateral constraint | Dense foam blocks wedged between deck edges and case walls |

**Vertical Stack Calculation** (see 01-hardware-specification.md §5.5):

| Layer | Height | Cumulative |
|-------|--------|------------|
| Case floor | 0mm | 0mm |
| Batteries (on side) | 107mm | 107mm |
| Clearance above battery | 10mm | 117mm |
| Electronics zone | 30mm | 147mm |
| **Deck posts (top)** | — | **147mm** |
| Deck plate | 2mm | 149mm |
| Cavity | 47mm | 196mm |
| Case rim | — | 196mm |

**Note:** Batteries installed on their side (212×147×107mm L×W×H) with 107mm height, not 147mm upright. This provides clearance for deck structure.

**Deck Retention:**
- **Primary:** Gravity + foam compression against walls (deck cannot shift laterally)
- **Backup:** If foam degrades, replace with folded cardboard, clothing, or any compressible material

**⚠️ NO FASTENERS INTO CASE.** Screws, threaded inserts, or any penetration of the case floor/walls would breach the Faraday shielding. The deck is held purely by gravity and foam friction.

**Access:** To reach basement (battery, MPPT), simply lift deck straight up off posts. No tools required.

### 5.3 Battery Pack (Na-ion 8S4P)

**⚠️ SAFETY:** Na-ion Polyanion cells are inherently safer than lithium-ion (no thermal runaway at 0V, aluminum collectors on both electrodes), but still require care:
- Do NOT short terminals — high current can cause burns
- Use insulated tools when working near pack
- Wear safety glasses during spot welding
- Work on non-conductive surface (wood, rubber mat)

#### 5.3.1 Pack Specifications

| Parameter | Value |
|-----------|-------|
| Chemistry | Na-ion Polyanion (HiNa 32140 NFPP) |
| Cell Format | 32mm diameter × 140mm cylindrical |
| Configuration | 8S4P (32 cells total) |
| Cell Capacity | 10Ah per cell |
| Pack Voltage | 12V (empty) → 32V (full), **24V nominal** |
| Pack Capacity | 40Ah / 960Wh |
| BMS | JK BD4A8S4P (8S active balancing, Bluetooth) |
| Pack Dimensions (est.) | ~280 × 160 × 120mm (L×W×H) |
| Weight (est.) | ~8 kg |

#### 5.3.2 Acquisition Options

**Option 1: Pre-Assembled Pack**
- If purchasing a pre-assembled Na-ion 8S4P pack with integrated BMS, skip to §5.3.4 (Installation)
- Verify pack meets specifications above before purchase
- Confirm BMS is JK-compatible or has equivalent 8S active balancing
- Request cell-level specifications to confirm Polyanion (NFPP) chemistry

**Option 2: Build from Cells**
- See **appendix-e-naion-pack-assembly.md** for complete fabrication procedure
- Required equipment: kWeld spot welder (or equivalent), 0.15mm nickel strip, 3D-printed cell holders
- Estimated build time: 4-6 hours
- Recommended for: builders wanting full control, sourcing cells directly from HiNa/distributors

#### 5.3.3 Foam Pocket Construction

Cut Kaizen foam pocket in basement layer to hold completed pack:

```
        BASEMENT FOAM LAYOUT — TOP VIEW
┌─────────────────────────────────────────────────────────┐
│                                                         │
│   ┌─────────────────────────────────┐  ┌─────────────┐ │
│   │                                 │  │             │ │
│   │     NA-ION 8S4P PACK           │  │   GENASUN   │ │
│   │     ~280 × 160 × 120mm         │  │   GVB-8-WP  │ │
│   │                                 │  │             │ │
│   │   [XT60 output at top]         │  │ [terminals  │ │
│   │   [BMS module alongside]       │  │  facing up] │ │
│   │                                 │  │             │ │
│   └─────────────────────────────────┘  └─────────────┘ │
│                                                         │
│   [Desiccant]                           [Desiccant]    │
│                                                         │
└─────────────────────────────────────────────────────────┘
      ▲                                        ▲
   FRONT                                    RIGHT
```

**Pocket Specifications:**

| Parameter | Value |
|-----------|-------|
| Pocket outer dimensions | 300 × 180 × 130mm (L×W×D) |
| Pocket inner dimensions | ~285 × 165 × 125mm (snug fit for pack + BMS) |
| Material | Kaizen PE foam, 57mm layers (stack 2-3 layers) |
| Wall thickness | 10-15mm on sides |
| Bottom | Solid foam (pack rests on it) |
| Top | Open (cables exit upward) |

**Cutting Procedure:**
1. Stack 2× layers of 57mm Kaizen foam (or 3× 30mm layers for 90mm+ depth)
2. Mark pocket outline: 285 × 165mm rectangle
3. Cut with hot wire cutter or sharp utility knife
4. Test fit pack — should slide in with light friction
5. Cut cable channel: 30mm wide notch at one end for XT60 + balance leads

#### 5.3.4 Installation

1. **Position foam pocket:**
   - Left side of basement, ~20mm from left wall
   - Front edge ~20mm from front wall
   - Verify clearance to MPPT pocket (§5.6) on right side

2. **Place battery pack:**
   - Slide pack into foam pocket, XT60 connector facing UP
   - BMS module can be:
     - Integrated into pack (if pre-assembled)
     - Mounted alongside pack in same foam pocket
     - Mounted separately on deck underside (if space constrained)
   - Pack should sit level with ~5-10mm foam compression

3. **Connect BMS balance leads:**
   - If BMS separate from pack, connect 9-wire balance harness (B0-B8)
   - Route leads neatly along pack edge
   - Secure with Kapton tape

4. **Connect main power leads:**
   - Pack positive (XT60 or ring terminal) → Battery bus [C]
   - Pack negative → Negative bus [R]
   - See §6.8.2 Wire Schedule, segments W01-W06

5. **Strain relief:**
   - Leave 50mm service loop on all cables
   - Secure cables to foam with adhesive clips
   - Ensure no stress on solder/weld joints

#### 5.3.5 Verification

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| Pack voltage | Multimeter across pack terminals | 12-32V (depending on charge state) |
| Cell balance | JK BMS app via Bluetooth | All cells within 50mV |
| BMS communication | Open JK BMS app, scan for device | BMS appears, shows cell voltages |
| Thermal | Infrared thermometer on pack surface | <40°C (ambient + 15°C max) |
| Physical | Gently rock pack in pocket | No rattling, snug fit |

**If BMS shows cell imbalance >100mV:**
- Allow balancing overnight with pack at rest
- If imbalance persists, individual cell may be faulty
- See appendix-e-naion-pack-assembly.md §7 (Troubleshooting)

#### 5.3.6 Retention

- **Primary:** Kaizen foam compression (deck weight holds pack in pocket)
- **Secondary:** Dense foam wedges at pocket edges if fit is loose
- **Tertiary:** If pack shifts during transport testing, add 25mm nylon strap across top

**⚠️ Do NOT use VHB tape on Na-ion cells.** Adhesive may react with cell casing or leave residue that interferes with thermal management.

### 5.4 Component Mounting (Deck Underside)

| Component | Mount Method | Notes |
|-----------|--------------|-------|
| OPi 5+ | M2.5 standoffs (4×) | Inverted, heatsink faces deck |
| NVMe SSD | M.2 standoff on OPi carrier | Native slot |
| Meanwell DC-DC | M3 screws to deck | Pre-drill holes |
| TPA2016D2 | Double-sided tape or M2 screws | Near speaker |
| DS3231 RTC | Double-sided tape | Near OPi GPIO |
| OLED | M2 screws through PCB mounting holes | Behind faceplate window, use nylon standoffs |
| Fuse block | M4 screws | Near power entry |
| LVD module | M3 screws or DIN clip | Inline with power |
| USB-C breakout | M2 screws | Near OPi power input |
| INA219 | M2 screws or double-sided tape | Deck underside, inline between LVD and KSD9700 |
| 15SQ045 Schottky Diode | M3 screw to deck plate (thermal mass) | Inline between scavenger fuse and battery+ bus |
| 6-Position Barrier Terminal Block | M3 screws to deck | Negative bus, adjacent to fuse block |

### 5.5 Thermal Path

The 3mm 6061-T6 aluminum deck acts as a thermal spreader, distributing heat from the concentrated heatsink area (~36cm²) across the full deck surface (~1170cm²).

```
OPi 5+ SoC (RK3588)
    ↓ (pyrolytic graphite pad — decade-stable, no dry-out)
Aluminum heatsink 60×60×10mm
    ↓ (direct contact or thin thermal pad if gap exists)
6061-T6 Aluminum deck plate 420×278×3mm
    ↓ (convection to cavity air, radiation to case walls)
Case interior (passive cooling)
```

**Critical:** Ensure heatsink makes solid contact with deck underside. OPi 5+ mounts inverted (heatsink facing deck). If gap >0.5mm exists between heatsink and deck, add thermal pad.

**Thermal Performance:**
- Aluminum thermal conductivity: ~167 W/m·K
- Deck thermal mass: ~950g aluminum absorbs transient spikes
- Spreading resistance: ~0.1°C/W (heatsink to deck edge)
- Expected benefit: 10-15°C reduction vs. heatsink-only cooling

### 5.6 MPPT Mounting (Genasun GVB-8-WP)

The Genasun GVB-8-WP boost MPPT charge controller is mounted in the basement layer alongside the Na-ion battery pack. This is a **boost (step-up) topology** controller that converts 18V solar panel voltage to the higher 24-32V battery voltage.

**⚠️ CRITICAL:** The Genasun must be **custom programmed** for our 8S Na-ion pack. Contact Sunforge LLC (Genasun manufacturer) to order with custom voltage profile:
- Absorption voltage: **32.0V** (4.0V/cell × 8S)
- Float voltage: **26.4V** (3.3V/cell × 8S)
- Reference part: GVB-8-WP-Li-CV (custom voltage variant)

#### 5.6.1 Unit Specifications

| Parameter | Value |
|-----------|-------|
| Model | Genasun GVB-8-WP-Li-CV (custom programmed) |
| Topology | Boost (step-up) — NOT buck |
| Dimensions | **80 × 50 × 25mm** (L × W × H) |
| Weight | ~100g |
| Input voltage | 5-26V (solar panel Vmp: 18V) |
| Output voltage | Custom: 32.0V absorption, 26.4V float |
| Max current | 8A |
| Efficiency | 97% typical |
| Enclosure | **IP68 potted/sealed** (WP = Waterproof) |
| Self-consumption | 0.2W |
| 0V Battery Wake | **YES** — critical for Lazarus mode |
| Thermal management | Potted encapsulation, passive conduction |
| Capacitors | Ceramic/tantalum (decade-stable, no electrolytic) |
| Bluetooth/App | None — fixed-voltage controller |

**Source:** Hardware Specification §4.3

#### 5.6.2 Thermal Considerations

Unlike convection-cooled controllers, the Genasun GVB-8-WP uses **potted encapsulation** for thermal management:

- Heat conducts through potting compound to aluminum case
- **No airflow slots required** — sealed unit
- At 97% efficiency with 50W solar input, dissipates ~1.5W as heat
- Potted construction provides decade-stable thermal path (no dry-out)
- Any orientation acceptable (no convection dependency)

**Mounting Surface Contact:** For optimal thermal dissipation, mount with direct contact to Kaizen foam base. The foam's low thermal conductivity is acceptable given the minimal heat load. For enhanced cooling in extreme environments, optionally mount to aluminum bracket with thermal pad.

#### 5.6.3 Mounting Method: Compact Foam Pocket

The Genasun's compact size (60% smaller than Victron) allows a simple, snug foam pocket without airflow provisions.

```
                    BASEMENT FOAM LAYOUT — TOP VIEW
    ┌─────────────────────────────────────────────────────────┐
    │                                                         │
    │   ┌─────────────────────────────────┐  ┌─────────────┐ │
    │   │                                 │  │             │ │
    │   │        NA-ION 8S4P PACK         │  │  GENASUN    │ │
    │   │        ~280 × 160 × 120mm       │  │  GVB-8-WP   │ │
    │   │                                 │  │  80×50×25   │ │
    │   │   [XT60 output at top]          │  │             │ │
    │   │   [BMS module alongside]        │  │ [screw      │ │
    │   │                                 │  │  terminals] │ │
    │   └─────────────────────────────────┘  └─────────────┘ │
    │                                                         │
    │   [Desiccant]                           [Desiccant]    │
    │                                                         │
    └─────────────────────────────────────────────────────────┘
          ▲                                        ▲
       FRONT                                    RIGHT
```

**Foam Pocket Specifications:**

| Parameter | Value |
|-----------|-------|
| Pocket outer dimensions | 100 × 70 × 35mm (L × W × D) |
| Pocket inner dimensions | ~85 × 55 × 30mm (snug fit) |
| Material | Kaizen PE foam, 30mm layer |
| Wall thickness | 7-10mm on sides |
| Bottom | Solid foam (unit rests on it) |
| Top | Open (wire access) |
| Airflow slots | **None required** (sealed unit) |

#### 5.6.4 Mounting Options

The Genasun's light weight (~100g) and compact size allow flexible mounting:

**Option A: Foam Pocket (Recommended)**
- Simple Kaizen foam pocket as shown above
- Snug fit provides retention without straps
- Easy removal for service
- Best for vibration damping

**Option B: VHB Tape Direct Mount**
- Apply 3M VHB tape to bottom of unit
- Adhere directly to case floor (on Faraday fabric)
- Compact, no foam pocket needed
- Harder to remove for service

**Option C: Screw Mount (if equipped)**
- Some Genasun units have mounting flanges with M3 holes
- Mount to aluminum bracket or foam-backed plate
- Most secure for high-vibration environments

#### 5.6.5 Installation Procedure

1. **Verify custom programming:**
   - Confirm unit label shows correct voltage profile
   - Expected: 32.0V absorption, 26.4V float
   - If stock unit received, **do not install** — contact Sunforge LLC

2. **Prepare foam pocket:**
   - Cut pocket in 30mm Kaizen foam: 85 × 55mm rectangle
   - Depth: ~25-30mm (unit sits flush or slightly proud)
   - Test fit — should slide in with light friction
   - **No airflow slots needed**

3. **Position pocket in basement:**
   - Location: Right side of basement, ~20mm from right wall
   - Adjacent to battery pack (short wire runs)
   - Secure foam base to case floor with spray adhesive or VHB tape

4. **Install MPPT:**
   - Slide Genasun into pocket, screw terminals facing UP
   - Verify unit sits level and stable
   - If loose fit, add foam shims at sides

5. **Connect wiring:**
   - **PV+ / PV-:** To XT60 panel mount (solar input, 12 AWG)
   - **BAT+ / BAT-:** To battery bus via ring terminals (10 AWG)
   - Wire polarity is **critical** — reverse connection damages unit
   - Route cables up through deck opening
   - Maintain 50mm service loop for MPPT removal

6. **Verify connections:**
   - Double-check polarity before connecting battery
   - PV terminals: Red/White stripe = positive
   - BAT terminals: Red = positive (to battery bus [C])

#### 5.6.6 Terminal Identification

```
    GENASUN GVB-8-WP — TOP VIEW (terminals up)
    ┌─────────────────────────────────────────┐
    │                                         │
    │   ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐   │
    │   │ PV+ │  │ PV- │  │BAT+ │  │BAT- │   │
    │   └──┬──┘  └──┬──┘  └──┬──┘  └──┬──┘   │
    │      │        │        │        │      │
    │      │        │        │        │      │
    │   SOLAR IN (18V)    BATTERY OUT (32V)  │
    │                                         │
    │         [GENASUN GVB-8-WP]              │
    │         [Custom: 32.0V/26.4V]           │
    │                                         │
    └─────────────────────────────────────────┘
```

**Wire Connections (per §6.8.2):**

| Terminal | Wire | AWG | Color | To |
|----------|------|-----|-------|----|
| PV+ | W19 | 12 | Red/White | XT60 panel mount + |
| PV- | W20 | 12 | Blk/White | XT60 panel mount - |
| BAT+ | W21 | 10 | Red | Battery bus [C] |
| BAT- | W22 | 10 | Black | Negative bus [R] |

#### 5.6.7 Verification

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| Visual | Inspect unit label | Shows custom voltage (32.0V/26.4V) |
| Fit | Rock unit in pocket | No rattling, snug fit |
| Polarity | Trace wires before connection | PV+→XT60+, BAT+→Bus+ |
| Power-on | Apply 18V to PV terminals (bench PSU), 0V load | Unit powers from PV input |
| Charging | Connect to battery pack, apply sunlight/lamp | Voltage rises toward 32V |

**0V Battery Wake Test:**

The Genasun's 0V wake capability is critical for Lazarus mode (decade-dormant recovery):

1. Disconnect battery pack completely
2. Apply 18V to PV terminals via bench supply
3. Measure BAT+ output: Should show ~32V (absorption voltage)
4. This confirms the boost converter activates from PV power alone
5. Reconnect battery pack

#### 5.6.8 Access for Service

To remove MPPT:
1. Disconnect kill switch (safety)
2. Lift Genasun straight up out of foam pocket
3. Disconnect screw terminals (PV, then BAT)
4. Note: No retention strap needed due to compact size and snug foam fit

No tools required for removal — field-serviceable design.

#### 5.6.9 Comparison: Genasun vs. Previous Victron Design

| Parameter | Genasun GVB-8-WP | Victron SmartSolar 75/15 |
|-----------|------------------|-------------------------|
| Topology | Boost (step-up) | Buck (step-down) |
| Dimensions | 80 × 50 × 25mm | 100 × 113 × 40mm |
| Volume | ~100 cm³ | ~452 cm³ |
| Weight | ~100g | ~500g |
| Enclosure | IP68 potted | Vented plastic |
| Cooling | Conduction (sealed) | Convection (airflow) |
| Airflow slots | Not needed | Required |
| Bluetooth | No | Yes |
| 0V wake | Yes | No |
| Decade dormancy | Designed for | Not designed for |
| Capacitors | Ceramic/tantalum | Electrolytic |

**Why Genasun for Lazarus-Phoenix:**
- **0V battery wake** enables decade-dormant recovery
- **Boost topology** matches low-voltage solar to high-voltage Na-ion pack
- **Potted construction** eliminates electrolytic capacitor failure mode
- **Compact size** leaves more basement space for battery pack

### 5.7 Passive Bootstrap Assembly

The passive bootstrap circuit provides unconditional 0V battery recovery via Ohm's Law — a failsafe backup path that operates without any active electronics. Even if the Genasun controller fails after decade storage, this circuit will trickle-charge a dead battery using only passive components.

#### 5.7.1 Purpose & Components

**Function:** When battery voltage is 0V and solar panels are illuminated, current flows through the bootstrap path to trickle-charge the pack until the Genasun MPPT can take over (~5V).

**Circuit Path:**
```
PV+ ─── 1N5408 ─── 47Ω 25W ─── Battery+
       (diode)   (resistor)
```

**Components (per Hardware Spec §4.5):**

| Component | Specification | Purpose |
|-----------|---------------|---------|
| Diode | 1N5408 (1000V/3A silicon rectifier) | Blocks reverse current; **NOT Schottky** — silicon has nA-range reverse leakage vs mA for Schottky |
| Resistor | 47Ω 25W vitreous enamel wirewound | Current limiting; survives decade storage with no drift |
| Fuse | 2A slow-blow (optional) | Short circuit protection |

**Why Silicon (1N5408) NOT Schottky:**
Schottky diodes have high reverse leakage current (mA range) that increases exponentially with temperature. Over decade storage in a hot enclosure (60°C+), this would slowly drain the battery. Silicon rectifiers have nA-range leakage — orders of magnitude safer.

#### 5.7.2 Thermal Mounting

**⚠️ CRITICAL:** The 47Ω resistor dissipates significant power during 0V recovery and requires thermal mass for heat sinking.

**Power Dissipation Calculation:**
- Panel Voc: 22V (typical, overcast may be lower)
- Current at 0V battery: 22V ÷ 47Ω = 0.47A
- Power: 22V × 0.47A = **10.3W**
- Resistor rating: 25W (2.4× safety margin ✓)

**Mounting Method — Bolt to Deck Plate:**

```
                     RESISTOR MOUNTING (side view)
    ┌────────────────────────────────────────────┐
    │            Aluminum deck plate             │
    │               (2mm thick)                  │
    └───────────────────┬────────────────────────┘
                        │
                   M4 bolt
                        │
              ┌─────────┴─────────┐
              │    47Ω 25W        │
              │  Vitreous Enamel  │
              │    Wirewound      │
              │   (cylindrical)   │
              └───────────────────┘
```

**Procedure:**

1. **Select mounting location:**
   - Deck underside, near MPPT cable entry point
   - Clear of other components (heat radiates outward)
   - At least 30mm clearance on all sides for airflow

2. **Prepare mounting bracket:**
   - Use resistor's existing mounting tab (if equipped), OR
   - Create simple L-bracket from 1mm aluminum strip
   - Drill M4 hole for bolt-through to deck

3. **Install resistor:**
   - Position resistor body parallel to deck (maximize radiating surface)
   - Apply thermal paste to mounting point (optional but recommended)
   - Secure with M4 × 10mm bolt + lock washer through deck
   - Torque to 0.5 Nm

4. **Verify thermal path:**
   - Resistor mounting tab makes solid contact with deck
   - No air gap between resistor and aluminum
   - Deck acts as thermal mass/spreader

**Thermal Behavior:**
- At 10W continuous: Resistor body reaches ~80-100°C
- Deck plate spreads heat to larger surface area
- Acceptable for 0V recovery (typically <1 hour duration)
- Once Genasun takes over at ~5V, bootstrap current drops to near-zero

#### 5.7.3 Assembly & Wiring

**Wire Segments (per §6.8.2 Wire Schedule):**

| Seg | From | To | AWG | Color | Length | Terminations |
|-----|------|-----|-----|-------|--------|---------------|
| W28 | Genasun PV+ | 1N5408 Anode | 18 | Red/White | 50mm | Ferrule ↔ Solder |
| W29 | 1N5408 Cathode | 47Ω resistor | 18 | Red | 50mm | Solder ↔ Solder |
| W30 | 47Ω resistor | Battery bus [C] | 18 | Red | 80mm | Solder ↔ Ring 10mm |

**Assembly Sequence:**

1. **Pre-wire components on bench:**
   - Solder W28 to 1N5408 anode (non-banded end)
   - Solder W29 between 1N5408 cathode (banded end) and resistor lead
   - Solder W30 to opposite resistor lead
   - Apply heat shrink to all solder joints

2. **Install diode:**
   - Position 1N5408 near Genasun PV+ terminal
   - Secure diode body with Kapton tape or cable tie (no heatsink needed — low dissipation)
   - Connect W28 ferrule to same point as W24 (Genasun PV+ input)

3. **Install resistor:**
   - Mount per §5.7.2 thermal mounting procedure
   - Route W29 from diode to resistor (keep clear of hot resistor body)
   - Route W30 from resistor to battery bus

4. **Connect to battery bus:**
   - Attach W30 ring terminal to battery bus stud at node [C]
   - Same stud as Genasun BAT+ (W26) and pack positive (W01)

**Polarity Check:**
```
Solar XT60+ ─── Genasun PV+ ─┬─── (W28) ─── 1N5408 ─── (W29) ─── 47Ω ─── (W30) ─── Battery+
                             │           A→K         resistor
                             │         (anode to
                             │          cathode)
                             │
                             └─── Genasun internal boost path ─── Battery+
```

#### 5.7.4 Verification

**Pre-Power Checks:**

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| Diode polarity | Multimeter diode mode: anode (W28) to cathode (W29) | ~0.6V forward drop |
| Diode reverse | Multimeter diode mode: cathode to anode | OL (open) |
| Resistor value | Multimeter resistance across resistor | 45-50Ω (±5% tolerance) |
| Continuity | W28 ferrule to W30 ring terminal | Continuity through diode + resistor |
| Thermal mount | Attempt to move resistor by hand | Firmly attached, no rattle |

**0V Recovery Test (Functional — from §8.1):**

1. Disconnect battery pack from system (remove XT60 from battery bus)
2. Connect solar panel to XT60 [S] (or apply 18V from bench PSU to PV terminals)
3. Measure voltage across 47Ω resistor with multimeter
4. **Pass:** Should read 8-10V (indicating ~0.2-0.4A trickle current)
5. Measure current through bootstrap path (inline ammeter or clamp meter)
6. **Pass:** 0.3-0.5A at 18V panel input
7. Reconnect battery pack — Genasun should take over charging

**Expected Values at 0V Battery:**

| Panel Voltage | Bootstrap Current | Resistor Power | Resistor Voltage |
|---------------|-------------------|----------------|------------------|
| 18V (Vmp) | 0.38A | 6.8W | 18V |
| 22V (Voc) | 0.47A | 10.3W | 22V |
| 12V (cloudy) | 0.26A | 3.1W | 12V |

**⚠️ Note:** During 0V recovery test, resistor will get hot (80-100°C). This is normal. Do not touch resistor body during or immediately after test. Allow 5 minutes cool-down before handling.

### 5.8 Load Disconnect Assembly

The Load Disconnect circuit prevents boot-loop failure where the computer attempts to start on an insufficiently charged battery, crashes the voltage, and blocks charging. Cross-reference Hardware Specification §4.7 for design rationale.

#### 5.8.1 Purpose & Components

**Purpose:**
- Disconnect load (computer) when battery voltage < 18V
- Reconnect load when battery voltage > 20V
- 2V hysteresis prevents rapid cycling
- Zero quiescent power draw using latching relay

**Component List:**

| Component | Value/Part | Quantity | Purpose |
|-----------|------------|----------|----------|
| TL431A | TO-92 | 2 | Precision voltage reference (2.5V internal) |
| 2N2222A | TO-92 NPN | 2 | Relay coil drivers |
| Panasonic ADW1124HLW | 24V latching | 1 | Load switch (10A NO contacts) |
| R1 | 68kΩ 1/4W 1% | 1 | SET divider upper |
| R2 | 10kΩ 1/4W 1% | 1 | SET divider lower |
| R3 | 56kΩ 1/4W 1% | 1 | RESET divider upper |
| R4 | 10kΩ 1/4W 1% | 1 | RESET divider lower |
| R5, R6 | 10kΩ 1/4W | 2 | TL431 cathode pull-ups |
| R7, R8 | 4.7kΩ 1/4W | 2 | Transistor base resistors |
| C1, C2 | 100µF 50V electrolytic | 2 | Pulse energy storage |
| D1, D2 | 1N4148 | 2 | Flyback protection |
| Perfboard | 50×70mm | 1 | Circuit substrate |
| M3×8 standoffs | Nylon | 4 | PCB mounting |

**Threshold Calculations:**

*SET threshold (close relay):*
- TL431 trips when Vref = 2.5V
- Vbat = Vref × (R1 + R2) / R2 = 2.5V × (68k + 10k) / 10k = **19.5V** (~20V)

*RESET threshold (open relay):*
- Vbat = 2.5V × (56k + 10k) / 10k = **16.5V** (~18V with component tolerance)

**Note:** Use 1% tolerance resistors for R1-R4. Standard 5% resistors may cause threshold drift of ±1V.

#### 5.8.2 Circuit Topology

```
══════════════════════════════════════════════════════════════════════════════
                    LOAD DISCONNECT CIRCUIT — PULSE-LATCH
                          Universal Knowledge Ark
══════════════════════════════════════════════════════════════════════════════

                              ADW1124HLW RELAY
                           ┌─────────────────────┐
                           │                     │
  Battery+ ────────────────┤ COM        NO ├─────┼─────────► To DC-DC (Load)
                           │                     │
                           │  SET    RESET       │
                           │  COIL   COIL        │
                           └───┬───────┬─────────┘
                               │       │
                              D1      D2     (1N4148 flyback diodes)
                               │       │
                               │       │
                 C1 100µF ─────┤       ├───── C2 100µF
                               │       │
                 Q1 collector──┘       └──Q2 collector
                      │                      │
                  2N2222A                2N2222A
                      │                      │
             Q1 base──┤                      ├──Q2 base
                      │                      │
              R7 4.7k─┤                      ├─R8 4.7k
                      │                      │
  SET THRESHOLD ──────┘                      └────── RESET THRESHOLD
  (TL431 cathode)                                   (TL431 cathode)


  SET THRESHOLD CIRCUIT                 RESET THRESHOLD CIRCUIT
  ~~~~~~~~~~~~~~~~~~~~~~                ~~~~~~~~~~~~~~~~~~~~~~~~

  Battery+ ──┬── R1 68kΩ ──┬            Battery+ ──┬── R3 56kΩ ──┬
             │             │                       │             │
             │             │                       │             │
      R5 10k─┴─► TL431     │                R6 10k─┴─► TL431     │
             │  cathode    │                       │  cathode    │
             │     │       │                       │     │       │
             │     └── REF │                       │     └── REF │
             │         │   │                       │         │   │
             │        R2   │                       │        R4   │
             │       10kΩ  │                       │       10kΩ  │
             │         │   │                       │         │   │
  Battery- ──┴─────────┴───┴──TL431 anode          ┴─────────┴───┴──TL431 anode


  CAPACITOR PRE-CHARGE:
  ~~~~~~~~~~~~~~~~~~~~~
  Battery+ ── R9 1kΩ ──┬── C1+ (also feeds R10 1kΩ ── C2+)
                       │
                       └── Charges capacitors when circuit powered
                           Time constant: 1kΩ × 100µF = 100ms

══════════════════════════════════════════════════════════════════════════════
```

**Circuit Operation:**

1. **Initial State (Vbat < 16.5V):** Both TL431s OFF (cathodes pulled high via R5/R6). Transistors Q1/Q2 OFF. Relay in last-known state (typically OPEN if discharged).

2. **Battery Charging (16.5V → 19.5V):** RESET TL431 turns ON first at 16.5V (no effect, relay already open). Capacitors C1/C2 charge via R9/R10.

3. **SET Trigger (Vbat crosses 19.5V rising):**
   - SET TL431 turns ON → cathode pulls low
   - Q1 base driven via R7 → Q1 turns ON
   - C1 discharges through SET coil (~50ms pulse)
   - Relay latches CLOSED → load connected

4. **Normal Operation (Vbat 19.5V - 28V):** Relay remains latched. No current draw.

5. **RESET Trigger (Vbat drops below 16.5V):**
   - RESET TL431 turns OFF → cathode goes high
   - BUT... (see note below about edge detection)

**⚠️ Design Note:** The simple circuit above provides level-triggered response. For true edge-triggered behavior (single pulse only at threshold crossing), additional RC differentiator networks are needed. The capacitor discharge naturally limits pulse duration, but repeated triggers can occur if voltage hovers near threshold. For this application, the latching relay behavior makes this acceptable—once latched, repeated pulses have no effect.

#### 5.8.3 Perfboard Assembly

**Layout (50×70mm perfboard, component side view):**

```
  ┌─────────────────────────────────────────────────────────────┐
  │  ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ○ [R1 68k  ] ○ ○ ○ ○ ○ ○ [R3 56k  ] ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ○ [R2    ] ○ [TL431-S] ○ [R4    ] ○ [TL431-R] ○ ○ ○ ○ ○   │
  │  ○ [10k   ] ○           ○ [10k   ] ○           ○ ○ ○ ○ ○   │
  │  ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ○ [R5 10k] ○ [R7 4k7] ○ [R6 10k] ○ [R8 4k7] ○ ○ ○ ○ ○ ○   │
  │  ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ○ [Q1 2N2222] ○ ○ ○ ○ ○ [Q2 2N2222] ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ○ [C1 100µF ●] ○ [D1] ○ [C2 100µF ●] ○ [D2] ○ ○ ○ ○ ○ ○   │
  │  ○      +       ○ ┃┃   ○      +       ○ ┃┃   ○ ○ ○ ○ ○ ○   │
  │  ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ○ [R9 1k  ] ○ ○ ○ ○ ○ ○ [R10 1k ] ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○ ○   │
  │  ◉         ◉                                   ◉         ◉ │ ← M3 mounting holes
  │  [Bat+] [Bat-] [SET] [RESET] [COM] [NO]                    │ ← Wire pads
  └─────────────────────────────────────────────────────────────┘

  Legend:
  ○ = Perfboard hole
  ◉ = M3 mounting hole (drill 3.2mm)
  ● = Capacitor polarity (+ side)
  ┃┃ = Diode (cathode stripe toward relay coil)
```

**Assembly Sequence:**

1. **Drill mounting holes:** Four corners, 3.2mm for M3 standoffs
2. **Install resistors:** R1-R10, bend leads flat on solder side
3. **Install TL431s:** Note pinout (REF-Anode-Cathode, flat face reference)
4. **Install transistors:** Q1, Q2 (2N2222), note EBC pinout
5. **Install diodes:** D1, D2 (1N4148), cathode stripe toward relay connection
6. **Install capacitors:** C1, C2 (observe polarity, + toward Battery+)
7. **Wire solder-side traces:** Use insulated wire jumpers as needed
8. **Add terminal pads:** Six solder pads for external connections

**TL431A Pinout (TO-92, flat face toward you):**
```
         ┌─────┐
    REF ─┤  ○  ├─ CATHODE
         │     │
         └──┬──┘
            │
          ANODE
```

**2N2222A Pinout (TO-92, flat face toward you):**
```
         ┌─────┐
      E ─┤  ○  ├─ C
         │     │
         └──┬──┘
            │
            B
```

#### 5.8.4 Relay Mounting & Wiring

**Relay Mounting:**

The Panasonic ADW1124HLW relay mounts separately from the PCB due to its size and contact ratings.

| Step | Action |
|------|--------|
| 1 | Position relay on deck near DC-DC converter |
| 2 | Mark mounting holes (relay base pattern) |
| 3 | Drill 3mm pilot holes |
| 4 | Secure with M3×6 screws + washers |
| 5 | Route 18AWG wires from COM/NO terminals to battery bus and DC-DC input |

**Relay Pinout (ADW1124HLW):**
```
  ┌───────────────────┐
  │                   │
  │   SET    RESET    │
  │   COIL   COIL     │
  │    ○       ○      │   ← Coil terminals (top)
  │    │       │      │
  │    ○       ○      │   ← Coil common (tied to Battery-)
  │                   │
  │   COM      NO     │
  │    ●───○   ○      │   ← Contact terminals (bottom)
  │         NC        │
  └───────────────────┘
```

**Wire Schedule (add to §6.8.2):**

| Wire ID | From | To | Gauge | Color | Length |
|---------|------|-----|-------|-------|--------|
| W31 | Battery+ bus | LVD PCB Bat+ | 18 AWG | Red | 15cm |
| W32 | Battery- bus | LVD PCB Bat- | 18 AWG | Black | 15cm |
| W33 | LVD PCB SET | Relay SET coil | 22 AWG | Yellow | 10cm |
| W34 | LVD PCB RESET | Relay RESET coil | 22 AWG | Orange | 10cm |
| W35 | Relay coil common | Battery- bus | 22 AWG | Black | 10cm |
| W36 | Battery+ bus | Relay COM | 18 AWG | Red | 10cm |
| W37 | Relay NO | DC-DC input+ | 18 AWG | Red | 15cm |

**PCB Mounting:**

Mount the Load Disconnect PCB using nylon standoffs:

| Step | Action |
|------|--------|
| 1 | Position PCB on deck near relay |
| 2 | Mark four mounting holes |
| 3 | Drill 3mm pilot holes |
| 4 | Install M3×8 nylon standoffs |
| 5 | Secure PCB with M3 nylon screws |

**Connection Diagram:**

```
  Battery+ Bus ──────────────────────────────┬──────────────────► W36 ► Relay COM
       │                                     │
       │                                     └──► W31 ► LVD PCB [Bat+]
       │
       │                                          LVD PCB
       │                                     ┌─────────────────┐
       │                                     │   ┌─► W33 ►─────┼─► Relay SET coil
       │                                     │   │             │
       │                                     │   └─► W34 ►─────┼─► Relay RESET coil
       │                                     │                 │
       │                                     │   ◄─ W32 ◄──────┼─┐
       │                                     └─────────────────┘ │
       │                                                         │
  Battery- Bus ◄─────────────────────────────────────────────────┴──◄ W35 ◄ Relay coil common


  Relay COM ◄─── W36 ◄─── Battery+ Bus
       │
  Relay NO ────► W37 ────► DC-DC Input+ ────► Computer/Load
```

#### 5.8.5 Verification

**Pre-Power Checks:**

| Test | Method | Expected |
|------|--------|----------|
| R1 value | Multimeter Ω | 66-70kΩ |
| R2, R4 values | Multimeter Ω | 9.5-10.5kΩ |
| R3 value | Multimeter Ω | 54-58kΩ |
| C1, C2 polarity | Visual inspection | + toward Battery+ rail |
| D1, D2 orientation | Visual (cathode stripe) | Stripe toward relay coil |
| TL431 continuity | Anode to REF | ~2MΩ (high-Z) |
| Q1, Q2 transistor test | Multimeter diode mode | B-E ~0.6V, B-C ~0.6V, C-E OL |
| Relay coil resistance | SET coil, RESET coil | ~1kΩ each |
| No shorts | Bat+ to Bat- | OL (open) |

**Bench Power Supply Test:**

Use adjustable bench PSU (0-30V, 2A minimum) to simulate battery voltage.

| Step | Action | Expected Result |
|------|--------|-----------------|
| 1 | Set PSU to 12V, connect to LVD PCB Bat+/Bat- | Relay remains OPEN (NC state) |
| 2 | Slowly increase voltage toward 20V | Monitor relay — should not click yet |
| 3 | Cross 19-20V threshold | Relay CLICKS — SET coil fires, relay latches CLOSED |
| 4 | Verify relay state | COM-NO continuity (closed circuit) |
| 5 | Increase to 24V | No change — relay stays closed |
| 6 | Decrease voltage slowly toward 18V | Monitor relay — should not click yet |
| 7 | Cross 17-18V threshold | Relay CLICKS — RESET coil fires, relay latches OPEN |
| 8 | Verify relay state | COM-NO open (no continuity) |
| 9 | Decrease to 12V | No change — relay stays open |
| 10 | Repeat cycle 3× | Consistent SET/RESET at thresholds |

**Threshold Verification Table:**

| Voltage | SET TL431 | RESET TL431 | Expected Relay State |
|---------|-----------|-------------|----------------------|
| 12V | OFF | OFF | OPEN (no boot) |
| 15V | OFF | OFF | OPEN (no boot) |
| 17V | OFF | ON (no effect) | OPEN (no boot) |
| 18V | OFF | ON | OPEN (no boot) |
| 19V | OFF | ON | OPEN (no boot) |
| 20V | ON → pulse | ON | CLOSED (boot OK) |
| 24V | ON (steady) | ON | CLOSED (boot OK) |
| 28V | ON (steady) | ON | CLOSED (boot OK) |
| 17V (falling) | OFF | OFF → pulse | OPEN (disconnect) |

**Troubleshooting:**

| Symptom | Probable Cause | Fix |
|---------|----------------|-----|
| Relay never closes | SET threshold too high | Check R1/R2 values, verify TL431 orientation |
| Relay closes too early (<18V) | SET threshold too low | R1 value too low, replace |
| Relay never opens | RESET threshold too low | Check R3/R4 values |
| Relay chatters near threshold | Hysteresis too narrow | Add positive feedback resistor (advanced) |
| No click heard | Coil not energized | Check transistor, capacitor polarity, wiring |
| Relay clicks but no contact | Relay contacts damaged | Replace relay |

**⚠️ Integration Note:** The Load Disconnect relay NO contact is in series with the battery+ line to the DC-DC converter. When relay is OPEN, the computer has no power. When relay is CLOSED, computer can boot. This is the intended behavior to prevent boot-loop on low battery.

---

### 5.9 Analog Voltmeter Installation

The analog voltmeter provides battery status feedback with zero silicon components—a purely mechanical indicator that survives decade dormancy when all digital systems may have degraded. Cross-reference Hardware Specification §4.6 for design rationale.

#### 5.9.1 Purpose & Component

**Purpose:**
- User-visible battery voltage indication
- Zero silicon in measurement path (decade-stable)
- Fallback status when digital systems fail
- Visual confirmation during Lazarus wake recovery

**Component:**

| Parameter | Specification |
|-----------|---------------|
| Type | 0-30V DC Moving-Coil Panel Meter |
| Mechanism | d'Arsonval (permanent magnet + wire coil) |
| Accuracy | ±2% full scale (±0.6V) |
| Face Dimensions | ~45×35mm (verify before cutting) |
| Mounting | Panel-mount with bezel clips or screws |
| Power Required | None (passive mechanical) |
| Connection | Spade terminals (typically 2.8mm) |

**Voltage-to-State Reference Label:**

| Reading | Battery State | System Capability |
|---------|---------------|-------------------|
| 0V | Depleted/Dormant | Passive bootstrap charging |
| 5-12V | Lazarus wake | Genasun active, charging |
| 12-16V | Low | Still charging, wait |
| 16-20V | Emergency Mode | Text retrieval (~3W) |
| 20-24V | Full Mode | Full LLM inference |
| 24-32V | Comfortable | Extended runtime |
| 32V | Full | Fully charged |

#### 5.9.2 Panel Cutout

**Location:** Deck faceplate, visible when lid open (per §4.2 Hole Schedule)

**Cutout Dimensions:**
- Position: 280mm from left edge, 80mm from bottom
- Size: 45×35mm rectangular (verify against actual meter)
- Orientation: Number scale horizontal, readable from front

**Procedure:**

| Step | Action |
|------|--------|
| 1 | Remove deck faceplate from case (if already installed) |
| 2 | Mark center point at 280mm × 80mm coordinates |
| 3 | Verify meter face dimensions with calipers |
| 4 | Draw 45×35mm rectangle centered on mark |
| 5 | Apply masking tape over area (protects finish) |
| 6 | Drill 10mm pilot holes at corners (inside rectangle) |
| 7 | Cut between holes using Dremel or jigsaw |
| 8 | File edges smooth, deburr |
| 9 | Test-fit meter (should sit flush with bezel) |
| 10 | Remove masking tape, clean debris |

**⚠️ Measure Twice, Cut Once:** Panel meter dimensions vary by manufacturer. Verify your specific meter before cutting. The 45×35mm dimension is typical but not universal.

#### 5.9.3 Mounting Procedure

**Mounting Methods (meter-dependent):**

| Method | Description | Tools |
|--------|-------------|-------|
| Bezel clips | Spring clips on meter sides snap into panel | None |
| Rear screws | M3 screws through meter flange | 2.5mm hex |
| Front bezel | Decorative ring threads onto front | Hand tight |

**Installation Steps:**

| Step | Action |
|------|--------|
| 1 | Insert meter from rear of panel |
| 2 | Verify bezel sits flush against front |
| 3 | Secure using meter's mounting method |
| 4 | Verify meter face is level (adjust if needed) |
| 5 | Confirm needle rests at 0V (no load applied) |

**Seal Consideration:** If operating in high-humidity environments, apply thin bead of silicone around bezel perimeter to prevent moisture intrusion. Moisture can cause bearing seizure over decade timeframes.

#### 5.9.4 Wiring

**Wire Schedule (per §6.8.2):**

| Wire ID | From | To | AWG | Color | Length | Terminations |
|---------|------|-----|-----|-------|--------|---------------|
| W18 | Battery bus [C] | Voltmeter + [T] | 18 | Red | 200mm | Ring 10mm ↔ Spade 2.8mm |
| W19 | Negative bus [R] | Voltmeter - [T] | 18 | Black | 200mm | Ferrule ↔ Spade 2.8mm |

**Connection Diagram:**

```
  Battery+ Bus [C] ────────── W18 (Red 18AWG) ───────────⮞ Voltmeter (+)
        │                                                    │
        │                                              ┌──────┴──────┐
        │                                              │   0-30V    │
        │                                              │  [======]  │
        │                                              │   Meter    │
        │                                              └──────┬──────┘
        │                                                    │
  Battery- Bus [R] ────────── W19 (Black 18AWG) ─────────⮞ Voltmeter (-)
```

**Installation Steps:**

| Step | Action |
|------|--------|
| 1 | Verify meter polarity markings (+ and - terminals) |
| 2 | Crimp spade terminals onto W18 and W19 wire ends |
| 3 | Connect W18 (red) to meter positive terminal |
| 4 | Connect W19 (black) to meter negative terminal |
| 5 | Route wires to battery bus bar (allow service loop) |
| 6 | Connect W18 ring terminal to Battery+ bus |
| 7 | Connect W19 ferrule to Negative bus |
| 8 | Verify connections are tight (no wobble) |

**⚠️ Polarity Warning:** Reversed polarity will peg the needle backward and may damage the meter movement. Verify + to + before applying power.

#### 5.9.5 Verification

**Pre-Power Visual Check:**

| Check | Method | Expected |
|-------|--------|----------|
| Needle at rest | Visual | Points to 0V (or slightly below) |
| Polarity | Trace wires | Red to +, Black to - |
| Connection security | Gentle tug | No looseness |
| Panel mounting | Visual | Bezel flush, no gaps |

**Bench Power Supply Calibration Test:**

| Step | PSU Voltage | Expected Reading | Pass Criteria |
|------|-------------|------------------|---------------|
| 1 | 0V (off) | 0V | Needle at rest |
| 2 | 12V | 12V | ±0.5V |
| 3 | 18V | 18V | ±0.5V |
| 4 | 24V | 24V | ±0.5V |
| 5 | 30V | 30V | ±0.6V |
| 6 | 32V | 30V (pegged) | Full-scale deflection |

**In-System Verification:**

| Step | Action | Expected |
|------|--------|----------|
| 1 | Connect charged battery pack | Meter shows 24-32V |
| 2 | Compare to multimeter at Battery+ bus | Within ±1V |
| 3 | Verify Load Disconnect test (TP2) agrees | Same reading |

**Troubleshooting:**

| Symptom | Probable Cause | Fix |
|---------|----------------|-----|
| Needle doesn't move | Open circuit, loose connection | Check W18/W19 continuity |
| Needle pegged backward | Reversed polarity | Swap W18/W19 at meter |
| Reading significantly off | Meter out of cal, wrong range | Replace meter or verify 0-30V range |
| Needle sluggish/sticks | Bearing contamination | Replace meter (internal issue) |
| Intermittent reading | Loose spade terminal | Re-crimp or replace terminal |

**✅ Zero-Silicon Verification:** Unlike all digital readings, the analog meter contains no semiconductors, no software, no firmware. It provides ground-truth battery status independent of all electronic systems. After decade dormancy, this may be the only functioning status indicator.

---

## 6. Wiring Harness

### 6.1 Power Distribution Diagram

**Lazarus-Phoenix Architecture (v3.0.0)**

This diagram shows the complete power system with Na-ion battery, boost MPPT, passive bootstrap for 0V recovery, and load disconnect for boot-loop prevention.

```
═══════════════════════════════════════════════════════════════════════════════
                    POWER DISTRIBUTION — LAZARUS-PHOENIX
                       Universal Knowledge Ark v3.2.0
═══════════════════════════════════════════════════════════════════════════════

        SOLAR ARRAY                                    SCAVENGER INPUT
     3× 50W 12V Parallel                               (Binding Posts)
    Vmp=18V, Imp=8.4A                                    18-30V DC
        (150W total)                                        │
             │                                              │ Banana jacks
             │ XT60 [W19,W20]                               ▼
             ▼                                    ┌──────────────────┐
    ┌────────────────┐                            │    15A ATO FUSE  │
    │  XT60 PANEL    │                            │    (scavenger)   │
    │    MOUNT       │                            └────────┬─────────┘
    │  (faceplate)   │                                     │ [W23]
    └───────┬────────┘                                     ▼
            │                                     ┌──────────────────┐
            │                                     │  1N5408 BLOCKING │
            │                                     │     DIODE        │
            │                                     │  (backfeed prot) │
            │                                     └────────┬─────────┘
            │                                              │ [W24,W25]
            ├──────────────────────────────────────────────┤
            │                                              │
            │                                              │
    ┌───────┴────────────────────────────────────┐        │
    │                                            │        │
    │            CHARGING PATHS                  │        │
    │                                            │        │
    │  ┌────────────────┐   ┌────────────────┐   │        │
    │  │    PASSIVE     │   │    GENASUN     │   │        │
    │  │   BOOTSTRAP    │   │   GVB-8-WP     │   │        │
    │  │                │   │                │   │        │
    │  │  1N5408 diode  │   │  Boost MPPT    │   │        │
    │  │      +         │   │  18V → 32V     │   │        │
    │  │  47Ω 25W res   │   │                │   │        │
    │  │                │   │  Custom:       │   │        │
    │  │  (0V recovery  │   │  32.0V abs     │   │        │
    │  │   fallback)    │   │  26.4V float   │   │        │
    │  │                │   │                │   │        │
    │  │  ~0.5A trickle │   │  0V wake: YES  │   │        │
    │  └───────┬────────┘   └───────┬────────┘   │        │
    │          │                    │ [W21,W22]  │        │
    │          │                    │            │        │
    └──────────┴────────────────────┴────────────┘        │
                         │                                │
                         │                                │
                         ▼                                │
    ┌─────────────────────────────────────────────────────┴───────────────────┐
    │                                                                         │
    │                    NA-ION 8S4P BATTERY SYSTEM                           │
    │                                                                         │
    │    ┌─────────────────────────────────────────────────────────────┐      │
    │    │   HiNa 32140 Polyanion Cells (32 total)                     │      │
    │    │   Configuration: 8S4P                                       │      │
    │    │   Capacity: 40Ah / 960Wh                                    │      │
    │    │   Voltage: 12V (empty) ─── 24V (nominal) ─── 32V (full)     │      │
    │    │                                                             │      │
    │    │   + JK BMS BD4A8S4P (active balancing, Bluetooth)           │      │
    │    │   + 0V storage safe (aluminum collectors both electrodes)   │      │
    │    └─────────────────────────────────────────────────────────────┘      │
    │                              │                                          │
    │                              │ [W05,W06]                                │
    │                              │                                          │
    └──────────────────────────────┼──────────────────────────────────────────┘
                                   │
        ┌──────────────────────────┤
        │                          │
        ▼                          ▼
┌───────────────────┐    ┌──────────────────────────────────────────────────┐
│  ANALOG VOLTMETER │    │              LOAD DISCONNECT                     │
│     0-30V DC      │    │                                                  │
│   (moving coil)   │    │   TL431 Precision Shunt + Resistor Divider       │
│                   │    │              +                                   │
│  Zero silicon     │    │   Panasonic ADW1124HLW Latching Relay            │
│  feedback path    │    │                                                  │
│                   │    │   ┌─────────────────────────────────────────┐    │
│  ┌─────────────┐  │    │   │  Threshold Behavior:                    │    │
│  │ 32V ═ Full  │  │    │   │                                         │    │
│  │ 24V ═ Nom   │  │    │   │  Voltage RISING above 20V → Relay CLOSE │    │
│  │ 20V ═ Ready │  │    │   │  Voltage FALLING below 18V → Relay OPEN │    │
│  │ 18V ═ Low   │  │    │   │                                         │    │
│  │ 12V ═ Empty │  │    │   │  Hysteresis: 2V (prevents oscillation)  │    │
│  └─────────────┘  │    │   │                                         │    │
│                   │    │   │  Purpose: Prevents boot-loop when       │    │
│  (faceplate)      │    │   │  battery too low to sustain computer    │    │
└───────────────────┘    │   └─────────────────────────────────────────┘    │
                         │                         │                        │
                         └─────────────────────────┼────────────────────────┘
                                                   │
                                                   │ (load output, 18-32V)
                                                   ▼
                         ┌──────────────────────────────────────────────────┐
                         │              DPST KILL SWITCH                    │
                         │         (disconnects load path)                  │
                         │                                                  │
                         │   Toggle switch with safety guard                │
                         │   20A rating                                     │
                         │                                                  │
                         │   Position OFF = Complete isolation              │
                         │   Position ON = Power to protection chain        │
                         └──────────────────────────┬───────────────────────┘
                                                    │
                                                    │
                                                    ▼
                         ┌──────────────────────────────────────────────────┐
                         │              NTC THERMISTOR (5D-9)               │
                         │                                                  │
                         │   5Ω cold → <0.1Ω hot (self-heating)             │
                         │   Limits inrush current at switch-on             │
                         └──────────────────────────┬───────────────────────┘
                                                    │
                                                    ▼
    ┌───────────────────────────────────────────────────────────────────────┐
    │                        FUSE BLOCK (4-way ATO)                         │
    │                                                                       │
    │   Common Input ─────────────────────────────┐                         │
    │        │                                    │                         │
    │   ┌────┴────┐   ┌─────────┐   ┌─────────┐  ┌┴────────┐                │
    │   │  15A    │   │  10A    │   │   5A    │  │  SPARE  │                │
    │   │ BREAKER │   │  (Red)  │   │  (Tan)  │  │ (empty) │                │
    │   │ manual  │   │  aux    │   │  logic  │  │         │                │
    │   │ reset   │   │         │   │         │  │         │                │
    │   └────┬────┘   └────┬────┘   └────┬────┘  └─────────┘                │
    │        │             │            │                                   │
    │        │             │            └──► (future 5V branch)             │
    │        │             └──► (future aux loads)                          │
    │        │                                                              │
    │        └──► Main path continues below                                 │
    │                                                                       │
    └───────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ (15A breaker output)
                                    ▼
                         ┌──────────────────────────────────────────────────┐
                         │           INA219 POWER MONITOR                   │
                         │                                                  │
                         │   Vin+ ────┬───[0.1Ω SHUNT]───┬──── Vin-        │
                         │            │                  │                  │
                         │            │                  │                  │
                         │            └─────┬────────────┘                  │
                         │                  │ I2C                           │
                         │            ┌─────┴─────┐                         │
                         │            │ SDA → Pin 3                         │
                         │            │ SCL → Pin 5                         │
                         │            │ VCC → 3.3V                          │
                         │            │ GND → Negative bus                  │
                         │            └───────────┘                         │
                         │                                                  │
                         │   I2C Address: 0x40                              │
                         │   Measures: Voltage (18-32V), Current, Power     │
                         │   Software LVD: ark-power triggers shutdown <18V │
                         └──────────────────────────┬───────────────────────┘
                                                    │
                                                    ▼
                         ┌──────────────────────────────────────────────────┐
                         │         KSD9700 THERMAL CUTOFF (85°C)            │
                         │                                                  │
                         │   Normally-closed bimetallic switch              │
                         │   Opens at 85°C, resets at ~65°C                 │
                         │                                                  │
                         │   Mount: Bolted to aluminum deck plate           │
                         │   (thermal contact with OPi 5+ heatsink zone)    │
                         │                                                  │
                         │   Last-resort hardware protection                │
                         └──────────────────────────┬───────────────────────┘
                                                    │
                                                    │ (protected 18-32V)
                                                    ▼
                         ┌──────────────────────────────────────────────────┐
                         │          WIDE-INPUT DC-DC CONVERTER              │
                         │                                                  │
                         │   Input: 12-32V (matches Na-ion full range)      │
                         │   Output: 5V @ 5A (25W max)                      │
                         │   Efficiency: >90%                               │
                         │                                                  │
                         │   Handles voltage swing from empty (12V)         │
                         │   to full charge (32V) without adjustment        │
                         └──────────────────────────┬───────────────────────┘
                                                    │
                                                    │ 5V output [W15,W16]
                                                    │
         ┌──────────────────────────────────────────┼───────────────────────┐
         │                                          │                       │
         ▼                                          ▼                       ▼
┌─────────────────────┐              ┌─────────────────────┐    ┌───────────────────┐
│      OPi 5+         │              │     TPA2016D2       │    │    ReSpeaker      │
│    (via USB-C       │              │     Amplifier       │    │   Mic Array       │
│     breakout)       │              │                     │    │   (USB power)     │
│                     │              │   Audio amp for     │    │                   │
│  3-4W idle          │              │   speaker output    │    │   STT input       │
│  8-10W inference    │              │                     │    │                   │
│  12-15W peak        │              │   [W17,W18]         │    │                   │
└─────────────────────┘              └─────────────────────┘    └───────────────────┘

═══════════════════════════════════════════════════════════════════════════════
                           VOLTAGE THRESHOLD SUMMARY
═══════════════════════════════════════════════════════════════════════════════

  Battery Voltage    │  System State           │  Behavior
 ────────────────────┼─────────────────────────┼─────────────────────────────
  32.0V              │  Full charge            │  MPPT enters float mode
  26.4V              │  Float voltage          │  Maintenance charging
  24.0V              │  Nominal                │  Normal operation
  20.0V              │  Load disconnect CLOSE  │  System can boot/run
  18.0V              │  Load disconnect OPEN   │  ark-power graceful shutdown
  16.0V              │  Emergency mode         │  Text retrieval only (~3W)
  12.0V              │  Empty                  │  System offline, charging only
  0V                 │  Lazarus dormancy       │  Passive bootstrap activates

═══════════════════════════════════════════════════════════════════════════════
                              WIRE SEGMENT KEY
═══════════════════════════════════════════════════════════════════════════════

  [W05,W06]   Battery pack main leads to bus (10 AWG)
  [W15,W16]   DC-DC 5V output to USB-C breakout (18 AWG)
  [W17,W18]   DC-DC 5V output to TPA2016D2 amp (18 AWG)
  [W19,W20]   Solar XT60 to MPPT PV input (12 AWG)
  [W21,W22]   MPPT BAT output to battery bus (10 AWG)
  [W23]       Scavenger binding post to fuse (12 AWG)
  [W24,W25]   Scavenger fuse to diode to bus (12 AWG)

  Full wire schedule: See §6.8.2

═══════════════════════════════════════════════════════════════════════════════
```

**Key Architecture Changes from v2.x (LiFePO4/Victron):**

| Component | Old (v2.x) | New (v3.x Lazarus-Phoenix) |
|-----------|------------|----------------------------|
| Battery | LiFePO4 12V 100Ah | Na-ion 8S4P 24V 40Ah (0V-safe) |
| MPPT | Victron SmartSolar 75/15 (buck) | Genasun GVB-8-WP (boost) |
| LVD | Module @ 11.5V | Load Disconnect @ 18V/20V |
| DC-DC | Meanwell SD-15A-5 (9.2-18V in) | Wide-Input (12-32V in) |
| 0V Wake | Not supported | Supported (Genasun + bootstrap) |
| Voltmeter | Digital only | Analog 0-30V (zero silicon) |
| Decade Dormancy | Not designed for | Core design goal |

### 6.2 Wire Color Code

| Function | Color | AWG |
|----------|-------|-----|
| Battery + / 24V main | Red | 10 |
| Battery − / Negative | Black | 10 |
| Solar + | Red/White stripe | 12 |
| Solar − | Black/White stripe | 12 |
| 5V + | Orange | 18 |
| 5V − | Brown | 18 |
| Signal / GPIO | Blue | 22 |
| I2C SDA | Green | 22 |
| I2C SCL | Yellow | 22 |
| Audio | White | 22 |

### 6.3 Protection Chain Wiring Sequence

Wire in this exact order (power flows left to right):

```
Load Disconnect → Kill Switch → NTC → Fuse Block → INA219 → KSD9700 → DC-DC → Loads
```

**Load Disconnect:** First in chain after battery. TL431 + latching relay opens at <18V, closes at >20V. Prevents boot-loop when battery too low.

**NTC Thermistor Placement:** Immediately after kill switch, before fuse block. Limits inrush current when kill switch is closed.

**INA219 Placement:** After fuse block (15A breaker output). Monitors voltage/current via I2C. Software triggers graceful shutdown at <18V.

**KSD9700 Placement:** Inline with 24V+ between INA219 output and DC-DC input. Mount to aluminum deck near OPi 5+ heatsink so it senses actual component temperature.

### 6.4 I2C Bus Wiring

Single I2C bus (I2C1 on OPi 5+) connects:
- DS3231 RTC (address 0x68)
- OLED Display (address 0x3C)

**Wiring:**
```
OPi GPIO Pin 3 (SDA) ──┬── DS3231 SDA ──┬── OLED SDA ──┬── INA219 SDA
                       │                │              │
OPi GPIO Pin 5 (SCL) ──┼── DS3231 SCL ──┼── OLED SCL ──┼── INA219 SCL
                       │                │              │
OPi 3.3V ──────────────┼── DS3231 VCC ──┼── OLED VCC ──┼── INA219 VCC
                       │                │              │
OPi GND ───────────────┴── DS3231 GND ──┴── OLED GND ──┴── INA219 GND

**I2C Addresses:**
- DS3231 RTC: 0x68
- OLED SSD1306: 0x3C
- INA219 Power Monitor: 0x40
```

### 6.5 Audio Signal Path

```
OPi 3.5mm Jack → Ground Loop Isolator → TPA2016D2 Input → Speaker
```

**Ground Loop Isolator:** 3.5mm inline transformer. Eliminates buzz from ground loop between USB (ReSpeaker) and analog audio (amplifier) grounds.

**Power Sequencing:** The TPA2016D2 includes built-in soft-start (~10ms ramp) that prevents speaker pop on power-up. No external sequencing required. The ground loop isolator also blocks any DC offset during OPi boot. If additional control is desired, the SDZ (shutdown) pin can be connected to a GPIO and held low until the OS is ready, but this is optional.

### 6.6 D-pad & Button Wiring

**GPIO Pin Assignments (Orange Pi 5+ 40-pin header):**

| Control | Physical Pin | GPIO Name | gpiochip | Line | Pull | Active |
|---------|-------------|-----------|----------|------|------|--------|
| D-pad Up | 7 | GPIO1_C6 | 1 | 22 | Internal pull-up | LOW |
| D-pad Down | 11 | GPIO4_B2 | 4 | 10 | Internal pull-up | LOW |
| D-pad Left | 13 | GPIO4_B3 | 4 | 11 | Internal pull-up | LOW |
| D-pad Right | 15 | GPIO0_D4 | 0 | 28 | Internal pull-up | LOW |
| D-pad Center | 16 | GPIO1_D3 | 1 | 27 | Internal pull-up | LOW |
| ESC Button | 18 | GPIO1_D2 | 1 | 26 | Internal pull-up | LOW |
| Home Button | 22 | GPIO2_D4 | 2 | 28 | Internal pull-up | LOW |


**Power Button:** Uses hardware power button on OPi 5+ board (connected to PMIC). Software monitors `/dev/input` for power events.

**Pin Selection Rationale:**
- Avoids I2C5 (pins 3, 5) used for DS3231, OLED, INA219
- Avoids UART0 (pins 8, 10) reserved for debug console
- All selected pins are general-purpose GPIO without critical alt functions
- Pins span gpiochips 0, 1, 2, 4 — software must handle multiple chips

**gpiod Line Calculation:**
```
GPIO{bank}_{group}{pin} → chip = bank, line = (group × 8) + pin
Where: A=0, B=1, C=2, D=3

Example: GPIO1_C6 → chip=1, line=(2×8)+6=22
```

**Wiring Diagram:**
```
                    40-PIN HEADER (partial)
                    ┌─────────────────────┐
                    │  1 ○ ○ 2            │
                    │  3 ○ ○ 4            │ ← I2C5 SDA (avoid)
                    │  5 ○ ○ 6            │ ← I2C5 SCL (avoid)
           D-pad Up │  7 ● ○ 8            │
                GND │  9 ○ ○ 10           │
         D-pad Down │ 11 ● ○ 12           │
         D-pad Left │ 13 ● ○ 14           │ ← GND
        D-pad Right │ 15 ● ○ 16 ●         │ D-pad Center (PTT)
                    │ 17 ○ ○ 18 ●         │ ESC
                    │ 19 ○ ○ 20           │ ← GND
                    │ 21 ○ ○ 22 ●         │ HOME (TTS)
                    │    ...              │
                    │ 39 ○ ○ 40           │
                    └─────────────────────┘

● = Button GPIO (active-low with pull-up)
○ = Not used for buttons
```

**Button Wiring (each button):**
```
GPIO Pin ──┬── Button ── GND (pin 9, 14, or 20)
           │
           └── Internal pull-up enabled in software

Optional: Add external 10kΩ pull-up to 3.3V if internal pulls unreliable
```

**Hold Detection:**
- D-pad Center: 1000ms hold triggers PTT (Push-to-Talk voice input)
- Home: 1000ms hold triggers TTS (read response aloud)
- Software debounce: 50ms

### 6.7 Cable Management

- Route power cables (10-12 AWG) along case edges
- Route signal cables (18-22 AWG) separately from power
- Use spiral wrap for cable bundles
- Secure cables every 80-100mm with adhesive clips
- Leave 50mm service loop at each connection
- Label both ends of every cable with heat-shrink labels
- **Zero-hinge wiring:** No cables cross the lid hinge

### 6.8 Protection Chain Schematic & Wire Schedule

This section provides the complete electrical schematic for the Lazarus-Phoenix protection chain with labeled nodes, wire gauges, polarities, and test points.

**Architecture Summary:** Na-ion 8S4P battery (12V-32V range), Genasun GVB-8-WP boost MPPT, passive bootstrap for 0V recovery, TL431 + latching relay load disconnect (18V/20V thresholds), wide-input DC-DC (12-32V → 5V).

#### 6.8.1 Circuit Topology

**Node Reference:** Letters A-T identify connection points for wire schedule and test points.

```
═══════════════════════════════════════════════════════════════════════════════════
                           POWER SYSTEM SCHEMATIC
               Universal Knowledge Ark v3.2.0 — Lazarus-Phoenix
═══════════════════════════════════════════════════════════════════════════════════

                    SOLAR PATH                          SCAVENGER PATH
                    ─────────                           ──────────────
                        │                                     │
                   [XT60 Panel]                         [Binding Posts]
                   (faceplate)                           (faceplate)
                        │                                Red(+) Blk(-)
                        │ [S]                              │     │
                    ┌───┴───┐                              │     │
              PV+ ──┤       ├── PV-                        │     │
                    │       │                              │     │
                    │       │                              │     │
          ┌─────────┴───────┴─────────┐                    │     │
          │                           │             ┌──────┴─┐   │
          │    PASSIVE BOOTSTRAP      │             │15A ATO │   │
          │    (0V recovery path)     │             │ FUSE   │   │
          │                           │             │(scav)  │   │
          │  PV+ ──┬── 1N5408 ───┐    │             └───┬────┘   │
          │        │   (diode)   │    │          [A]    │        │
          │        │             │    │                 │        │
          │        │         47Ω 25W  │            ┌────┴────┐   │
          │        │         resistor │            │15SQ045  │   │
          │        │             │    │            │SCHOTTKY │   │
          │        │             ▼    │            │ ───|>── │   │
          │        │        To BAT+   │            │(cathode │   │
          │        │        (trickle) │            │ to bus) │   │
          │        │                  │            └────┬────┘   │
          │        │                  │          [B]    │        │
          │        │                  │                 │        │
          │    ┌───┴───────────┐      │                 │        │
          │    │   GENASUN     │      │                 │        │
          │    │  GVB-8-WP     │      │                 │        │
          │    │               │      │                 │        │
          │    │  Boost MPPT   │      │                 │        │
          │    │  18V → 32V    │      │                 │        │
          │    │               │      │                 │        │
          │    │  Custom:      │      │                 │        │
          │    │  32.0V abs    │      │                 │        │
          │    │  26.4V float  │      │                 │        │
          │    │               │      │                 │        │
          │    │  0V wake: YES │      │                 │        │
          │    │               │      │                 │        │
          │    │  BAT+ BAT-    │      │                 │        │
          │    └───┬─────┬─────┘      │                 │        │
          │        │     │            │                 │        │
          └────────┼─────┼────────────┘                 │        │
                   │     │                              │        │
                   │     │                              │        │
                   ▼     │                              │        │
    ┌──────────────────────────────────────────────────────────────────────┐
    │                         BATTERY BUS [C]                              │
    │                                                                      │
    │   ┌──────────────────────────────────────────────────────────────┐  │
    │   │                    NA-ION 8S4P PACK                          │  │
    │   │                                                              │  │
    │   │   HiNa 32140 Polyanion Cells (32 total)                      │  │
    │   │   Configuration: 8S4P (40Ah / 960Wh)                         │  │◄─┘
    │   │   Voltage: 12V (empty) ── 24V (nom) ── 32V (full)            │  │
    │   │                                                              │  │
    │   │   + JK BMS BD4A8S4P (active balancing, Bluetooth)            │  │
    │   │   + 0V storage safe (aluminum collectors)                    │  │
    │   │                                                              │  │
    │   │              (+)────────────────(−)                          │  │
    │   │               │ XT60 connector  │                            │  │
    │   └───────────────┼─────────────────┼────────────────────────────┘  │
    │                   │                 │                               │
    └───────────────────┼─────────────────┼───────────────────────────────┘
              [C]       │ BAT+            │ BAT-
                        │                 │
        ┌───────────────┼─────────────────┼───────────────────────────────┐
        │               │                 │                               │
        │               │    ════════════════════════════════════════     │
        │               │    ║      NEGATIVE BUS (STAR TOPOLOGY)     ║    │
        │               │    ║  All negatives to single point [R]    ║◄───┘
        │               │    ║  6-position barrier terminal block    ║
        │               │    ════════════════════════════════════════
        │               │                                           [R] GND
        │               │
        │   ┌───────────┴───────────────────────┐
        │   │                                   │
        │   ▼                                   ▼
        │  ┌──────────────────────┐    ┌────────────────────────────────────┐
        │  │  ANALOG VOLTMETER    │    │       LOAD DISCONNECT              │
        │  │      0-30V DC        │    │                                    │
        │  │   (moving coil)      │    │   TL431 Precision Shunt            │
        │  │                      │    │   + Resistor Divider               │
        │  │  Zero silicon        │    │   + Panasonic ADW1124HLW           │
        │  │  feedback path       │    │     Latching Relay                 │
        │  │                      │    │                                    │
        │  │  ┌─────────────┐     │    │   ┌────────────────────────────┐   │
        │  │  │ 32V ═ Full  │     │    │   │  V > 20V → Relay CLOSES    │   │
        │  │  │ 24V ═ Nom   │     │    │   │  V < 18V → Relay OPENS     │   │
        │  │  │ 20V ═ Ready │     │    │   │  Hysteresis: 2V            │   │
        │  │  │ 18V ═ Low   │     │    │   │                            │   │
        │  │  │ 12V ═ Empty │     │    │   │  Prevents boot-loop when   │   │
        │  │  └─────────────┘     │    │   │  battery too low           │   │
        │  │                      │    │   └────────────────────────────┘   │
        │  │  (faceplate) [T]     │    │                                    │
        │  └──────────────────────┘    │   Resistor Divider:                │
        │                              │   R1 = 70kΩ (BAT+ to TL431 ref)    │
        │                              │   R2 = 10kΩ (TL431 ref to GND)     │
        │                              │                                    │
        │                              │   Relay contacts: NO/C/NC          │
        │                              │   Load path through NO contact     │
        │                              └──────────────┬─────────────────────┘
        │                                       [D]   │ (load output)
        │                                             │
        └─────────────────────────────────────────────┤
                                                      │
                                                      ▼
                         ┌──────────────────────────────────────────────────┐
                         │              DPST KILL SWITCH                    │
                         │         (disconnects load path)                  │
                         │                                                  │
                         │   ┌─────┐        ┌─────┐                         │
                         │───┤ SW1 ├────────┤ SW2 ├───                      │
                         │   └──┬──┘        └──┬──┘                         │
                         │      │              │                            │
                         │  LOAD+ in      (unused pole)                     │
                         │  LOAD+ out                                       │
                         │                                                  │
                         │   Toggle switch with safety guard, 20A rating    │
                         └──────────────────────────┬───────────────────────┘
                                              [E]   │
                                                    │
                                                    ▼
                         ┌──────────────────────────────────────────────────┐
                         │              NTC THERMISTOR (5D-9)               │
                         │                                                  │
                         │   ┌─────────────────┐                            │
                         │───┤   5Ω cold       ├───                         │
                         │   │  <0.1Ω hot      │                            │
                         │   │  (inrush limit) │                            │
                         │   └─────────────────┘                            │
                         │                                                  │
                         │   Limits inrush current when switch first closed │
                         └──────────────────────────┬───────────────────────┘
                                              [F]   │
                                                    │
                                                    ▼
    ┌───────────────────────────────────────────────────────────────────────────┐
    │                         FUSE BLOCK (4-way ATO)                            │
    │  ┌─────────────────────────────────────────────────────────────────────┐  │
    │  │                                                                     │  │
    │  │    COMMON INPUT ──────────────────────────────────┐                 │  │
    │  │         │                                         │                 │  │
    │  │    ┌────┴────┐  ┌─────────┐  ┌─────────┐  ┌──────┴───┐              │  │
    │  │    │ 15A     │  │ 10A     │  │ 5A      │  │ SPARE    │              │  │
    │  │    │ BREAKER │  │ RED     │  │ TAN     │  │ (empty)  │              │  │
    │  │    │(manual  │  │(aux)    │  │(logic)  │  │          │              │  │
    │  │    │ reset)  │  │         │  │         │  │          │              │  │
    │  │    └────┬────┘  └────┬────┘  └────┬────┘  └──────────┘              │  │
    │  │   [G]   │       [H]  │       [I]  │                                 │  │
    │  │         │             │           │                                 │  │
    │  │         │             │           └──► (5V branch - future)         │  │
    │  │         │             │                                             │  │
    │  │         │             └──► (aux loads - future)                     │  │
    │  │         │                                                           │  │
    │  │         └──► Main 24V path continues below                          │  │
    │  │                                                                     │  │
    │  └───────────────────────────── CHASSIS GROUND [R] ───────────────────┘  │
    │                                                                          │
    └──────────────────────────────────────────────────────────────────────────┘
                    │
              [G]   │ (15A breaker output)
                    │
                    ▼
    ┌────────────────────────────────────┐
    │    INA219 POWER MONITOR            │
    │    (High-side current sensing)     │
    │                                    │
    │  Vin+ ─────┬───[0.1Ω]───┬───── Vin-│
    │            │   SHUNT    │          │
    │     [J]    │            │    [K]   │
    │            │            │          │
    │            └──┬─────────┘          │
    │               │                    │
    │               │ I2C                │
    │            ┌──┴──┐                 │
    │            │ SDA │──► OPi Pin 3    │
    │            │ SCL │──► OPi Pin 5    │
    │            │ VCC │──► 3.3V         │
    │            │ GND │──► GND [R]      │
    │            └─────┘                 │
    │                                    │
    │  I2C Address: 0x40                 │
    │  Measures: Voltage (18-32V),       │
    │            Current, Power          │
    │  Software LVD: ark-power triggers  │
    │  graceful shutdown at <18V         │
    └───────────────┬────────────────────┘
              [K]   │ (INA219 Vin- output)
                    │
                    ▼
    ┌────────────────────────────────────┐
    │    KSD9700 THERMAL CUTOFF          │
    │                                    │
    │     ┌─────────────────┐            │
    │  ───┤ NC    COM       ├───         │
    │     │  \    /         │            │
    │     │   \  /  (closed │            │
    │     │    \/    <85°C) │            │
    │     │                 │            │
    │     │  Opens at 85°C  │            │
    │     │  Closes at ~65°C│            │
    │     └─────────────────┘            │
    │                                    │
    │  Mount: Bolted to deck plate       │
    │  (senses OPi heatsink temp)        │
    │  Last-resort hardware protection   │
    └───────────────┬────────────────────┘
              [L]   │ (thermal-protected 18-32V)
                    │
                    ▼
    ┌────────────────────────────────────┐
    │    WIDE-INPUT DC-DC CONVERTER      │
    │                                    │
    │     ┌─────────────────────┐        │
    │  ───┤ +Vin         +Vout  ├───     │  → 5V to loads
    │     │ (12-32V)     (5V)   │        │
    │     │                     │        │
    │     │  12-32V → 5V @ 5A   │        │
    │     │  Efficiency >90%    │        │
    │     │                     │        │
    │     │  Handles full Na-ion│        │
    │     │  voltage range      │        │
    │     │                     │        │
    │  ───┤ -Vin         -Vout  ├───     │  → GND
    │     └─────────────────────┘        │
    │                                    │
    └──────────┬─────────────────┬───────┘
         [M]   │ +5V       [N]   │ GND
               │                 │
               ▼                 ▼
    ┌─────────────────────────────────────────────────────────┐
    │                   5V DISTRIBUTION                       │
    │                                                         │
    │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
    │  │  USB-C       │  │  TPA2016D2   │  │  ReSpeaker   │   │
    │  │  Breakout    │  │  Amplifier   │  │  (via USB)   │   │
    │  │              │  │              │  │              │   │
    │  │  [O] → OPi   │  │  [P]         │  │  [Q]         │   │
    │  └──────────────┘  └──────────────┘  └──────────────┘   │
    │                                                         │
    └─────────────────────────────────────────────────────────┘

═══════════════════════════════════════════════════════════════════════════════════
                            POLARITY REFERENCE
═══════════════════════════════════════════════════════════════════════════════════

• Schottky Diode (15SQ045): Anode (A) ← Scavenger+, Cathode (K) → Battery Bus
  - TO-220 package: TAB = CATHODE
  - Forward drop: ~0.45V @ 10A
  - Blocks reverse current (battery → scavenger)

• Bootstrap Diode (1N5408): Anode ← PV+, Cathode → 47Ω resistor → BAT+
  - Silicon (NOT Schottky) for low reverse leakage
  - Allows trickle charge at 0V battery, blocks backfeed in dark

• TL431 Shunt Regulator: Cathode/Anode/Ref pinout
  - Ref pin receives resistor divider (sets 18V/20V thresholds)
  - Controls latching relay coil

• Latching Relay (ADW1124HLW): SET/RESET/COM/NO/NC pins
  - SET pulse closes relay (>20V detected)
  - RESET pulse opens relay (<18V detected)
  - Load path through COM-NO contact

• NTC Thermistor (5D-9): Non-polarized, either lead to either side

• KSD9700: Non-polarized thermal switch (normally closed)

• INA219: Vin+ toward source (fuse block), Vin- toward load (KSD9700)
  - Current flows through onboard 0.1Ω shunt
  - Positive current = discharge, Negative current = charge

• Analog Voltmeter: Observe +/− markings on meter terminals
  - Connect directly to battery bus (no isolation needed for moving-coil)

═══════════════════════════════════════════════════════════════════════════════════
```

#### 6.8.2 Complete Wire Schedule

Every wire segment in the protection chain, listed in order of installation.

**Power Path (18-32V) - Main Protection Chain:**

| Seg | From Node | To Node | AWG | Color | Length | From Term | To Term | Notes |
|-----|-----------|---------|-----|-------|--------|-----------|---------|-------|
| W01 | Na-ion Pack+ | Battery bus [C] | 10 | Red | 150mm | XT60 | Ring 10mm | Pack positive |
| W02 | Na-ion Pack- | Negative bus [R] | 10 | Black | 150mm | XT60 | Ferrule | Pack negative |
| W03 | Battery bus [C] | Load disconnect in | 10 | Red | 100mm | Ring 10mm | Ferrule | To TL431 circuit |
| W04 | Load disconnect out [D] | Kill SW in | 10 | Red | 80mm | Ferrule | Spade 6.3mm | Protected output |
| W05 | Kill SW out [E] | NTC in | 10 | Red | 80mm | Spade 6.3mm | Solder | Short jumper |
| W06 | NTC out [F] | Fuse block in | 10 | Red | 100mm | Solder | Ferrule | To common input |
| W07 | 15A breaker out [G] | INA219 Vin+ | 12 | Red | 100mm | Ferrule | Screw term | To power monitor |
| W08 | INA219 Vin- [K] | KSD9700 | 12 | Red | 80mm | Screw term | Solder | Through shunt |
| W09 | KSD9700 [L] | DC-DC +Vin | 12 | Red | 100mm | Solder | Ferrule | Thermal protected |
| W10 | DC-DC -Vin | Negative bus [R] | 12 | Black | 100mm | Ferrule | Ferrule | DC-DC ground |

**Load Disconnect Circuit (TL431 + Latching Relay):**

| Seg | From Node | To Node | AWG | Color | Length | From Term | To Term | Notes |
|-----|-----------|---------|-----|-------|--------|-----------|---------|-------|
| W11 | Battery bus [C] | R1 (70kΩ) | 18 | Red | 50mm | Ferrule | Solder | Divider high side |
| W12 | R1/R2 junction | TL431 Ref | 18 | Red | 30mm | Solder | Solder | Threshold sense |
| W13 | R2 (10kΩ) | Negative bus [R] | 18 | Black | 50mm | Solder | Ferrule | Divider low side |
| W14 | TL431 Cathode | Relay SET coil | 18 | Red | 50mm | Solder | Solder | >20V trigger |
| W15 | TL431 Anode | Negative bus [R] | 18 | Black | 50mm | Solder | Ferrule | TL431 ground |
| W16 | Relay COM | Battery bus [C] | 10 | Red | 80mm | Spade 4.8mm | Ring 10mm | Load input |
| W17 | Relay NO | Load disconnect out [D] | 10 | Red | 80mm | Spade 4.8mm | Ferrule | Load output |

**Analog Voltmeter (0-30V Moving Coil):**

| Seg | From Node | To Node | AWG | Color | Length | From Term | To Term | Notes |
|-----|-----------|---------|-----|-------|--------|-----------|---------|-------|
| W18 | Battery bus [C] | Voltmeter + [T] | 18 | Red | 200mm | Ring 10mm | Spade 2.8mm | Meter positive |
| W19 | Negative bus [R] | Voltmeter - [T] | 18 | Black | 200mm | Ferrule | Spade 2.8mm | Meter negative |

**Power Path (5V) - Load Distribution:**

| Seg | From Node | To Node | AWG | Color | Length | From Term | To Term | Notes |
|-----|-----------|---------|-----|-------|--------|-----------|---------|-------|
| W20 | DC-DC +Vout [M] | USB-C breakout + | 18 | Orange | 150mm | Ferrule | Screw term | Main 5V |
| W21 | DC-DC -Vout [N] | USB-C breakout - | 18 | Brown | 150mm | Ferrule | Screw term | 5V ground |
| W22 | DC-DC +Vout | TPA2016 VIN | 18 | Orange | 200mm | Ferrule | Solder | Amp power |
| W23 | DC-DC -Vout | TPA2016 GND | 18 | Brown | 200mm | Ferrule | Solder | Amp ground |

**Solar Input Path (Genasun GVB-8-WP Boost MPPT):**

| Seg | From Node | To Node | AWG | Color | Length | From Term | To Term | Notes |
|-----|-----------|---------|-----|-------|--------|-----------|---------|-------|
| W24 | XT60+ (panel) [S] | Genasun PV+ | 12 | Red/White | 300mm | XT60 | Ferrule | Solar positive (18V nom) |
| W25 | XT60- (panel) | Genasun PV- | 12 | Blk/White | 300mm | XT60 | Ferrule | Solar negative |
| W26 | Genasun BAT+ | Battery bus [C] | 10 | Red | 200mm | Ferrule | Ring 10mm | Boost output (32V max) |
| W27 | Genasun BAT- | Negative bus [R] | 10 | Black | 200mm | Ferrule | Ferrule | MPPT ground |

**Passive Bootstrap Path (0V Recovery):**

| Seg | From Node | To Node | AWG | Color | Length | From Term | To Term | Notes |
|-----|-----------|---------|-----|-------|--------|-----------|---------|-------|
| W28 | Genasun PV+ | 1N5408 Anode | 18 | Red/White | 50mm | Ferrule | Solder | Bootstrap diode input |
| W29 | 1N5408 Cathode | 47Ω 25W resistor | 18 | Red | 50mm | Solder | Solder | Current limiting |
| W30 | 47Ω resistor | Battery bus [C] | 18 | Red | 80mm | Solder | Ring 10mm | Trickle to pack |

**Scavenger Input Path:**

| Seg | From Node | To Node | AWG | Color | Length | From Term | To Term | Notes |
|-----|-----------|---------|-----|-------|--------|-----------|---------|-------|
| W31 | Binding post+ | 15A fuse (scav) | 12 | Red | 150mm | Banana stud | Ferrule | Scavenger in |
| W32 | 15A fuse out [A] | Schottky anode | 12 | Red | 80mm | Ferrule | Spade 6.3mm | Fused scav |
| W33 | Schottky cathode [B] | Battery bus [C] | 12 | Red | 100mm | Spade 6.3mm | Ring 10mm | Protected input |
| W34 | Binding post- | Negative bus [R] | 12 | Black | 150mm | Banana stud | Ferrule | Scavenger gnd |

**I2C Bus (INA219):**

| Seg | From Node | To Node | AWG | Color | Length | From Term | To Term | Notes |
|-----|-----------|---------|-----|-------|--------|-----------|---------|-------|
| W35 | INA219 SDA | OPi Pin 3 | 22 | Green | 200mm | Header | Dupont | I2C data |
| W36 | INA219 SCL | OPi Pin 5 | 22 | Yellow | 200mm | Header | Dupont | I2C clock |
| W37 | INA219 VCC | OPi 3.3V | 22 | Red | 200mm | Header | Dupont | Logic power |
| W38 | INA219 GND | OPi GND | 22 | Black | 200mm | Header | Dupont | Logic ground |

#### 6.8.3 Negative Bus Implementation

**Star Topology:** All negative/return connections terminate at a single point to prevent ground loops and ensure consistent 0V reference. Note: This is a **floating DC system** (no earth ground) — the Pelican case is non-conductive and Faraday-shielded.

**Negative Bus Hardware:**

| Parameter | Value |
|-----------|-------|
| Component | 6-position 30A barrier terminal block |
| Positions | 6 screw terminals (expandable with additional blocks) |
| Rating | 30A per position |
| Wire Range | 10-18 AWG |
| Dimensions | ~45×15×15mm |
| Mounting | 2× M3 screws to deck underside, adjacent to fuse block |
| Location | Deck underside, near fuse block |

```
                    NEGATIVE BUS (Star Topology)
                    ══════════════════════════════
                              │
                    ┌─────────┴─────────┐
                    │  6-POSITION        │
                    │  BARRIER TERMINAL  │
                    │  BLOCK (30A)       │
                    │       [R]          │
                    └─────────┬─────────┘
                              │
    Position:   1     2       3       4       5       6
                │     │       │       │       │       │
                ▼     ▼       ▼       ▼       ▼       ▼
             Pack-  MPPT-   DC-DC-  Scav-   TL431-  Deck
             (W02)  (W27)   (W10)   (W34)   (W15)   Strap
```

**Wiring Procedure:**
1. Mount barrier terminal block to deck underside with 2× M3 screws
2. Strip each negative wire to 8-10mm, crimp ferrule
3. Insert ferrules into screw terminals in order shown above
4. Torque each screw to 0.5 Nm (firm, but not overtightened)
5. Apply dielectric grease to exposed terminals
6. Verify continuity between all positions (<0.1Ω)

**Terminal Assignments:**

| Position | Wire ID | Source | AWG | Termination |
|----------|---------|--------|-----|-------------|
| 1 | W02 | Na-ion Pack negative | 10 | Ferrule 6.0mm² |
| 2 | W27 | Genasun BAT- | 10 | Ferrule 6.0mm² |
| 3 | W10 | DC-DC -Vin | 12 | Ferrule 4.0mm² |
| 4 | W34 | Scavenger binding post negative | 12 | Ferrule 4.0mm² |
| 5 | W15 | TL431 Anode (load disconnect) | 18 | Ferrule 1.0mm² |
| 6 | — | Deck grounding strap | 10 | Ring terminal M4 + jumper |

#### 6.8.4 Test Points & Expected Values

**Multimeter Test Points (referenced to Negative Bus [R]):**

| Test Point | Node | Expected Value (Full Charge) | Expected Value (Low Battery) | Test Condition |
|------------|------|------------------------------|------------------------------|----------------|
| TP1 | [C] Battery Bus | 30-32V | 18-20V | Kill switch OFF |
| TP2 | [T] Analog Voltmeter | 30-32V | 18-20V | Visual confirmation |
| TP3 | [D] Load Disconnect out | 30-32V | 0V (relay open) | Relay state |
| TP4 | [E] Kill SW out | 30-32V | 0V (relay open) | Kill switch ON |
| TP5 | [F] NTC out | 30-32V | 0V | After 10s warm-up |
| TP6 | [G] Breaker out | 30-32V | 0V | Breaker not tripped |
| TP7 | [K] INA219 Vin- | 30-32V | 0V | Through shunt |
| TP8 | [L] KSD9700 out | 30-32V | 0V | Temp <85°C |
| TP9 | [M] DC-DC +Vout | 4.9-5.1V | 0V (no input) | Under load |
| TP10 | [A] Scav fuse out | 0V (no source) | External voltage | Scavenger connected |
| TP11 | [B] Schottky out | Battery voltage | Battery voltage | Diode blocks backfeed |
| TP12 | [S] Solar XT60+ | 0-22V | Panel Voc | Panel connected |

**Lazarus-Phoenix Voltage Reference:**

| State | Voltage | Meaning |
|-------|---------|----------|
| Full | 32V | 8S × 4.0V/cell (absorption complete) |
| Float | 26.4V | 8S × 3.3V/cell (maintenance charge) |
| Nominal | 24V | 8S × 3.0V/cell (normal operation) |
| Ready | 20V | Load disconnect closes (system boots) |
| Low | 18V | Load disconnect opens (shutdown) |
| Empty | 12V | 8S × 1.5V/cell (fully discharged) |

**Continuity Tests (Kill Switch OFF, Relay Open):**

| Test | Probe 1 | Probe 2 | Expected | Notes |
|------|---------|---------|----------|-------|
| CT1 | Na-ion Pack+ | Battery Bus [C] | Continuity | Pack connected |
| CT2 | Na-ion Pack- | Negative Bus [R] | Continuity | Ground path |
| CT3 | [D] Load Disconnect out | [C] Battery Bus | Open | Relay open (<20V) |
| CT4 | [D] Load Disconnect out | [C] Battery Bus | Continuity | Relay closed (>20V) |
| CT5 | [L] KSD9700 out | [K] INA219 out | Continuity | Thermal switch closed |
| CT6 | Binding post+ | [B] Schottky out | Open (diode) | Reverse blocked |
| CT7 | Bootstrap diode anode | Genasun PV+ | Continuity | Bootstrap path intact |

**NTC Thermistor Test:**

| Condition | Resistance | Notes |
|-----------|------------|-------|
| Cold (25°C) | 4-6Ω | Before power-on |
| Warm (after 30s) | <0.5Ω | Self-heated, low impedance |

#### 6.8.5 Fuse Coordination & Trip Order

**Design Intent:** Protection layers should activate in order from most specific to most general, allowing targeted fault isolation. The Lazarus-Phoenix architecture uses a hardware load disconnect (TL431 + latching relay) as primary low-voltage protection, with software (ark-power via INA219) as secondary.

| Fault Condition | First to Trip | Rating | Second to Trip | Notes |
|-----------------|---------------|--------|----------------|-------|
| 5V short circuit | DC-DC internal | 5A | 15A breaker | DC-DC has internal protection |
| Scavenger overcurrent | 15A scav fuse | 15A | — | Isolates external fault |
| Main load overcurrent | 15A breaker | 15A | Battery BMS | Breaker is resettable |
| Battery dead short | Battery BMS | 50-100A | — | JK BMS protects cells |
| Thermal runaway | KSD9700 | 85°C | — | Opens before damage |
| Low battery (hardware) | Load disconnect relay | 18V | Battery BMS ~10V | Prevents boot-loop |
| Low battery (software) | ark-power shutdown | <18V | Load disconnect | Graceful OS shutdown |

**Protection Coordination Timeline:**

```
Fault Occurs
    │
    ├─► 0ms:    Battery BMS reacts to short circuit (>100A)
    │
    ├─► 10ms:   15A breaker trips on sustained overcurrent
    │
    ├─► 100ms:  KSD9700 opens if temperature exceeds 85°C
    │
    ├─► 500ms:  ark-power initiates graceful shutdown at <18V (via INA219)
    │
    ├─► 1s:     Load disconnect relay opens at 18V (hardware cutoff)
    │
    └─► 5s:     DC-DC thermal shutdown if overloaded
```

**Load Disconnect Behavior:**

| Battery Voltage | Relay State | System State |
|-----------------|-------------|---------------|
| >20V | CLOSES | System boots, normal operation |
| 18V-20V | UNCHANGED | Hysteresis band, maintains current state |
| <18V | OPENS | System shutdown, prevents boot-loop |
| <12V | OPEN | Deep discharge, awaiting solar recovery |

#### 6.8.6 Schottky Diode Mounting Detail

**15SQ045 TO-220 Package:**

```
          FRONT VIEW              SIDE VIEW (mounted)
        ┌───────────┐            ┌────────────────┐
        │           │            │  Diode body    │
        │  15SQ045  │            │    ┌────┐      │
        │           │            │    │    │      │
        │   ───|>── │            │    │    │      │
        │ A      K  │            └────┴────┴──────┘
        │ (anode)   │                 │    │
        │    (cathode = TAB)          │    └─ M3 screw
        └───────────┘                 │       to deck
            │ │ │                     │
            │ │ │                     └─ Thermal pad
            │ │ │                        to aluminum
        Leads (bend 90°)
```

**Mounting Procedure:**
1. Apply thermal paste to diode tab
2. Position on aluminum deck plate (clear of other components)
3. Secure with M3 × 6mm screw + lock washer
4. Torque to 0.5 Nm
5. Bend leads 90° toward scavenger fuse and battery bus
6. Attach spade terminals to leads
7. Heat shrink exposed connections

**Thermal Dissipation:**
- Max power @ 10A: 0.45V × 10A = 4.5W
- Deck plate provides thermal mass
- No additional heatsink required for intermittent scavenger use

#### 6.8.7 Assembly Checklist for Protection Chain

**Before Applying Power:**

- [ ] All ferrules properly crimped (no exposed strands)
- [ ] Spade terminals fully seated
- [ ] Ring terminals on correct size studs
- [ ] Schottky diode polarity correct (tab = cathode toward battery)
- [ ] Bootstrap diode (1N5408) polarity correct (band toward resistor)
- [ ] Bootstrap resistor (47Ω 25W) properly mounted with ventilation
- [ ] NTC thermistor inline after kill switch
- [ ] Load disconnect wiring correct:
  - [ ] R1 (70kΩ) between BAT+ and TL431 Ref
  - [ ] R2 (10kΩ) between TL431 Ref and GND
  - [ ] TL431 Cathode to relay SET coil
  - [ ] Relay COM from BAT+, NO to load path
- [ ] Analog voltmeter connected to battery bus (observe +/- polarity)
- [ ] INA219 Vin+ toward fuse block, Vin- toward KSD9700
- [ ] KSD9700 mounted with thermal contact to deck
- [ ] Negative bus star topology (all negatives to single point [R])
- [ ] No exposed copper on any connection
- [ ] Cable ties securing harness (no stress on terminals)
- [ ] Kill switch in OFF position

**First Power-On Sequence:**

1. Kill switch OFF
2. Connect Na-ion pack via XT60 to battery bus
3. Measure TP1: Should read pack voltage (18-32V depending on SoC)
4. Verify analog voltmeter [T] displays same reading
5. Verify load disconnect relay is:
   - OPEN if pack <20V (wait for solar charging)
   - CLOSED if pack >20V
6. Turn kill switch ON
7. If relay CLOSED: Measure TP4, TP5, TP6: Should match TP1
8. Measure TP7, TP8: Should match TP1
9. Measure TP9 (5V): Should read 4.9-5.1V
10. Connect OPi 5+ USB-C
11. Verify boot

**0V Recovery Test (Optional — for dormancy validation):**

1. Disconnect pack from battery bus
2. Connect solar panel to XT60 [S]
3. Verify bootstrap path: measure voltage across 47Ω resistor
4. Should see trickle current (~0.3-0.5A at 18V panel)
5. Reconnect pack — should charge normally via Genasun

---

## 7. Final Assembly

### 7.1 Integration Sequence

**Phase 1: Faraday Shielding**
1. Install Faraday fabric in base
2. Install Faraday fabric in lid
3. Seal all seams with 3M 1181 tape
4. Test with smartphone (must go to voicemail)

**Phase 2: Basement Layer**
1. Cut Kaizen foam for battery pockets (see §5.3)
2. Place battery pack in pocket
3. Cut Kaizen foam for MPPT pocket (see §5.6) — no airflow slots needed for Genasun
4. Verify Genasun custom voltage programming (32.0V/26.4V) before installation
5. Install Genasun GVB-8-WP in foam pocket, screw terminals facing up
6. Wire battery ↔ MPPT (BAT+/BAT- to battery bus)
7. Wire solar input (XT60) to MPPT PV terminals
8. Assemble passive bootstrap circuit (see §5.7):
   - Mount 47Ω 25W resistor to deck with thermal contact
   - Wire 1N5408 diode + resistor (W28-W30)
   - Connect to Genasun PV+ and battery bus
9. Test: Apply 18V to PV terminals — verify controller powers from solar (0V battery wake)
10. Test: Verify bootstrap path continuity and resistor thermal mounting (per §5.7.4)
11. Assemble Load Disconnect PCB (see §5.8):
    - Populate 50×70mm perfboard with resistor dividers, TL431s, transistors, capacitors
    - Verify component values and orientations before soldering
12. Mount ADW1124HLW latching relay to deck near DC-DC location
13. Test: Bench PSU test of LVD circuit — verify SET at ~20V, RESET at ~17V (per §5.8.5)

**Phase 3: Deck Construction**
1. Cut deck plate to size
2. Drill mounting holes for all components
3. Install PVC support posts
4. Mount deck loosely (for wiring access)

**Phase 4: Power Distribution (Deck Underside)**
1. Mount fuse block
2. Install 15A manual reset breaker
3. Mount LVD module
4. Mount KSD9700 (thermal contact with deck)
5. Wire NTC thermistor inline
6. Mount Meanwell DC-DC
7. Complete power wiring per §6.1
8. Test: Measure 5V output with kill switch on

**Phase 5: Compute (Deck Underside)**
1. Mount OPi 5+ with standoffs (inverted)
2. Install pyrolytic graphite pad on SoC
3. Attach heatsink (ensure deck contact)
4. Install NVMe SSD in M.2 slot
5. Mount USB-C breakout board
6. Wire 5V to USB-C breakout
7. Test: Boot OPi 5+, verify NVMe detected

**Phase 6: Peripherals (Deck Underside)**
1. Mount TPA2016D2 amplifier
2. Wire audio: OPi → isolator → amp → speaker
3. Mount DS3231 RTC
4. Wire I2C bus (RTC + OLED)
5. Mount OLED behind faceplate window
6. Test: I2C scan shows 0x68 and 0x3C

**Phase 7: Deck Faceplate**
1. Install industrial D-pad
2. Install Power, ESC, Home buttons
3. Wire all controls to OPi GPIO
4. Install speaker behind grill
5. Install ReSpeaker mic array (with silicone gasket)
6. Install XT60 panel mount
7. Install binding posts
8. Install USB-A panel mounts (angled)
9. Test: Each control triggers GPIO, audio plays

**Phase 8: Lid Assembly**
1. Mount E-ink display
2. Mount IT8951 controller
3. Install storm window (with air gap)
4. Install USB panel mount
5. Route coiled USB cable in cavity
6. Test: Display shows test pattern via USB

**Phase 9: Final Integration**
1. Place keyboard + solar panels in cavity
2. Secure deck with thumbscrews
3. Install desiccant pouches (3×)
4. Stock lid foam with spares
5. Close case, verify latches
6. Final water test

### 7.2 Torque Specifications

| Fastener | Torque | Notes |
|----------|--------|-------|
| Pelican case latches | Hand tight | Do not overtighten |
| M2.5 electronics | 0.3 Nm | Anti-vibration compound |
| M3 panel mounts | 0.5 Nm | With lock washer |
| M4 deck thumbscrews | Hand tight | Tool-free by design |
| M4 structural | 0.8 Nm | Loctite Blue |
| Binding posts | 1.0 Nm | Until gasket compresses |
| XT60 panel nut | 1.0 Nm | Do not over-tighten |

### 7.3 Desiccant Installation

1. Fill 3× mesh pouches with indicating silica gel
2. Verify beads are orange (dry)
3. Place pouches:
   - 1× in basement near battery
   - 1× in deck underside near electronics
   - 1× in lid foam
4. Note installation date for maintenance tracking

### 7.4 Lid Foam Spare Parts Packing

**Lid Foam Layout Diagram:**

```
┌─────────────────────────────────────────────────────────────────┐
│                        LID FOAM (top view)                      │
│                                                                 │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                                                         │   │
│   │                    CENTER POCKET                        │   │
│   │              (Cold spare NVMe in ESD bag)               │   │
│   │                                                         │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│   ┌───────────────────┐  ┌───────────────────────────────────┐  │
│   │   MEDIUM POCKET   │  │          SIDE POCKET              │  │
│   │                   │  │                                   │  │
│   │  Wago 221 kit     │  │  GPS dongle (ESD)                 │  │
│   │                   │  │  USB LED gooseneck                │  │
│   │                   │  │  Spare USB-C cable (rolled)       │  │
│   │                   │  │  Alligator whip (coiled)          │  │
│   │                   │  │                                   │  │
│   └───────────────────┘  └───────────────────────────────────┘  │
│                                                                 │
│   ┌───────────────────┐  ┌───────────────────────────────────┐  │
│   │   SMALL POCKET    │  │          SMALL POCKET             │  │
│   │                   │  │                                   │  │
│   │  CR2032 ×4        │  │  Spare desiccant (Mylar)          │  │
│   │  Fuses (10A, 5A)  │  │  Dielectric grease tube           │  │
│   │                   │  │                                   │  │
│   └───────────────────┘  └───────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

| Item | Location | Packaging |
|------|----------|-----------|
| Cold spare NVMe 512GB | Center pocket | Anti-static bag |
| CR2032 batteries (×4) | Small pocket | Original packaging |
| ATO fuses (10A, 5A) | Small pocket | Loose or in case |
| Wago 221 assortment | Medium pocket | Original packaging |
| USB GPS dongle | Side pocket | Anti-static bag |
| USB LED gooseneck | Side pocket | Loose |
| Spare USB-C cable | Rolled | Cable tie |
| Alligator whip cable | Coiled | Cable tie |
| Spare silica gel | Sealed bag | Mylar pouch |
| Dielectric grease | Tube | Original |

---

## 8. Validation Tests

### 8.1 Power System Tests

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| Battery voltage | Measure at pack terminals | 24-32V (depending on SoC) |
| Kill switch isolation | Switch off, measure downstream | 0V |
| NTC function | Measure resistance cold vs after 10s run | Resistance drops significantly |
| Fuse protection | Apply 20A briefly to 15A circuit | Breaker trips |
| Load disconnect function | Bench PSU sweep 22V→16V (see §8.1.1) | Opens at 18V ± 0.5V, closes at 20V ± 0.5V |
| KSD9700 function | Heat with heat gun (low setting, keep moving) until 85°C | Circuit opens, resets ~65°C |
| DC-DC output | Measure under 2A load | 4.9-5.1V |
| Efficiency | Measure input/output power | >85% |
| Solar charging | Connect panels in sun | MPPT shows charge current |

#### 8.1.1 Load Disconnect Verification Procedure (Bench PSU Method)

**Purpose:** Verify load disconnect (TL431 + latching relay) open/close thresholds without stressing batteries.

**Equipment:**
- Adjustable bench power supply (0-30V, 5A minimum)
- Multimeter
- Test leads with alligator clips

**Setup:**
1. Disconnect Na-ion pack from system (remove XT60 from battery bus)
2. Connect bench PSU to battery bus terminals:
   - PSU (+) → Battery Bus (+) [node C]
   - PSU (−) → Negative Bus [node R]
3. Set PSU current limit to 3A (prevents damage if short)
4. Kill switch ON

**Test Sequence:**

| Step | PSU Voltage | Expected Result | Action if Fail |
|------|-------------|-----------------|----------------|
| 1 | 24.0V | System boots, DC-DC outputs 5V, relay CLOSED | Check wiring |
| 2 | 22.0V | System runs normally, relay CLOSED | — |
| 3 | 20.0V | Relay remains CLOSED (above threshold) | — |
| 4 | 19.0V | Relay remains CLOSED (hysteresis band) | — |
| 5 | 18.0V | Relay **must** OPEN (0V downstream) | Check TL431 resistor divider |
| 6 | 16.0V | Relay remains OPEN | — |
| 7 | 18.0V | Relay remains OPEN (below close threshold) | — |
| 8 | 19.5V | Relay remains OPEN (hysteresis band) | — |
| 9 | 20.0V | Relay **should** CLOSE | Check TL431 resistor divider |
| 10 | 24.0V | System boots normally | — |

**Pass Criteria:**
- Open (disconnect): 18V ± 0.5V
- Close (reconnect): 20V ± 0.5V (~2V hysteresis)
- No oscillation at threshold
- Latching behavior: relay maintains state during hysteresis band

**Notes:**
- TL431 threshold set by R1 (70kΩ) / R2 (10kΩ) resistor divider
- If thresholds incorrect, verify resistor values (tolerance drift)
- Panasonic ADW1124HLW latching relay requires SET/RESET pulses
- This test takes ~5 minutes vs. hours of battery discharge
- Bench PSU also useful for INA219 calibration (known voltage/current)

**Threshold Calculation Reference:**
```
TL431 reference voltage: 2.5V
Open threshold:  V_open  = 2.5V × (R1 + R2) / R2 = 2.5V × 80k / 10k = 20V
Actual with hysteresis circuit: ~18V open, ~20V close
```

### 8.2 Compute Tests

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| Boot from NVMe | Power on | Login prompt in <60s |
| Boot from eMMC | Remove NVMe, power on | Lifeboat OS boots |
| NVMe speed | `fio` sequential read | >500 MB/s |
| NVMe SMART | `smartctl -a /dev/nvme0n1` | No errors, healthy |
| RAM test | `memtester 16G 1` | 0 errors |
| NPU access | Run RKLLM test inference | Inference completes |
| Thermal (idle) | Monitor 30 min idle | <50°C |
| Thermal (load) | Monitor 30 min stress | <75°C |

### 8.3 Display Tests

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| IT8951 detection | `lsusb` | IT8951 device visible |
| Full refresh | Black → White → Black | All pixels respond |
| Partial update | Update text region | <500ms |
| Sunlight readability | View in direct sun | Readable |
| Viewing angle | View at 45° from perpendicular | Readable |
| OLED status | Boot system | Shows voltage + status |

### 8.4 Audio Tests

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| ReSpeaker detection | `arecord -l` | Device listed |
| Mic capture | Record 10s, play back | Clear audio, no noise |
| Speaker output | Play test tone | Clear, no distortion |
| Volume range | Sweep volume 0-100% | Full range, no clipping |
| Ground loop | Listen for 60Hz hum | No audible hum |

### 8.5 I/O Tests

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| D-pad up | Press, check GPIO | GPIO goes LOW |
| D-pad down | Press, check GPIO | GPIO goes LOW |
| D-pad left | Press, check GPIO | GPIO goes LOW |
| D-pad right | Press, check GPIO | GPIO goes LOW |
| D-pad center | Press, check GPIO | GPIO goes LOW |
| Power button | Press, check GPIO | GPIO goes LOW |
| ESC button | Press, check GPIO | GPIO goes LOW |
| Home button | Press, check GPIO | GPIO goes LOW |

| USB keyboard | Type characters | Input received |
| RTC time | Set time, power cycle, check | Time persists |
| INA219 voltage | Apply 24V from bench supply, read via I2C | Voltage ±0.1V of multimeter |
| INA219 current | Apply known load, read via I2C | Current ±50mA of reference |
| GPS lock | Connect dongle outside, clear sky view | Satellite lock in <60s (cold start may take 30-60s first time; subsequent hot starts <10s) |

### 8.6 Environmental Tests

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| Water ingress | 30 min @ 100mm depth | No moisture inside |
| Faraday | Phone inside, call it | Goes to voicemail |
| Drop (face down) | 1m onto concrete or hard surface, face down | Functional, latches secure, no visible damage |
| Drop (corner) | 1m onto concrete or hard surface, corner first | Functional, no internal component shift |
| Drop (edge) | 1m onto concrete or hard surface, long edge first | Functional, deck seated, no cable disconnection |

**Post-Drop Verification (all drop tests):**
1. Latches close and seal properly
2. Display shows no cracks or dead pixels
3. System boots normally
4. Open case: verify deck seated on posts, no loose components, no disconnected cables
5. Run I2C scan: all devices respond (0x3C, 0x40, 0x68)

| Vibration | 30 min car ride | All connections secure |
| Temperature (hot) | 4 hours @ 40°C ambient | Normal operation |
| Temperature (cold) | 4 hours @ 5°C ambient | Normal operation |

### 8.7 Full System Integration Tests

| Test | Procedure | Pass Criteria |
|------|-----------|---------------|
| Cold boot to ready | Power on from off | Ready in <90s |
| Voice query | Speak query via ReSpeaker | Response via TTS |
| Text query | Type query via keyboard | Response on E-ink |
| Power mode transition | Active → Idle → Sleep → Wake | All transitions work |
| Software LVD (ark-power) | INA219 reads <18V | Graceful shutdown via systemd |
| Hardware load disconnect | Continue discharge below 18V | Relay opens, 0V to loads |
| Solar recovery | Connect solar after LVD trip | System recovers |
| Battery runtime (active query) | 1 query every 10 min, display on, full LLM inference cycle | >12 hours (~10W average) |
| Battery runtime (browse) | Display on, occasional D-pad navigation, no LLM queries | >24 hours (~5.6W average) |
| Battery runtime (standby) | Display off, services stopped, CPU min freq | >11 days (~2.8W) |

**Runtime Calculation Basis (Na-ion 8S4P):**
- Battery capacity: 960Wh (40Ah @ 24V nominal)
- Usable capacity: ~768Wh (80% depth of discharge for Na-ion longevity)
- Active query: 768Wh ÷ 20W = 38h
- Browse: 768Wh ÷ 5.6W = ~137h (~6 days)
- Standby: 768Wh ÷ 2.8W = ~274h (~11 days)
- Trade-off: 25% less runtime vs LiFePO4 spec for decade dormancy capability

| Solar charge time (normal) | 20%→100% in full sun (150W array) | <6 hours |
| Solar charge time (Lazarus) | 0V→bootable via passive bootstrap + MPPT | 4-8 hours |

### 8.8 Pre-Deployment Checklist

Before sealing for storage/deployment:

- [ ] All tests in §8.1-8.7 pass
- [ ] Lazarus wake test passed (§8.9)
- [ ] Battery charged to 50-60% (optimal storage)
- [ ] Kill switch OFF
- [ ] Desiccant pouches installed (orange/dry)
- [ ] All spare parts in lid foam
- [ ] Cold spare NVMe sealed in anti-static bag
- [ ] Lid foam organized and secured
- [ ] All cables secured with no stress points
- [ ] Case latches close smoothly
- [ ] Pressure equalization valve clear
- [ ] Serial numbers recorded
- [ ] Assembly date recorded on label inside lid

---

### 8.9 Lazarus Wake Validation Test

This test validates the complete 0V recovery pathway—the signature capability of the Lazarus-Phoenix architecture. It confirms that after decade dormancy with a fully depleted battery, the system can autonomously recover and boot using only solar power. Cross-reference Hardware Specification §4.5 (Passive Bootstrap) and §4.3 (Genasun MPPT).

#### 8.9.1 Test Overview

**Purpose:** Validate end-to-end 0V → bootable recovery sequence.

**Prerequisites:**
- All §8.1-8.8 tests passed
- Bench power supply (0-30V, 5A minimum)
- Analog or digital multimeter
- Stopwatch or timer
- Temperature-controlled environment (15-25°C ideal)
- Safety: Fully charged fire extinguisher nearby

**Test Duration:** ~15-30 minutes (accelerated bench test) or 4-8 hours (real solar)

#### 8.9.2 Test Configuration Options

| Option | Method | Duration | Fidelity |
|--------|--------|----------|----------|
| A | Bench PSU simulating solar | 15-30 min | High (controllable) |
| B | Real solar array outdoors | 4-8 hours | Highest (real-world) |

**Recommended:** Use Option A for acceptance testing (faster, repeatable). Use Option B for final validation before deployment.

#### 8.9.3 Option A: Bench PSU Test Procedure

**Setup:**

| Step | Action |
|------|--------|
| 1 | Disconnect battery pack from system (XT60 at pack output) |
| 2 | Connect bench PSU to battery bus (simulate depleted pack at 0V) |
| 3 | Set bench PSU to 0V, current limit 5A |
| 4 | Connect second bench PSU to solar input (PV+ / PV-) |
| 5 | Set solar PSU to 0V initially |
| 6 | Attach multimeter probes to battery bus for monitoring |
| 7 | Verify kill switch is ON (allows load path) |

**Phase 1: Passive Bootstrap Verification (0V → 12V)**

| Step | Action | Expected | Pass Criteria |
|------|--------|----------|---------------|
| 1 | Set solar PSU to 18V (simulates panel Voc) | Bootstrap conducts | Current flows |
| 2 | Measure current through 47Ω resistor | ~0.3-0.5A | 0.25-0.55A |
| 3 | Measure voltage at battery bus | Slowly rising | >0V and increasing |
| 4 | Verify Genasun LED OFF | MPPT not yet active | No LED illumination |
| 5 | Slowly increase "battery" PSU voltage (simulating charge) | Voltage rises | Controllable ramp |
| 6 | At ~8-10V, observe Genasun | May begin to activate | LED may flicker |
| 7 | Continue to 12V | Genasun fully active | Solid LED, increased current |

**Phase 2: MPPT Takeover Verification (12V → 20V)**

| Step | Action | Expected | Pass Criteria |
|------|--------|----------|---------------|
| 8 | Observe charging current | Higher than bootstrap alone | >1A from MPPT |
| 9 | Measure bootstrap resistor | Lower voltage drop | <5V (MPPT handling load) |
| 10 | Continue ramp to 18V | Load disconnect still OPEN | No relay click |
| 11 | Ramp to 19V | Load disconnect preparing | Monitor closely |
| 12 | Cross 20V threshold | **RELAY CLICKS** → SET | Audible click |
| 13 | Verify relay state | COM-NO closed | Continuity confirmed |

**Phase 3: System Boot Verification (>20V)**

| Step | Action | Expected | Pass Criteria |
|------|--------|----------|---------------|
| 14 | Set battery PSU to 24V (normal operating voltage) | System has power | DC-DC active |
| 15 | Observe DC-DC output | 5V present | 4.9-5.1V |
| 16 | OPi 5+ boot sequence | LEDs active, boot messages | Normal boot |
| 17 | System reaches login prompt | Full boot complete | Login available |
| 18 | Run basic diagnostic | System functional | No errors |

**Phase 4: Low-Voltage Disconnect Verification**

| Step | Action | Expected | Pass Criteria |
|------|--------|----------|---------------|
| 19 | Reduce battery PSU to 18V | System still running | Relay stays CLOSED |
| 20 | Reduce to 17V | Approaching threshold | Monitor relay |
| 21 | Cross below 16.5V | **RELAY CLICKS** → RESET | Audible click |
| 22 | Verify system powers off | DC-DC loses input | OPi 5+ shuts down |
| 23 | Increase voltage to 20V | Relay re-engages | System can reboot |

#### 8.9.4 Option B: Real Solar Test Procedure

**Prerequisites:**
- Clear sunny day (>800 W/m² irradiance)
- 3×50W solar panels connected (150W array)
- Battery pack deeply discharged (<1V pack voltage) or 0V simulation
- Safe outdoor location for multi-hour test

**Procedure:**

| Step | Action | Expected | Pass Criteria |
|------|--------|----------|---------------|
| 1 | Start early morning (panels in shade) | 0V solar input | Baseline |
| 2 | Orient panels toward sun path | Illumination begins | Voltage rises |
| 3 | Record time when Voc > 15V | Solar active | Timestamp |
| 4 | Monitor battery voltage rise | Bootstrap charging | Slow increase |
| 5 | Record time when battery reaches 12V | Genasun activates | Timestamp |
| 6 | Record time when battery reaches 20V | Load disconnect closes | Timestamp + click |
| 7 | Verify system boots | Full recovery | Boot to login |
| 8 | Calculate total recovery time | 0V → boot | Should be 4-8 hours |

**Expected Timeline (150W array, full sun):**

| Phase | Duration | Cumulative |
|-------|----------|------------|
| 0V → 12V (bootstrap + early MPPT) | 2-4 hours | 2-4 hours |
| 12V → 20V (MPPT charging) | 2-4 hours | 4-8 hours |
| Boot sequence | ~2 minutes | 4-8 hours |

**Note:** Overcast conditions significantly extend recovery time (2-3× longer).

#### 8.9.5 Pass/Fail Criteria

| Criterion | Pass | Fail |
|-----------|------|------|
| Bootstrap current at 0V battery | 0.25-0.55A | <0.2A or >0.6A |
| Genasun activates by 12V | LED on, current increases | No activation |
| Load disconnect closes at 20V | Audible click, continuity | No click by 22V |
| System boots after relay closes | Login prompt | Boot failure |
| Load disconnect opens below 17V | Audible click, system off | Stays closed below 16V |
| Recovery time (bench accelerated) | <30 minutes | >45 minutes |
| Recovery time (real solar) | <8 hours | >12 hours |

#### 8.9.6 Troubleshooting

| Symptom | Probable Cause | Fix |
|---------|----------------|-----|
| No bootstrap current | Diode failed open, resistor open | Check 1N5408 and 47Ω continuity |
| Very low bootstrap current | High resistance in path | Check connections, wire gauge |
| Genasun never activates | MPPT fault, wrong programming | Verify Genasun LED, return for service |
| Relay never clicks | TL431 threshold wrong, transistor fault | Bench test LVD circuit per §5.8.5 |
| System won't boot | DC-DC fault, OPi 5+ issue | Isolate and test components |
| Relay clicks repeatedly | Threshold too narrow, voltage instability | Check capacitor values, add hysteresis |

#### 8.9.7 Documentation

Record results for traceability:

| Field | Value |
|-------|-------|
| Test Date | |
| Tester | |
| Test Type | [ ] Bench PSU [ ] Real Solar |
| Bootstrap Current @ 0V | A |
| Genasun Activation Voltage | V |
| Load Disconnect Close Voltage | V |
| Load Disconnect Open Voltage | V |
| Boot Success | [ ] Yes [ ] No |
| Total Recovery Time | |
| Notes | |

**✅ Signature Capability Verified:** A passing Lazarus Wake test confirms the system can recover from decade dormancy—the core design goal of the Lazarus-Phoenix architecture.

---

## 9. Maintenance Procedures

### 9.1 Quarterly Storage Scrub

If device in storage >3 months:

1. Open case, power on
2. Verify TRIM support: `lsblk -D` (DISC-MAX should show non-zero value for NVMe)
3. Run storage scrub: `sudo fstrim -av`
4. Check desiccant (replace if pink)
5. Check battery voltage (recharge if <18V pack voltage)
6. Verify RTC time correct
7. Power off, reseal

### 9.2 Desiccant Regeneration

When beads turn pink/green:

1. Remove pouches from case
2. Empty beads onto oven-safe tray (metal or glass, NOT plastic)
3. Bake at 120°C (250°F) for 2-3 hours (max safe temp: 150°C / 300°F — higher damages beads)
4. Beads return to orange when dry
5. Cool, refill pouches, reinstall

### 9.3 Connector Maintenance

Annually or after harsh conditions:

1. Power off, kill switch off
2. Disconnect each connector
3. Inspect for corrosion, debris
4. Apply thin layer of dielectric grease
5. Reconnect firmly

---

## 10. Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0.0 | Dec 2025 | Initial specification (RPi CM5 based) |
| 2.0.0 | Dec 2025 | Complete rewrite aligned with Hardware Spec v2.0.0: OPi 5+, 5-layer architecture, full protection chain, Faraday shielding, industrial controls |
| 2.1.0 | Dec 2025 | CRIT-003: Added INA219 power monitor to I2C bus and protection chain. Updated wiring diagrams, cable prep, component mounting, and validation tests. |
| 2.2.0 | Dec 2025 | CRIT-004: Fixed scavenger input wiring. Updated power distribution diagram to show binding posts connecting to battery bus via Schottky diode (not MPPT PV). Added diode mounting and cable prep. |
| 2.3.0 | Dec 2025 | CRIT-006: Added complete GPIO pin assignments for 7 input buttons. Includes physical pin numbers, GPIO names, gpiochip/line for gpiod, wiring diagram, and hold detection notes. Power button uses hardware PMIC. |
| 2.4.0 | Dec 2025 | CRIT-007: Added §6.8 Protection Chain Schematic & Wire Schedule. Complete circuit topology with labeled nodes (A-R), 30-wire schedule with gauges/lengths/terminations, star ground topology, 10 test points with expected values, fuse coordination table, Schottky mounting detail, and assembly checklist. |
| 2.5.0 | Dec 2025 | CRIT-013: Specified deck/faceplate material as 6061-T6 aluminum sheet, 3mm thick. Clarified deck mounting: PVC posts on foam pads, lateral foam constraint — NO fasteners into case (preserves Faraday shielding). Updated §4.2, §5.2, §5.5. Cross-refs 01-hardware-specification.md §8.5 BOM. |
| 2.6.0 | Dec 2025 | Case upgrade: Pelican 1500 → Pelican 1557 Air (Orange). Updated §4.1 with new dimensions (440×330×248mm interior, 196mm base depth). Deck dimensions updated to 425×315mm in §4.2 and §5.2. Aligns with Hardware Spec v2.5.0. |
| 2.7.0 | Dec 2025 | Dependency update: Hardware Spec v2.7.0→v2.8.0 alignment for HIGH-016/HIGH-017 (MPNs, mounting hardware). |
| 2.8.0 | Dec 2025 | HIGH-018: Complete rewrite of §3.2 Firmware & Configuration. Corrected boot priority (NVMe primary, eMMC fallback, not the reverse). Added 5-step U-Boot configuration procedure with SPI flash setup, boot order via fw_setenv, and fallback verification. Cross-referenced from Hardware Spec §3.1. |
| 2.9.0 | Dec 2025 | HIGH-019 + HIGH-020: Expanded §3.4 Cable Preparation with complete cable schedule (60 segments total). Added §3.4.1 Ferrule Size Reference table (DIN 46228). Added §3.4.3 I2C Bus Cables (W31-W42), §3.4.4 GPIO Button Cables (W43-W50), §3.4.5 Audio Cables (W51-W55), §3.4.6 USB Cables (W56-W60). Includes daisy-chain diagrams, termination types, and cable selection notes. |
| 2.10.0 | Dec 2025 | HIGH-021: Removed stale D-pad references. §1.2 Forstner bit now for OLED (not D-pad). §3.1 Pre-Assembly updated to "Nav buttons (×5)" matching discrete button design from CRIT-005. Hole schedule in §4.2 already correct (5× 12mm). |
| 2.11.0 | Dec 2025 | HIGH-022: §4.5 Display Window Installation rewritten for adhesive-only mounting. No fasteners penetrate case shell (Faraday constraint). Uses silicone sealant + VHB tape corners. Removed drilling instructions. |
| 2.12.0 | Dec 2025 | HIGH-023: §4.6 Faraday Shielding Installation expanded. Added §4.6.2 Deck-to-Case Grounding (tinned copper braid straps). Added §4.6.3 Panel-Mount Connector Shielding (XT60 wrap, mesh bonding). Added §4.6.4 Shielding Test procedure with AM radio leak detection. |
| 2.13.0 | Dec 2025 | HIGH-024: Resolved display USB routing. 3× deck USB-A panel mounts. Zero-hinge wiring via coiled cable through cavity to storm window access port. |
| 2.14.0 | Dec 2025 | HIGH-025: Fixed deck post height ~80mm → 147mm. Added vertical stack table to §5.2 matching Hardware Spec §5.5. Fixed deck plate 3mm → 2mm in §4.2 and §5.2. Batteries installed on side (107mm height). HIGH-026: Won't Fix — no threaded inserts needed (no case penetration design). |
| 2.15.0 | Dec 2025 | HIGH-027: Renamed §6.8.3 "Ground Bus"→"Negative Bus" (floating DC system has no earth ground). Specified 6-position 30A barrier terminal block as dedicated negative bus hardware. Added terminal assignment table, wiring procedure, mounting specs. Updated §5.4 component mounting table. Dependency updated to Hardware Spec v2.14.0. |
| 2.16.0 | Dec 2025 | HIGH-028: Removed Reset button (redundant with kill switch for hard reset, UI menu for soft reboot). Removed from hole schedule (§4.2), GPIO table (§6.6), layer diagram (§5.1), assembly sequence (§7.1), and I/O tests (§8.5). Function buttons reduced 4→3. Dependency updated to Hardware Spec v2.15.0. |
| 2.17.0 | Dec 2025 | HIGH-029: Added §5.6 MPPT Mounting. Victron SmartSolar 75/15 mounts in Kaizen foam pocket with airflow slots for convection cooling. Includes pocket dimensions (130×125×50mm), airflow slot design (4× 10×60mm vertical channels), Velcro retention strap, and installation procedure. Updated §7.1 Phase 2 integration sequence with cross-references to §5.3 and §5.6. |
| 2.18.0 | Dec 2025 | HIGH-030: Revised LVD test procedure. Added bench power supply to §1.1 Essential Tools. Replaced battery discharge test with bench PSU voltage sweep method (§8.1.1). Eliminates unnecessary battery stress, reduces test time from hours to ~5 minutes. |
| 2.26.0 | Dec 2025 | MED-022: Added §4.6.4 Pressure Equalization Valve documentation. Clarified valve is external feature (case body front face, Gore-Tex membrane) not affected by interior Faraday lining. Renumbered §4.6.4→§4.6.5 Shielding Test. |
| 2.27.0 | Dec 2025 | MED-023: KSD9700 test clarified (low setting, keep moving). Added reset temp to pass criteria. |
| 2.28.0 | Dec 2025 | MED-024 + MED-025: §8.6 Drop test updated from plywood to concrete with 3 orientations (face/corner/edge) + post-drop verification checklist. §8.7 Battery runtime test now defines 3 load profiles (active query 10W, browse 5W, standby 2.5W) with runtime calculation basis. |
| 2.29.0 | Dec 2025 | LOW issues batch: §8.5 GPS test now specifies clear sky view + cold/hot start times. §9.1 Added TRIM verification step. §9.2 Added desiccant max temp (150°C). |
| 2.30.0 | Dec 2025 | LOW-007: Added wire gauge rationale to §3.4.3 (22AWG for signal integrity, durability, standardization). LOW-009: Added lid foam layout diagram to §7.4 showing pocket locations for all 10 spare items. |
| **3.0.0** | **Dec 2025** | **Lazarus-Phoenix alignment:** Updated for Na-ion 8S4P (40Ah/960Wh) battery system. Added spot welder to required tools. Updated pre-assembly testing for Genasun GVB-8, Na-ion cells, JK BMS, load disconnect, wide-input DC-DC, analog voltmeter. Runtime calculations updated (25% reduction for decade dormancy capability). Protection components updated for TL431 + latching relay load disconnect. |
| **3.1.0** | **Dec 2025** | **CRIT-ASM-003:** Complete rewrite of §5.3 Battery Mounting. Replaced LiFePO4 dual-battery procedure with Na-ion 8S4P hybrid approach: installation-focused main section with pre-assembled or DIY options; full cell pack fabrication procedure moved to new appendix-e-naion-pack-assembly.md. Includes pack specs, foam pocket construction, verification procedures, and troubleshooting. |
| **3.2.0** | **Dec 2025** | **CRIT-ASM-004/005/006 + HIGH-ASM-001/002:** Complete Lazarus-Phoenix power system documentation. §5.6 MPPT Mounting rewritten for Genasun GVB-8-WP (boost, IP68 potted). §6.1 Power Distribution Diagram complete redraw with Na-ion 8S4P, passive bootstrap, load disconnect, analog voltmeter. §6.8 Protection Chain Schematic expanded (nodes A-T, 38-wire schedule). §6.2 Wire color 12V→24V. §6.3 Protection chain sequence updated (load disconnect first). |
| **3.3.0** | **Dec 2025** | **HIGH-ASM-003:** Fixed §8.1 Power System Tests voltage thresholds for Lazarus-Phoenix. Battery voltage: 12.8-13.6V → 24-32V. Renamed LVD function → Load disconnect function with correct thresholds (18V open, 20V close). Rewrote §8.1.1 Load Disconnect Verification Procedure with 10-step bench PSU sweep (22V→16V), TL431 resistor divider notes, and threshold calculation reference. Updated §8.5 INA219 test voltage (12V → 24V). Updated §8.7 Software/Hardware LVD tests for ark-power + load disconnect relay. |
| **3.4.0** | **Dec 2025** | **HIGH-ASM-005:** Complete rewrite of §3.4.2 Power Cables. Replaced obsolete 12V/LiFePO4 cable table (Anderson Y-Harness, Victron references) with Lazarus-Phoenix wire schedule (W01-W34). Now includes: Main Protection Chain (W01-W10), Load Disconnect Circuit (W11-W17), Analog Voltmeter (W18-W19), 5V Load Distribution (W20-W23), Solar Input Path (W24-W27), Passive Bootstrap (W28-W30), Scavenger Input (W31-W34). Added cross-reference to §6.8.2 Wire Schedule as authoritative source. Added §3.4.7 External Cables & BMS Leads covering solar Y-harness prep (MC4 → XT60) and BMS balance lead verification (9-wire JST-XH). |
| **3.5.0** | **Dec 2025** | **Batch A cleanup:** LOW-ASM-001 (§2.3 protection components list), MED-ASM-001 (§4.2 voltmeter cutout 45×35mm), MED-ASM-002 (§8.7 solar charge times split: normal 6hr, Lazarus wake 4-8hr). |
| **3.6.0** | **Dec 2025** | **HIGH-ASM-007:** Added §5.7 Passive Bootstrap Assembly procedure. New section documents: §5.7.1 Purpose & Components (1N5408 diode, 47Ω 25W resistor, 2A fuse per Hardware Spec §4.5); §5.7.2 Thermal Mounting with power dissipation calculations (10.3W at Voc) and bolt-to-deck procedure for resistor heat sinking; §5.7.3 Assembly & Wiring cross-referencing §6.8.2 wire schedule W28-W30; §5.7.4 Verification with pre-power checks and 0V recovery functional test. Updated §7.1 Phase 2 integration sequence (steps 8-10). |
| **3.7.0** | **Dec 2025** | **HIGH-ASM-008:** Added §5.8 Load Disconnect Assembly. Complete TL431 dual-threshold pulse-latch circuit: §5.8.1 Components (TL431A ×2, 2N2222A drivers, ADW1124HLW relay, resistor dividers); §5.8.2 Circuit topology with threshold calculations (~19.5V SET, ~16.5V RESET); §5.8.3 Perfboard assembly (50×70mm layout); §5.8.4 Relay mounting & wiring (W31-W37); §5.8.5 Verification with bench PSU sweep procedure. |
| **3.8.0** | **Dec 2025** | **HIGH-ASM-009:** Added §5.9 Analog Voltmeter Installation. 0-30V DC moving-coil panel meter: §5.9.1 Purpose & specs (d'Arsonval, ±2%); §5.9.2 Panel cutout procedure (45×35mm rectangular); §5.9.3 Mounting methods (bezel clips/screws/ring); §5.9.4 Wiring (W18/W19 per §6.8.2); §5.9.5 Verification with 6-point calibration test. |
| **3.9.0** | **Dec 2025** | **HIGH-ASM-010:** Added §8.9 Lazarus Wake Validation Test. Complete 0V→operational recovery test: §8.9.1 Overview & prerequisites; §8.9.2 Test options (bench PSU vs real solar); §8.9.3 Option A bench procedure (4 phases: bootstrap verify, MPPT takeover, system boot, LVD test); §8.9.4 Option B outdoor procedure; §8.9.5 Pass/fail criteria (7 quantitative); §8.9.6 Troubleshooting; §8.9.7 Documentation template. Updated §8.8 Pre-Deployment Checklist. |
| **4.0.0** | **Dec 2025** | **VAULT EDITION:** Storage architecture aligned with Hardware Spec v4.0.0. §1.3 Consumables: Added conformal coating, UV light, Faraday bags. §3.1 Pre-Assembly: Updated for Swissbit X-76m SLC NVMe and S-56 SLC SD. §3.2: DS3231 now uses Li-SOCl2 primary battery. §3.3 NVMe Preparation: Rewritten for Swissbit SLC with identity verification. §3.3.1 Cold Spare: Faraday bag storage with SHA-256 manifests. NEW §3.3.2 Archival SD Preparation: Swissbit S-56 SLC with critical corpus subset. NEW §3.3.3 Conformal Coating Procedure: Complete MG Chemicals 422B application guide with masking matrix, UV inspection. MED-STG-005 resolved. |

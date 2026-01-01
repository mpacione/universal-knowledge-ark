---
version: "5.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
tags:
  - spec
  - universal-knowledge-ark
  - operations
---

# Appendix C: Operations Manual

**Version:** 5.0 (Vault Edition)

## 1. Quick Start

### 1.1 First Power-On

1. Open case latches
2. Remove items from cavity (keyboard sleeve, panels)
3. Retrieve coiled USB cable from sleeve
4. Connect cable between deck port and lid port
5. Flip kill switch to ON
6. Wait for boot (~45 seconds)
7. If "TIME LOST" appears, see [[#4. Time Management|Section 4]]

### 1.2 Normal Operation

1. Open case, connect display cable
2. Kill switch ON
3. Home Screen appears
4. Press `1` for Survival Menu (no typing)
5. Press `2` to type a question
6. Press `3` for System diagnostics
7. When done: Kill switch OFF, disconnect cable, close case

### 1.3 Charging

1. Open case for ventilation (or use extension cable)
2. Unfold solar panel(s)
3. Connect panel(s) to XT60 port (use Y-splitter for both)
4. Position panels in direct sun, case in shade
5. MPPT charges automatically
6. Kill switch can be ON (use while charging) or OFF (storage charge)

> ⚠️ Never charge with case sealed in direct sun. Internal temps will exceed safe limits.

## 2. Daily Use

### 2.1 Survival Menu Navigation

The Survival Menu requires only single-key presses. No typing.

| Level | Action | Key |
|-------|--------|-----|
| Home | Open Survival Menu | `1` |
| Category | Select topic | `1`-`9` |
| Subcategory | Select topic | `1`-`9` |
| Content | Read, scroll | `↑`/`↓` |
| Any level | Go back one level | `ESC` |
| Any level | Return to Home | `H` |

### 2.2 Asking Questions

1. From Home, press `2`
2. Type your question using USB keyboard
3. Press `Enter`
4. Footer shows "Processing..." (OLED shows spinner animation)
5. Results display when ready
6. Scroll with `↑`/`↓` or `PgUp`/`PgDn`

**Tips:**
- Use specific terms: "splint broken arm" not "help injury"
- If results are poor, try Survival Menu instead
- Press `↑` for previous searches

### 2.3 Night Operations

E-ink displays have no backlight. For night use:

1. Locate aux USB port by feel (tactile ridge around port)
2. Retrieve USB LED gooseneck light from lid foam
3. Plug into aux USB port (deck faceplate)
4. Position light to illuminate screen and keyboard
5. Cool white (5000-6500K) provides accurate color for medical assessment

## 3. Power Management

### 3.1 Reading Battery Status

**OLED Display (always visible):**
```
BAT: 24.0V (OK)
```

**Voltage to Charge Approximation (Na-ion 8S):**

| Voltage | Approx. SOC | System State |
|---------|-------------|--------------|
| 32.0V | 100% | Full charge |
| 28.0V | 75% | Nominal |
| 24.0V | 50% | Nominal (name voltage) |
| 20.0V | 25% | Load disconnect closes |
| 18.0V | 10% | Software LVD / load disconnect opens |
| 16.0V | 5% | Emergency mode only |
| 12.0V | 0% | Deep discharge floor |
| 0.0V | — | Decade storage (safe for Na-ion) |

> **Note:** Unlike LiFePO4, Na-ion cells with aluminum current collectors tolerate 0V storage indefinitely. See [[#9. Lazarus Wake Procedure|§9 Lazarus Wake Procedure]] for recovery from decade dormancy.

### 3.1.1 Reading the Analog Voltmeter

The deck faceplate includes a 0-30V DC moving-coil analog voltmeter. This provides battery state feedback with zero silicon dependency—critical when waking from decade dormancy before digital displays are powered.

**Meter Location:** Deck faceplate, visible when lid is open.

**Scale Interpretation:**

| Needle Position | Meaning | Action |
|-----------------|---------|--------|
| 0V | Decade dormancy | Connect solar, begin Lazarus wake ([[#9. Lazarus Wake Procedure|§9]]) |
| 12V | Deep discharge | Charge immediately; do not attempt boot |
| 16-18V | Low | Charge before use; system may not boot |
| 18-20V | Marginal | System will boot; charge soon |
| 20-28V | Nominal | Normal operation |
| 28-32V | Full | Fully charged |
| >30V (off scale) | Overvoltage | Disconnect charging source immediately |

**When to Use:**
- After extended storage (months/years) before attempting boot
- When OLED display is not responding
- During Lazarus wake to monitor passive bootstrap progress
- Field verification when digital readings seem incorrect

### 3.2 Power Modes

| Mode | OLED | Power | Description |
|------|------|-------|-------------|
| READY | READY | 5W | Browsing content, awaiting input |
| BUSY | BUSY | 10W | Processing query (NPU active) |
| SLEEP | SLEEP | 2.5W | Standby after idle timeout |
| CRIT | Flashing | — | Battery critical (<10%), shutdown imminent |

**Standby behavior:** After 5 minutes of inactivity (configurable), the device enters SLEEP mode. Display retains last content (E-ink persistence). Press any button to wake (~2-3 seconds).

### 3.3 Charging Times

| Source | Power | Scenario | Time |
|--------|-------|----------|------|
| 3× 50W panels (full sun) | ~120W actual | 20%→100% (normal) | ~5-6 hours |
| 3× 50W panels (full sun) | ~120W actual | 0V→bootable (Lazarus) | 4-8 hours |
| Single 50W panel | ~40W actual | 20%→100% (normal) | ~15 hours |
| Car/marine battery (12V via binding posts) | N/A | Jump-start only | See note |
| External 18-30V DC (via binding posts) | Varies | Depends on source | Varies |

**Notes:**
- **Lazarus wake (0V start):** First 2-4 hours use passive bootstrap (~10W trickle). Once battery reaches ~12V, Genasun MPPT takes over at full power.
- **12V source limitation:** Car batteries (12V) are below optimal charging range (18-32V). Use only for emergency jump-start of a deeply discharged pack. The Genasun MPPT requires 18V+ input for boost operation.
- **Scavenger input:** Binding posts accept 18-32V DC. Voltages below 18V will not charge effectively.

### 3.4 Emergency Power Sources

Use scavenger cables (in lid foam) to connect alternative power:

**Alligator Whip → Binding Posts:**
- Two car/truck batteries in series (24V)
- Marine battery bank (24V)
- Solar array (18-32V)
- UPS battery bank

**Bare Wire Whip → Binding Posts:**
- Stripped power cables
- Improvised sources
- Unknown connectors

> ⚠️ Observe polarity. Red = positive, Black = negative. Reverse polarity will blow fuse (replaceable) but may damage electronics.

## 4. Time Management

### 4.1 Time Lost Warning

If you see "TIME LOST" on boot, the RTC battery died or this is first boot.

**Option A: GPS Sync (Recommended)**
1. Retrieve GPS dongle from lid foam
2. Plug into aux USB port
3. Step outside with clear sky view
4. Wait 30-60 seconds for "GPS TIME ACQUIRED"
5. Return dongle to storage

**Option B: Manual Set**
1. System Menu → Set System Time
2. Enter: `YYYY-MM-DD HH:MM:SS`
3. Press Enter

**Option C: Continue (Not Recommended)**
- Logs will be incorrect
- Navigation calculations will fail
- Some features may malfunction

### 4.2 Determining Time Without Clock

If GPS unavailable and no other clocks:

- **Sun position:** Local solar noon when sun is highest
- **Stars:** Polaris indicates north; specific constellations indicate season/time
- **Shortwave radio:** WWV (2.5/5/10/15/20 MHz) broadcasts UTC continuously
- **Other survivors:** Coordinate with anyone who has working time reference

## 5. Troubleshooting

### 5.1 Device Won't Boot

| Symptom | Likely Cause | Action |
|---------|--------------|--------|
| No OLED, no response | Kill switch OFF | Flip switch ON |
| No OLED, no response | Dead battery | Charge for 30+ minutes |
| OLED shows voltage, no display | Display cable disconnected | Connect cable |
| OLED shows "BOOT...", hangs | Software issue | Wait 2 min, then cycle power |
| Boots to "LIFEBOAT" | NVMe failed | Follow recovery menu |

### 5.2 Display Issues

| Symptom | Cause | Action |
|---------|-------|--------|
| Blank screen | Cable disconnected | Check cable both ends |
| Frozen image | Normal (e-ink retains) | System may be processing; check OLED |
| Garbled display | Partial refresh artifacts | Wait for full refresh |
| Cracked display | Impact damage | Replace display (spare in basement) |

### 5.3 Audio Issues

| Symptom | Cause | Action |
|---------|-------|--------|
| No sound | Low battery | Battery too low; charge |
| Buzzing/whine | Ground loop | Check isolator connections |
| Distorted | Volume too high | Software issue; restart |

### 5.4 Power Issues

| Symptom | Cause | Action |
|---------|-------|--------|
| Won't charge | Solar disconnected | Check XT60 connection |
| Won't charge | Wrong polarity | Use correct polarity |
| Shuts down unexpectedly | Low battery | Charge device |
| Shuts down unexpectedly | Overheating | Open case, move to shade |
| Fuse blown | Overcurrent | Replace fuse (spares in lid) |

### 5.5 Emergency Recovery

If device becomes unresponsive:

1. **Flip kill switch OFF, wait 10 seconds, flip ON**

2. **If still unresponsive:** Connect laptop via Ethernet
   - Laptop IP: 10.55.0.2/24
   - SSH to: root@10.55.0.1

3. **If NVMe failed:** Lifeboat OS boots automatically
   - Press `R` for Factory Reset (preserves library, erases settings)
   - ⚠️ This erases all user data

## 6. Maintenance

### 6.1 Regular Checks

| Interval | Task |
|----------|------|
| Weekly (if in use) | Check battery voltage |
| Monthly | Check desiccant indicator |
| Quarterly | Run storage scrub (see [[#6.4 Storage Scrub|§6.4]]) |
| Yearly (in storage) | Voltage check, recharge if <30% |
| Every 2-3 years | Replace RTC backup battery (CR2032) |

### 6.2 Desiccant Regeneration

If indicator beads turn pink/green (saturated):

1. Remove desiccant pouches from case
2. Spread beads on baking sheet
3. Heat at 120°C (250°F) for 2-3 hours
4. Alternative: hot car dashboard, black container in sun
5. Beads return to orange/blue when dry
6. Refill pouches, return to case

### 6.3 Connector Maintenance

For long-term storage or after exposure to moisture:

1. Apply thin film of dielectric grease to:
   - Binding post threads
   - XT60 connector pins
   - Battery terminals
2. Work connector a few times to distribute grease
3. Wipe excess

### 6.4 Storage Scrub (Quarterly)

NAND flash memory cells lose charge over time ("bit rot"). Reading data refreshes the cells. Run this procedure quarterly during active use, or before sealing for long-term storage.

**Automatic Prompt:** If `last_scrub > 90 days` and `battery > 50%`, the system displays a reminder at boot. This is non-blocking—you can dismiss and continue.

**Manual Procedure:**

1. From Home Screen, press `3` (System)
2. Select `Storage Maintenance`
3. Select `Run Scrub`
4. Wait for completion (15-30 minutes depending on data volume)
5. Review results:
   - **All OK:** No action needed
   - **Errors corrected:** PAR2 repaired corrupted blocks
   - **Uncorrectable errors:** Restore from Factory Reset Image

**What It Does:**
- Reads all blocks on the `/data` partition
- Verifies PAR2 checksums
- Automatically repairs minor corruption
- Refreshes NAND cell charge levels
- Logs results to `/data/logs/scrub.log`

**When to Skip:** If battery is below 50% or you need the device urgently, dismiss the reminder. Run the scrub when convenient—quarterly is a guideline, not a hard requirement.

### 6.5 Cold Spare Update (Annual)

The cold spare NVMe (stored in lid foam) should be updated annually to ensure it contains current content. Update more frequently after major library additions or system updates.

**When to Update:**
- Annual maintenance cycle
- After major library additions (>10GB new content)
- After OS or application updates
- Before extended storage (>2 years)

**Prerequisites:**
- Battery >50% (or connected to external power)
- USB 3.0 NVMe adapter (recommended) or willingness to swap drives
- ~60 minutes uninterrupted time

**Method A: USB Adapter (No Boot Interruption)**

1. **Verify primary system health:**
   - Run Storage Scrub ([[#6.4 Storage Scrub|§6.4]])
   - Run Diagnostics (System → Diagnostics)
   - Ensure no errors

2. **Connect cold spare:**
   - Retrieve from lid foam, remove from anti-static bag
   - Connect via USB 3.0 NVMe adapter to aux USB port
   - Wait for device recognition: `lsblk` shows new device (typically `/dev/sda`)

3. **Clone primary to spare:**
   ```bash
   # Verify device names first!
   lsblk
   # Primary is /dev/nvme0n1, spare is /dev/sdX
   sudo dd if=/dev/nvme0n1 of=/dev/sdX bs=4M status=progress
   sync
   ```
   - ⚠️ Triple-check device names before running dd
   - Time: ~30-45 minutes for 512GB via USB 3.0

4. **Verify (optional but recommended):**
   ```bash
   sudo cmp /dev/nvme0n1 /dev/sdX
   ```
   - If no output, drives are identical
   - Time: ~20 minutes additional

5. **Reseal and store:**
   - Update bag label: `IMAGED: YYYY-MM-DD`
   - Seal in anti-static bag
   - Return to lid foam pocket

**Method B: Drive Swap (Faster Clone, Requires Reboot)**

1. Complete step 1 from Method A

2. **Swap drives:**
   - Power off (kill switch OFF)
   - Remove primary NVMe from OPi 5+ M.2 slot
   - Install cold spare in M.2 slot
   - Connect primary to USB adapter

3. **Boot from spare, clone from primary:**
   - Kill switch ON
   - Boot (will boot from old spare image)
   - Clone: `sudo dd if=/dev/sda of=/dev/nvme0n1 bs=4M status=progress`
   - Sync: `sync`

4. **Swap drives back:**
   - Power off
   - Return primary to M.2 slot
   - Verify primary boots normally

5. **Verify spare and reseal:**
   - Optionally boot from spare to verify
   - Swap primary back
   - Update bag label, reseal, store

**Time Estimate:** 45-60 minutes (Method A) or 60-75 minutes (Method B)

**Skipping Updates:** If >3 years since last update, the cold spare may have outdated content or incompatible software. Consider a fresh clone rather than incremental updates.

## 7. Long-Term Storage

### 7.1 Preparing for Storage

1. Charge battery to 50-60% (optional—Na-ion tolerates 0V; higher charge = faster reactivation)
2. Run storage scrub (System → Storage Maintenance → Run Scrub)
3. Run diagnostic (System → Diagnostics), verify all pass
4. Disconnect display cable, store in sleeve
5. Pack cavity (keyboard, panels, cables in sleeve)
6. Check desiccant indicator (regenerate if needed)
7. Apply dielectric grease to exposed contacts
8. Kill switch OFF
9. Seal case (all latches engaged)

**For storage exceeding 12 months:**

10. Place sealed Pelican case inside **gusseted Mylar ziplock bag** (included in BOM)
11. Squeeze out excess air before sealing ziplock
12. For 5+ year storage: add oxygen absorber packet inside Mylar bag
13. Store in cool (15-25°C), dark location

**Rationale:** Pelican cases are water-tight (liquid) but not vapor-tight. Over years, humidity permeates the polypropylene shell and saturates desiccant. The Mylar bag provides a true vapor barrier for extended dormancy. The case interior already has Faraday shielding, so EMP protection is maintained.

### 7.2 Storage Duration Guidelines

| Duration | Maintenance |
|----------|-------------|
| 0-6 months | No action needed |
| 6-12 months | Optional voltage check |
| 12-24 months | Voltage check, recharge if <30%, verify Mylar seal |
| 24-36 months | Mandatory recharge, desiccant check, reseal Mylar |
| 36+ months | No recharge required—use Lazarus wake ([[#9. Lazarus Wake Procedure|§9]]) if 0V. Full test recommended. |
| 5-10+ years | Expect battery may need jump-start; Factory Reset Image has PAR2 protection |

### 7.3 Reactivation Procedure

1. Open case in ventilated area
2. Allow 30 minutes for pressure equalization
3. Check battery voltage (use analog voltmeter if OLED doesn't light)
4. If voltage <12V: use Lazarus wake procedure ([[#9. Lazarus Wake Procedure|§9]])
5. Connect display cable
6. Kill switch ON
7. Complete boot
8. Sync time if "TIME LOST" appears
9. Run full diagnostic
10. Verify library accessible (Survival Menu → any topic)

## 8. Field Improvisation

### 8.1 Power Scavenging

**Automotive Battery (12V — Limited Use):**
1. Alligator Whip → Binding Posts
2. Red clip to battery (+)
3. Black clip to battery (-) or chassis ground
4. **Note:** 12V is below optimal charging range (18-32V). Use only for emergency jump-start of a deeply discharged pack. For sustained charging, use 24V sources (two 12V batteries in series) or solar.

**24V Source (Preferred):**
1. Two 12V batteries in series, or 24V marine/solar system
2. Connect to binding posts (observe polarity)
3. Device will charge and operate simultaneously

**Solar Array (External/Replacement):**
1. Must be 18-32V output (Vmp ~18V optimal for Genasun boost MPPT)
2. Connect to XT60 or binding posts
3. Observe polarity

**Generator:**
1. Use 12V DC output if available
2. Connect to binding posts
3. Verify voltage before connecting

### 8.2 Wire Splicing

Use Wago 221 lever nuts (in lid foam) for field repairs:

1. Strip wire 11mm
2. Lift lever
3. Insert wire fully
4. Press lever down
5. Repeat for other wire(s)

No tools, no solder, reusable.

**Emergency (no Wagos available):**
- Twist stripped wires tightly together
- Wrap with tape or heat shrink
- For screw terminals: tin wire with solder, or fold twisted strands over before clamping

### 8.3 Fuse Replacement

Spare fuses in lid foam:
- 15A (Blue) — Battery circuit
- 10A (Red) — Solar/aux
- 5A (Tan) — System logic

To replace:
1. Kill switch OFF
2. Pull blown fuse from block
3. Insert replacement of same rating
4. Kill switch ON

### 8.4 Screen Clip Failure

If metal screen clips break or are lost:

1. Remove damaged clips
2. Apply 3M VHB tape (or duct tape) around entire display perimeter
3. Press firmly to lid frame
4. Allow 10 minutes before closing lid

### 8.5 Deck Support Failure

If PVC posts crack or foam pads disintegrate:

**Replacement posts (any of these work):**
- PVC pipe (any diameter that fits)
- Wooden dowels or chair legs
- Stacked washers or coins
- Cut sections of metal conduit

**Replacement foam:**
- Folded cardboard
- Rolled clothing
- Any compressible material

**Reinstallation:**
1. Remove deck (4 thumbscrews)
2. Replace corner posts with scavenged material, matching original height
3. Wedge padding material at deck edges for lateral stability
4. Replace deck, secure with thumbscrews

### 8.6 Thumbscrew Loss

If M4 thumbscrews are lost:

1. Check lid foam for spares (4 included)
2. If no spares: pass zip-ties or wire through 5mm backup holes adjacent to mount points
3. Secure deck with zip-ties — functional, if ugly

### 8.7 USB-C Cable Failure

If the OPi power cable fails:

1. Open deck (4 thumbscrews)
2. Locate USB-C breakout board on deck underside
3. Unplug failed cable
4. Replace with any standard USB-C cable (spare in lid foam)
5. Reassemble

## 9. Lazarus Wake Procedure (Decade Dormancy Recovery)

### 9.1 When to Use

Use this procedure when:
- Device has been in storage for months or years
- Analog voltmeter reads 0V or near-zero
- OLED display does not respond to kill switch
- You are recovering from intentional decade dormancy storage

**Do NOT use** for normal low-battery situations where voltage is above 16V—simply connect solar and charge normally.

### 9.2 Prerequisites

| Item | Requirement |
|------|-------------|
| Solar array | 3× 50W panels (150W total), or equivalent 18V+ source |
| Weather | Clear sky, direct sun (partial cloud extends timeline) |
| Time | 4-8 hours unattended (can monitor periodically) |
| Tools | None required (analog voltmeter is built-in) |

### 9.3 Procedure

**Phase 1: Passive Bootstrap (0V → ~12V)**

1. Open case, unfold solar panels
2. Connect panels to XT60 port
3. Position panels in direct sun, case in shade
4. Read analog voltmeter — confirm 0V or near-zero
5. **Wait.** The passive bootstrap circuit (diode + 47Ω resistor) trickle-charges the battery at ~250-500mA
6. Check voltmeter every 30-60 minutes
7. Phase 1 complete when voltmeter reads **~12V** (2-4 hours typical)

**Phase 2: MPPT Takeover (12V → 20V)**

8. At ~12V, the Genasun GVB-8-WP MPPT activates automatically
9. Charging rate increases significantly (bootstrap hands off)
10. Continue monitoring — voltage rises faster now
11. Phase 2 complete when voltmeter reads **~20V** (1-2 hours additional)

**Phase 3: System Boot (>20V)**

12. At 20V, the load disconnect relay closes automatically
13. Flip kill switch ON
14. OLED should illuminate within 5 seconds
15. Wait for full boot (~45 seconds)
16. If "TIME LOST" appears, sync time ([[#4. Time Management|§4]])
17. Run diagnostics: System → Diagnostics

**Phase 4: Full Charge (Optional)**

18. Continue charging to 28-32V for full capacity
19. Time: 2-4 additional hours depending on starting point

### 9.4 Expected Timeline

| Phase | Voltage Range | Duration | Charging Mode |
|-------|---------------|----------|---------------|
| 1 — Bootstrap | 0V → 12V | 2-4 hours | Passive (diode + resistor) |
| 2 — MPPT ramp | 12V → 20V | 1-2 hours | Genasun boost MPPT |
| 3 — Boot | 20V+ | Minutes | System operational |
| 4 — Full charge | 20V → 32V | 2-4 hours | Genasun boost MPPT |
| **Total** | 0V → 32V | **4-8 hours** | — |

### 9.5 Troubleshooting

| Symptom | Likely Cause | Action |
|---------|--------------|--------|
| Voltmeter stays at 0V after 1 hour | No sun / panels disconnected | Verify XT60 connection, reposition panels |
| Voltmeter stays at 0V after 1 hour | Bootstrap circuit fault | Check 1N5408 diode and 47Ω resistor (see [[02-Assembly-Specification#5.7 Passive Bootstrap Assembly|Assembly Spec §5.7]]) |
| Voltage rises then drops | Intermittent sun (clouds) | Normal; wait for consistent sun |
| Stuck at ~12V, won't rise further | MPPT not activating | Verify Genasun connections; may need bench test |
| 20V+ but system won't boot | Load disconnect fault | Check TL431 circuit (see [[02-Assembly-Specification#5.8 Load Disconnect Assembly|Assembly Spec §5.8]]) |
| Boots but shuts down immediately | Software LVD too aggressive | Charge to 22V+ before retry |

### 9.6 Post-Wake Checklist

After successful Lazarus wake:

- [ ] Time synced (GPS or manual)
- [ ] Diagnostics pass
- [ ] Survival Menu accessible
- [ ] Storage scrub scheduled (if >90 days since last)
- [ ] Cold spare update considered (if >1 year since last)

## 10. Archival Seal Procedure (Vault Edition)

### 10.1 When to Seal

Use the archival seal when:
- Corpus loading is complete and verified
- Preparing device for long-term (1+ year) storage
- Converting device to "seed vault" mode for passive survivability
- Before transferring device to a cache location

**Do NOT seal** if you plan to add content, update software, or use the device regularly. Sealing is for archival, not daily operation.

### 10.2 Prerequisites

| Item | Requirement |
|------|-------------|
| Battery | >50% charge (or connected to external power) |
| Storage | All intended corpus content loaded |
| Time | ~30 minutes for seal process |
| Conformal coating | Applied and cured (see [[02-Assembly-Specification#3.3.3 Conformal Coating|Assembly Spec §3.3.3]]) |

### 10.3 Seal Procedure

1. **Verify corpus integrity:**
   - System → Storage Maintenance → Verify All
   - Wait for completion (15-30 minutes)
   - All files must pass; resolve any errors before proceeding

2. **Run archival seal command:**
   ```bash
   ark-seal --mode=archival
   ```
   Or via menu: System → Archival → Seal Device

3. **Confirm seal:**
   - Prompt: "This will make /data and /archive READ-ONLY. Continue? [y/N]"
   - Type `y` and press Enter

4. **Wait for manifest generation:**
   - System generates SHA-256 checksums of all corpus files
   - Manifests stored in three locations (NVMe, SD, eMMC)
   - Progress displayed on E-ink; OLED shows "SEALING..."
   - Time: 5-15 minutes depending on corpus size

5. **Verify seal confirmation:**
   - E-ink displays: "DEVICE SEALED" with timestamp
   - OLED shows: "SEALED" status permanently
   - Seal flag written to /recovery/.sealed

6. **Prepare cold spares:**
   - Clone current NVMe to spare (see [[#6.5 Cold Spare Update|§6.5]])
   - Clone current SD to spare SD
   - Seal spares in Faraday bags with dated labels
   - Store in lid foam pockets

7. **Complete storage preparation:**
   - Follow [[#7. Long-Term Storage|§7 Long-Term Storage]] procedures
   - Seal case in Mylar bag for 5+ year storage

### 10.4 What Sealing Does

| Action | Purpose |
|--------|----------|
| Remounts /data read-only | Prevents any writes, maximizes retention |
| Remounts /archive read-only | Protects SD card corpus |
| Generates SHA-256 manifests | Enables bitrot detection on future boot |
| Stores manifests ×3 | Triple redundancy (NVMe, SD, eMMC) |
| Sets seal flag | Triggers Discovery Boot on future wake |
| Displays SEALED status | Visual confirmation on OLED |

### 10.5 Unsealing

> ⚠️ **WARNING:** Unsealing breaks archival warranty. Only unseal if device must be updated.

1. **Run unseal command:**
   ```bash
   ark-unseal --confirm
   ```
   Or via menu: System → Archival → Unseal Device

2. **Confirm unseal:**
   - Prompt: "This will allow writes and break archival seal. Continue? [y/N]"
   - Type `y` and press Enter

3. **Partitions remounted read-write**

4. **After updates, re-seal:**
   - Run `ark-seal --mode=archival` again
   - New manifests generated
   - Update cold spares

### 10.6 Discovery Boot (Post-Seal Wake)

When a sealed device boots after extended storage:

1. **TIME_LOST detection:**
   - If RTC year < 2025, system assumes decade+ dormancy
   - Displays: "KNOWLEDGE ARK RECOVERED"

2. **Orientation message:**
   - Explains what the device is
   - Provides basic usage instructions
   - Designed for unknown future handler

3. **Automatic integrity check:**
   - Verifies all files against stored manifests
   - Reports status:
     - **INTACT ✓** — All files match checksums
     - **DEGRADED ⚠** — Some files corrupted; recovery possible
     - **CRITICAL ✗** — Essential files corrupted; use cold spare

4. **Time sync prompt:**
   - GPS sync or manual date entry
   - Required to exit TIME_LOST state

### 10.7 Seed Vault Storage Checklist

For maximum passive survivability (5-50 years):

- [ ] Corpus complete and verified
- [ ] Conformal coating applied and UV-verified
- [ ] `ark-seal --mode=archival` completed
- [ ] OLED shows "SEALED"
- [ ] Cold spare NVMe cloned and Faraday-bagged
- [ ] Cold spare SD cloned and Faraday-bagged
- [ ] Desiccant fresh (orange/blue indicator)
- [ ] Case sealed in Mylar bag with oxygen absorber
- [ ] Stored in cool (15-25°C), dark, stable location
- [ ] Location documented for future retrieval

## Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0 | Dec 2025 | Initial operations guide |
| 4.0 | Dec 2025 | Extracted to reference appendix |
| 4.1 | Dec 2025 | Added quarterly storage scrub procedure for NAND bit rot prevention. |
| 4.2 | Dec 2025 | Updated power modes for realistic OPi 5+ consumption (CRIT-014). |
| 4.3 | Dec 2025 | Added §6.5 Cold Spare Update (Annual) procedure (HIGH-005). |
| 4.4 | Dec 2025 | HIGH-OPS-001: Na-ion 8S voltage table, analog voltmeter guidance, Lazarus wake procedure, updated charging times and scavenger voltage ranges. |
| **5.0** | **Dec 2025** | **VAULT EDITION (LOW-STG-011): NEW §10 Archival Seal Procedure with seal/unseal commands, manifest generation, Discovery Boot explanation, and Seed Vault Storage Checklist for 50-year passive survivability.** |

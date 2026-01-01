---
version: "5.0.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
tags:
  - spec
  - universal-knowledge-ark
  - reference
  - glossary
---

# Appendix B: Glossary

**Version:** 5.0.0 (Vault Edition)

Term definitions for the Universal Knowledge Ark specifications.

---

## Architecture Terms

**Basement:** Bottom layer of the Cyberdeck architecture. Contains Na-ion 8S4P battery pack, Genasun MPPT, and spare parts in Kaizen foam.

**Cavity:** Open space (60-80mm) between the deck faceplate and the lid when case is open. Stores keyboard, solar panels, cables.

**Cyberdeck:** The physical architecture of the device. Named after the portable computing terminals in cyberpunk fiction. Consists of five layers: Lid, Cavity, Deck Faceplate, Deck Underside, Basement.

**Daily Driver:** The main NVMe-based operating system with full application stack. Boots by default.

**Deck:** The removable electronics assembly. "Faceplate" is the user-facing side; "Underside" contains electronics mounted upside-down on aluminum heatsink.

**Lid Frame:** Laser-cut frame (B01) that holds the E-ink display and mounts to the Pelican lid.

**Lifeboat OS:** Minimal recovery operating system on eMMC. Boots automatically if NVMe fails. Provides Factory Reset capability.

**Zero Hinge Wiring:** Design principle eliminating all cables routed through the hinge. Display connects via user-plugged USB cable stored in cavity.

---

## Power Terms

**8S4P:** Battery pack configuration: 8 series groups × 4 parallel cells. For Na-ion: 8S × 3.0V = 24V nominal, 4P × 10Ah = 40Ah capacity. Total: 960Wh.

**BMS:** Battery Management System. For Lazarus-Phoenix: JK BD4A8S4P external BMS with active balancing, Bluetooth configuration, cell voltage monitoring, and protection circuits. See *JK BMS BD4A8S4P*.

**DPST:** Double-Pole Single-Throw switch. Disconnects two circuits (solar and battery) simultaneously with one action.

**Factory Reset Image:** Factory-fresh backup of the root filesystem stored on nvme0n1p4. Can be restored via Lifeboat OS. Preserves user library on /data partition. User-facing term; equivalent to "Golden Image" in scripts and technical documentation.

**Fever Breaker:** Thermal protection protocol. Software throttles at 70°C, warns at 75°C, shuts down at 80°C. Hardware (KSD9700) cuts power at 85°C.

**Hybrid LVD:** Combined hardware and software low voltage protection. Hardware load disconnect opens at 18V (prevents boot-loop), software triggers graceful shutdown at 18V. Replaces legacy 11.5V/12.0V thresholds from LiFePO4 era.

**Lazarus Protocol:** System wake procedure from 0V decade dormancy. Sequence: Solar connects → passive bootstrap trickle charges battery → voltage rises above MPPT threshold → Genasun takes over → load disconnect closes at 20V → system boots. Typical duration: 4-8 hours in full sun. See *Passive Bootstrap*, *Phoenix Protocol*.

**Load Disconnect:** TL431-based voltage supervisor + Panasonic ADW1124HLW latching relay. Opens at <18V (prevents boot-loop when battery too low), closes at >20V (allows system boot). Draws zero standby current when latched. Critical for Lazarus Protocol.

**LVD:** Low Voltage Disconnect. Generic term for hardware that cuts power when battery voltage drops below threshold. In Lazarus-Phoenix: implemented via *Load Disconnect* at 18V.

**MPPT:** Maximum Power Point Tracking. Solar charge controller technology that extracts 20-30% more power than PWM controllers by dynamically optimizing voltage/current. See *Genasun GVB-8-WP*.

**Na-ion:** Sodium-ion battery chemistry using NFPP (sodium iron phosphate phosphate) cathode and aluminum current collectors. Key property: tolerates 0V storage indefinitely without damage—aluminum doesn't dissolve like copper collectors in lithium cells. Enables decade dormancy (Lazarus Protocol). Trade-off: ~20% lower energy density than LiFePO4.

**Passive Bootstrap:** Failsafe charging circuit: 1N5408 silicon diode + 47Ω 25W resistor wired in parallel with MPPT input. When battery is at 0V and MPPT is inactive, solar current flows through this path at ~0.3A trickle rate. Enables 0V battery recovery without any active electronics. Silicon diode chosen over Schottky for lower reverse leakage during storage.

**Phoenix Protocol:** The Genasun GVB-8-WP's ability to charge a 0V battery directly from solar input. Unlike buck MPPTs (which step DOWN voltage and require battery presence to bootstrap), boost topology steps UP voltage and can operate with dead battery. Named for rising from ashes.

**SOC:** State of Charge. Battery percentage. Na-ion 8S voltage-to-SOC mapping: 12V = 0% (empty), 20V = ~25%, 24V = ~50% (nominal), 28V = ~75%, 32V = 100% (full). Curve is more linear than LiFePO4.

**Solar Fry:** Failure mode where MPPT is destroyed by voltage spike. Occurs when solar is connected but battery is disconnected (no load). Prevented by DPST kill switch.

**Winter Lockout:** Failure mode where device won't boot despite adequate battery charge. Caused by LVD threshold set too high combined with cold-induced voltage sag. Mitigated by 18V load disconnect threshold (well below 24V nominal).

---

## Hardware Terms

**Analog Voltmeter:** Moving-coil 0-30V DC panel meter using d'Arsonval galvanometer mechanism. Contains no silicon, no batteries, no firmware. Provides battery state indication after decade storage when all digital displays are dead. Voltage-to-state: 0V=dormant, 12V=empty, 18V=minimum boot, 20V=operational, 24V=nominal, 32V=full.

**Cold Spare:** Pre-loaded SLC NVMe or SD card stored in Faraday bag within lid foam. Contains complete corpus clone with SHA-256 manifest. Enables full system restoration if primary storage fails after decades of storage.

**Combo-Drive:** Fastener head with both slot and Phillips recesses. Accepts screwdriver, knife blade, or improvised flat tool. Preferred over hex-only for field serviceability.

**Conformal Coating:** Protective polymer coating (MG Chemicals 422B silicone) applied to PCBs. Protects against humidity, salt air, and condensation during long-term storage. UV fluorescent for verification under blacklight.

**DS3231:** Temperature-compensated precision RTC module. Accuracy ±2ppm (±1 minute/year). Critical for long-term offline timekeeping.

**E-ink:** Electrophoretic display technology. Consumes power only during refresh; retains image indefinitely without power. 10.3" Waveshare panel provides 1872×1404 resolution.

**Ferrule:** Metal crimped sleeve for stranded wire. Required for high-current (≥5A) screw terminal connections. For signal wiring, Wago 221 lever nuts are preferred.

**Genasun GVB-8-WP:** Boost MPPT charge controller for Lazarus-Phoenix architecture. Steps UP 18V solar input to 32V battery charging voltage. Fixed-voltage output (no app, no Bluetooth, no configuration). Potted/sealed IP68 construction, 80×50×25mm. Key capability: charges 0V battery from solar (Phoenix Protocol). Replaces Victron SmartSolar.

**HiNa 32140:** Sodium-ion cylindrical cell format manufactured by HiNa Battery Technology (China). Dimensions: 32mm diameter × 140mm length. Specs: 3.1V nominal, 3.9V max, 1.5V min, 10Ah capacity. NFPP cathode chemistry. 32 cells configured as 8S4P = 40Ah / 960Wh pack.

**IT8951:** Display controller for large E-ink panels. Provides USB interface, grayscale support, and sleep mode for near-zero standby power.

**JK BMS BD4A8S4P:** Active balancing Battery Management System for 8S lithium or sodium packs. 4A balance current (much faster than typical 60mA passive balancing). Bluetooth configuration via JK BMS app. Monitors individual cell voltages, pack temperature, charge/discharge current. Provides over-voltage, under-voltage, over-current, and short-circuit protection.

**Kaizen Foam:** Polyethylene foam with pre-scored grid. Allows custom pocket creation by "excavating" squares. Used for battery compartment and spares storage.

**KSD9700:** Bimetal thermal cutoff switch. Opens at 85°C, auto-resets at ~65°C. Wired in series with 5V rail as last-resort thermal protection.

**Li-SOCl2:** Lithium Thionyl Chloride primary (non-rechargeable) battery chemistry. Exceptionally long shelf life (20-40 years at room temperature) with minimal self-discharge. Used for RTC backup in seed vault applications. Tadiran TL-5903 specified for DS3231 module.

**NTC Thermistor:** Negative Temperature Coefficient resistor. Presents high resistance when cold (5Ω), drops to low resistance (<0.1Ω) when heated by current flow. Used to limit inrush current when connecting battery to capacitive loads, preventing switch contact welding.

**NPU:** Neural Processing Unit. Dedicated AI accelerator in RK3588 SoC. Provides 6 TOPS for inference acceleration via RKLLM.

**OPi 5+:** Orange Pi 5 Plus single-board computer. 32GB RAM, RK3588 SoC, NVMe support. Primary compute platform.

**PAR2:** Parchive version 2. Reed-Solomon error correction format that generates parity files allowing reconstruction of corrupted data. Used to protect Factory Reset Image from NAND bit rot during long-term storage.

**Pelican 1557 Air:** Waterproof (IP67), crushproof case from Pelican's lightweight Air series. Interior 440×330×248mm (17.33"×13.00"×9.75"). HPX² polymer construction is 40% lighter than standard Protector cases. Orange color for high visibility. 196mm base depth provides generous clearance for Na-ion 8S4P pack. Note: water-tight for liquid but vapor-permeable over years; use Mylar bag for extended storage.

**Polymer Capacitor:** Solid-state capacitor using conductive polymer instead of liquid electrolyte. Does not dry out over decades. Specified for DC-DC converter and amplifier to ensure 10+ year dormant storage reliability.

**PV Bootstrap:** Generic capability of some MPPT controllers to power internal logic from solar input when battery is dead. In Lazarus-Phoenix: provided by Genasun GVB-8-WP boost topology (Phoenix Protocol) plus passive bootstrap circuit as failsafe.

**PVC Posts:** Schedule 40 PVC pipe sections used as deck support pillars. Gravity-loaded, scavengeable, cold-tolerant. Replaces complex 3D-printed expansion rails.

**Pyrolytic Graphite Pad:** Solid-state thermal interface material. Unlike silicone-oil thermal paste that dries out over 5-10 years, graphite pads contain no volatile solvents and remain effective indefinitely.

**ReSpeaker:** Seeed USB microphone array with onboard DSP (XMOS XVF-3000). Provides acoustic echo cancellation, beamforming, and noise suppression in hardware.

**SLC NAND:** Single-Level Cell NAND flash memory storing exactly 1 bit per cell. Provides highest data retention (50+ years extrapolated at 25°C) and endurance (100,000 P/E cycles) due to wide voltage margins between states. Swissbit X-76m (NVMe) and S-56 (SD) selected for Vault Edition. Contrast with TLC (3 bits/cell) which requires quarterly refresh and fails within 1-3 years unpowered.

**Storm Window:** Polycarbonate sheet mounted with air gap in front of E-ink display. Absorbs impact to prevent screen damage when lid is closed forcefully.

**USB-C Breakout Board:** PCB with USB-C connector and screw terminals. Converts soldered pigtail to replaceable cable connection. Moves wear component to commodity item.

**VHB:** 3M Very High Bond tape. Industrial adhesive rated for high shear loads. Used for component mounting (with mechanical backup where critical).

**Victron SmartSolar:** Industrial-grade buck MPPT charge controller. *(Historical—replaced by Genasun GVB-8-WP in Lazarus-Phoenix architecture.)* Had PV bootstrap capability and Bluetooth configuration, but buck topology cannot charge 0V battery and controller draws standby current during storage.

**Wago 221:** Lever-actuated wire connectors. Accept stripped stranded wire 24-12 AWG without tools. Reusable, scavenger-friendly. Preferred for signal wiring.

---

## Software Terms

**Action Card:** Single-instruction display format for CRITICAL priority content. Shows one command per screen in large font for users under extreme stress. Advances with SPACE or ENTER. Example: "APPLY TOURNIQUET" with minimal supporting text.

**Archival Seal:** Write-once mode activated by `ark-seal --mode=archival`. Remounts /data and /archive partitions read-only, generates SHA-256 integrity manifests with triple redundancy (NVMe, SD, eMMC), and sets seal flag. Transforms device from active use to passive seed vault. Reversible via `ark-unseal --confirm`.

**Curated Content:** Pre-loaded library content (Wikipedia, Hesperian, StackExchange dumps) vetted during device build. Displayed with `[CURATED]` badge in search results.

**Discovery Boot:** Boot sequence triggered when TIME_LOST condition detected (RTC year < 2025). Displays "KNOWLEDGE ARK RECOVERED" orientation message for unknown future handler, runs automatic integrity verification, reports INTACT/DEGRADED/CRITICAL status. Designed for someone who finds the device decades from now with no prior knowledge.

**Emergency Fast-Path:** Keyword detection that intercepts emergency queries before LLM processing. When high-threat words (blood, bone, breathing) are detected, offers immediate redirect to Survival Menu (<200ms) rather than waiting for AI response (10-30s).

**FTS5:** SQLite Full-Text Search version 5. Provides fast keyword search without requiring LLM or embeddings.

**Graceful Degradation:** Core design principle where system remains partially functional even with component failures. When conflicts arise, reliability and repairability take priority over capability. Examples: Safe Mode provides basic search when Python crashes; device operates on single solar panel at reduced charge rate; partial library access if some storage blocks fail.

**Hybrid Search:** Search strategy combining FTS5 keyword matching with vector similarity (embeddings). Results merged via Reciprocal Rank Fusion.

**Import USB:** Process of importing documents into the knowledge library from USB drive. Involves parsing, chunking, embedding, and indexing. High-power operation gated by battery level. (User-facing name for "Ingest" in technical specs.)

**Integrity Manifest:** SHA-256 checksums of all corpus files generated at seal time by `ark-seal`. Stored with triple redundancy (NVMe /recovery/manifests/, SD /archive/.manifests/, eMMC Lifeboat). Used by Discovery Boot to detect bitrot after decades of storage. Reports INTACT (all match), DEGRADED (some corrupted), or CRITICAL (essential files corrupted).

**Library:** The knowledge base containing all indexed documents. Includes both pre-loaded survival content and user-imported materials. (User-facing name for "Corpus" in technical specs.)

**MARCH Protocol:** Military triage order: Massive hemorrhage, Airway, Respiration, Circulation, Hypothermia. The Red Flag Interceptor enforces this priority by redirecting users from distraction injuries to lethal ones.

**OverlayFS:** Linux filesystem that presents a merged view of read-only base and RAM-backed upper layer. Protects root filesystem from corruption.

**Panic Menu:** The Survival Menu. Designed for users under extreme stress. Requires only single-key navigation, no typing, no LLM required.

**Passive Survivability:** Design philosophy (Vault Edition) prioritizing device function after 50 years of unattended sealed storage without any human maintenance. Achieved through SLC NAND storage, Li-SOCl2 RTC battery, conformal coating, archival seal mode, and defense-in-depth with cold spares. Contrast with "active stewardship" which assumes quarterly maintenance.

**RAG:** Retrieval-Augmented Generation. Architecture where LLM responses are grounded in retrieved library chunks rather than relying solely on model training.

**Red Flag Interceptor:** Triage guard that displays before certain menu categories. Asks critical safety question (e.g., "Is there spurting blood?") before showing treatment content. Prevents users from treating dramatic injuries (exposed bone) while missing lethal ones (arterial bleed).

**Safe Mode:** Emergency fallback when Python crashes. Provides text-based menu and search via bash scripts directly on E-ink or terminal. (Formerly "Cockroach Mode" in early specs.)

**Safety Banner:** Warning displayed above search results for medical or chemical queries. Reminds users to verify dosages, ratios, and procedures from multiple sources. Does not block access — provides epistemic humility rather than censorship.

**Source Attribution:** Display of source document, source type badge, and page number for each search result. Enables users to evaluate reliability and trace information origin.

**Survival Menu:** Hierarchical menu of emergency topics (Medical, Water, Fire, etc.). Content retrieved by pre-tagged library lookup, not AI inference. Works even when LLM is dead. Uses symptom-based labels ("BLEEDING / BLOOD") rather than clinical terms ("Hemorrhage").

**User-Imported Content:** Library content added via Import USB function. Not vetted by device build process. Displayed with `[UNVERIFIED]` badge in search results to indicate it has not been reviewed for accuracy.

---

## Operational Terms

**Alligator Whip:** Scavenger cable with banana plugs on one end and alligator clips on the other. For connecting to car batteries, marine batteries, or improvised power sources.

**Bare Wire Whip:** Scavenger cable with banana plugs on one end and tinned bare wire on the other. For connecting to unknown or stripped power sources.

**Lazarus Wake:** Common shorthand for executing the Lazarus Protocol—waking the system from 0V decade dormancy via solar charging. See *Lazarus Protocol*.

**Pre-Flight Checklist:** Verification steps before sealing case for storage. Covers power state, component placement, desiccant, and faraday integrity.

**Scavenger Cables:** Pre-made cables for harvesting power from non-standard sources. Stored in lid foam for field use.

**Time Amnesia:** See *TIME_LOST*.

**TIME_LOST:** System state detected on boot when RTC year < 2025, indicating the device has been dormant long enough for RTC battery to fail or RTC to lose calibration. Triggers Discovery Boot sequence with orientation message and integrity verification. Named for the condition, not a protocol.

---

## AI/ML Terms

**espeak-ng:** Open-source speech synthesizer. Robotic but reliable TTS fallback for low-power mode.

**FAISS:** Facebook AI Similarity Search. Vector database library for efficient semantic retrieval. Uses IVFFlat index type.

**llama.cpp:** Open-source C++ library for running LLM inference on CPU. Fallback when RKLLM/NPU unavailable.

**MiniLM:** Compact sentence embedding model (all-MiniLM-L6-v2). Generates 384-dimensional vectors for semantic search.

**Piper:** Neural text-to-speech engine. Primary TTS providing natural-sounding voice output.

**RKLLM:** Rockchip's inference runtime optimized for RK3588 NPU. Runs quantized LLM models at 3-6 tokens/second for 8B models.

**VOSK:** Offline speech recognition toolkit. Low-power STT fallback with ~50MB model size.

**Whisper:** OpenAI speech-to-text model. Primary STT via whisper.cpp ARM implementation.

**ZIM:** OpenZIM archive format for offline content. Stores compressed Wikipedia, iFixit, WikiHow as single files.

---

## Acronyms

| Acronym | Meaning |
|---------|---------|
| AEC | Acoustic Echo Cancellation |
| BMS | Battery Management System |
| CME | Coronal Mass Ejection |
| DPST | Double-Pole Single-Throw |
| EMP | Electromagnetic Pulse |
| ETFE | Ethylene Tetrafluoroethylene (solar panel coating) |
| FTS | Full-Text Search |
| GPIO | General Purpose Input/Output |
| GPS | Global Positioning System |
| I2C | Inter-Integrated Circuit (serial bus) |
| IP67 | Ingress Protection (dust-tight, 1m immersion) |
| LLM | Large Language Model |
| LVD | Low Voltage Disconnect |
| MPPT | Maximum Power Point Tracking |
| NFPP | Sodium Iron Phosphate Phosphate (Na-ion cathode) |
| NPU | Neural Processing Unit |
| NVMe | Non-Volatile Memory Express |
| OLED | Organic Light-Emitting Diode |
| PWM | Pulse Width Modulation |
| RAG | Retrieval-Augmented Generation |
| RTC | Real-Time Clock |
| SLC | Single-Level Cell (NAND flash) |
| SOC | State of Charge |
| SPI | Serial Peripheral Interface |
| SPST | Single-Pole Single-Throw |
| STT | Speech-to-Text |
| TTS | Text-to-Speech |
| TVS | Transient Voltage Suppressor |
| UAC | USB Audio Class |
| WAL | Write-Ahead Logging |

---

## Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0 | Dec 2025 | Initial glossary |
| 4.0 | Dec 2025 | Extracted to reference appendix |
| 4.1 | Dec 2025 | Added AI/ML Terms section; removed duplicate Wago 221 |
| 4.2 | Dec 2025 | Minor corrections |
| 4.3.0 | Dec 2025 | Lazarus-Phoenix alignment: Updated 6 terms, removed Eco Mode, added 10 new terms, added NFPP acronym |
| **5.0.0** | **Dec 2025** | **VAULT EDITION: Added SLC NAND, Archival Seal, Discovery Boot, Integrity Manifest, Li-SOCl2, Passive Survivability, Conformal Coating, TIME_LOST. Updated Cold Spare for SLC storage.** |

---

**END OF DOCUMENT**

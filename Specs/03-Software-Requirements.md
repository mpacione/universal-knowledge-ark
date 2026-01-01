---
version: "2.8.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
source: "~/universal-knowledge-ark/specs/03-software-requirements.md"
tags:
  - spec
  - universal-knowledge-ark
  - software
---

# Software Requirements

**Version:** 2.8.0 (Vault Edition)

## Summary

Functional and non-functional requirements for the Universal Knowledge Ark software system.

## Key Capabilities

- **Knowledge Query** — Natural language questions answered via RAG + LLM
- **Voice I/O** — Whisper STT, Piper TTS for hands-free operation
- **Power Management** — Na-ion voltage monitoring, graceful shutdown
- **Archival Mode** — `ark-seal` for 50-year storage integrity
- **Discovery Boot** — Orientation sequence for unknown future handlers

## Functional Requirements

| ID | Requirement |
|----|-------------|
| FR-01 | System shall respond to knowledge queries within 15s (NPU) / 30s (CPU) |
| FR-02 | System shall operate on 32GB RAM without swap |
| FR-03 | System shall provide voice input/output as primary interface |
| FR-04 | System shall monitor battery voltage and trigger graceful shutdown at 18V |
| FR-05 | System shall support archival sealing with integrity manifests |

## Archival Mode (Vault Edition)

- `ark-seal` command remounts /data read-only
- SHA-256 manifests stored with triple redundancy
- Discovery boot detects TIME_LOST (RTC < 2025)
- Reports INTACT / DEGRADED / CRITICAL status

## Dependencies

- [[01-Hardware-Specification]] — Hardware constraints inform requirements
- [[04-Technical-Design]] — Implementation of these requirements
- [[05-Library-Manifest]] — Content that software must serve

---

## 1. Functional Requirements

### 1.1 Core Capabilities

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-01 | System shall accept natural language queries via USB keyboard | Must |
| FR-02 | System shall accept voice queries via push-to-talk activation | Should |
| FR-03 | System shall retrieve relevant knowledge from local vector database | Must |
| FR-04 | System shall generate coherent responses using local LLM with NPU acceleration | Must |
| FR-05 | System shall display responses on 10.3" E-ink screen (1872×1404) | Must |
| FR-06 | System shall optionally read responses aloud via TTS | Should |
| FR-07 | System shall operate completely offline | Must |
| FR-08 | System shall manage power states automatically | Must |
| FR-09 | System shall preserve query/response history | Should |
| FR-10 | System shall provide bookmarking functionality | Should |
| FR-11 | System shall support multiple knowledge domains | Should |
| FR-12 | System shall display battery SOC on OLED and E-ink (±5% accuracy via coulomb counting) | Must |

### 1.2 Knowledge Retrieval

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-13 | System shall use RAG for context-aware responses | Must |
| FR-14 | System shall support semantic search across all content | Must |
| FR-15 | System shall rank results by relevance | Must |
| FR-16 | System shall cite sources in responses | Should |
| FR-17 | System shall support domain-filtered queries | Should |
| FR-18 | System shall handle queries with no relevant results gracefully | Must |

### 1.3 User Interface

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-19 | System shall display text at readable font sizes (20pt minimum body) | Must |
| FR-20 | System shall support scrolling through long responses via D-pad | Must |
| FR-21 | System shall provide visual feedback during processing | Must |
| FR-22 | System shall support navigation via industrial D-pad + buttons | Must |
| FR-23 | System shall provide accessible one-handed operation | Should |
| FR-24 | System shall remember user's last position on power cycle | Should |
| FR-25 | System shall show persistent status on OLED (voltage, SOC, state) | Must |

### 1.4 Audio Interface

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-26 | System shall support push-to-talk voice input via ReSpeaker | Should |
| FR-27 | System shall provide on-demand TTS output (default off) | Should |
| FR-28 | System shall allow user to configure TTS preference (off/on-demand/auto) | Should |
| FR-29 | System shall provide audio feedback for PTT activation/deactivation | Should |

### 1.5 Power Management

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-30 | System shall enter low-power sleep after configurable idle period | Must |
| FR-31 | System shall wake on button press within 10 seconds | Must |
| FR-32 | System shall perform graceful shutdown via software LVD at 18.0V (Na-ion 8S) | Must |
| FR-33 | System shall detect and display charging status via INA219 current sensing | Must |
| FR-34 | System shall prevent operation during charging if battery <5% | Should |

### 1.6 Data Protection

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-35 | System shall maintain PAR2 parity data for corruption recovery | Must |
| FR-36 | System shall checkpoint user data to SD card periodically | Should |
| FR-37 | System shall boot from eMMC Lifeboat OS if NVMe fails | Must |
| FR-38 | System shall guide user through cold spare replacement if needed | Should |

### 1.7 Archival Mode (Vault Edition)

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-39 | System shall provide `ark-seal` command to enter archival mode | Must |
| FR-40 | Archival mode shall remount /data and /archive as read-only | Must |
| FR-41 | Archival mode shall generate SHA-256 integrity manifests for all corpus files | Must |
| FR-42 | Integrity manifests shall be stored with triple redundancy (NVMe, SD, eMMC) | Must |
| FR-43 | System shall set seal flag preventing further writes until explicit unseal | Must |
| FR-44 | Archival seal shall be reversible via `ark-unseal` with confirmation | Should |
| FR-45 | System shall display SEALED status on OLED when in archival mode | Must |

### 1.8 Discovery Boot (Vault Edition)

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-46 | System shall detect TIME_LOST condition (RTC year < 2025) on boot | Must |
| FR-47 | Discovery boot shall display "KNOWLEDGE ARK RECOVERED" on E-ink | Must |
| FR-48 | Discovery boot shall run automatic integrity verification against manifests | Must |
| FR-49 | Integrity status shall be displayed as INTACT / DEGRADED / CRITICAL | Must |
| FR-50 | If DEGRADED, system shall offer recovery from SD backup | Should |
| FR-51 | Discovery boot shall provide orientation message for unknown future handler | Must |
| FR-52 | System shall allow manual date entry or GPS sync to exit TIME_LOST | Must |

---

## 2. User Stories

### 2.1 Primary Use Cases

**US-01: Basic Knowledge Query (Keyboard)**
> As a survivor, I want to type "How do I purify water?" so that I can make safe drinking water from available sources.

Acceptance:
- Query submitted via USB keyboard
- Response displayed within 30 seconds
- Response includes step-by-step instructions
- Sources cited if from specific reference

**US-02: Voice Query**
> As a user with hands busy, I want to hold a button and ask "What are the signs of infection?" so I can get information without typing.

Acceptance:
- PTT activated via D-pad center hold (or designated button)
- Audio feedback confirms listening state
- Query transcribed and processed
- Response displayed on E-ink (and optionally read aloud)

**US-03: Medical Emergency Reference**
> As a caregiver, I want to look up "signs of dehydration" so that I can assess someone's condition.

Acceptance:
- Medical queries prioritize safety information
- Response includes when to seek professional help
- Related topics suggested (treatment, prevention)

**US-04: Agricultural Planning**
> As a farmer, I want to know "when to plant tomatoes in my region" so that I can plan my garden.

Acceptance:
- Response considers seasonal context if possible
- Includes both timing and conditions
- Links to related growing information

**Note:** Example uses USDA hardiness zones (North American system). System should handle equivalent regional queries worldwide (e.g., UK RHS zones, Australian climate zones, Köppen classifications). Knowledge base includes global agricultural references.

**US-05: Technical Repair**
> As a mechanic, I want to find "diesel engine troubleshooting" so that I can diagnose a vehicle problem.

Acceptance:
- Response structured as diagnostic flowchart
- Includes common failure modes
- References required tools
- Illustrated content from iFixit when available

### 2.2 System Interaction Stories

**US-06: Power Conservation**
> As a user, I want the device to sleep automatically so that I don't drain the battery unnecessarily.

Acceptance:
- Sleep after 5 minutes of inactivity (configurable)
- Display preserves last content (E-ink persistence)
- OLED continues showing battery status
- Wake via any button press

**US-07: Session Continuity**
> As a user, I want to continue where I left off so that I don't lose my research context.

Acceptance:
- Last query and response displayed on wake
- History accessible via menu
- Bookmarks preserved across power cycles
- State checkpointed to SD card

**US-08: Domain Browsing**
> As a learner, I want to browse by topic so that I can explore without specific queries.

Acceptance:
- Top-level domain categories accessible via menu
- Drill-down navigation within domains
- Random/featured content option

**US-09: Listen to Response**
> As a user resting my eyes, I want to press a button to have the current response read aloud.

Acceptance:
- Dedicated key triggers TTS of current response
- Audio plays through internal speaker
- User can interrupt playback with any button

**US-10: Night Operations**
> As a user in low-light conditions, I want to use the device without disturbing others.

Acceptance:
- OLED provides minimal status light
- E-ink readable with USB gooseneck LED
- Audio can be disabled entirely

### 2.3 Archival & Recovery Stories (Vault Edition)

**US-11: Archival Seal**
> As an archivist preparing the Ark for long-term storage, I want to seal the corpus so that no accidental writes can corrupt the data during decades of dormancy.

Acceptance:
- `ark-seal --mode=archival` command available
- All data partitions remounted read-only
- SHA-256 manifests generated for all files
- Manifests copied to NVMe, SD, and eMMC
- OLED shows "SEALED" status
- Write attempts fail gracefully with explanation

**US-12: Discovery by Unknown Future Handler**
> As a future discoverer who has never seen this device, I want clear guidance on what it is and how to use it so that I can access the preserved knowledge.

Acceptance:
- If RTC shows year < 2025, assume TIME_LOST
- E-ink displays "KNOWLEDGE ARK RECOVERED" message
- Orientation text explains device purpose
- Integrity check runs automatically
- Status shows INTACT / DEGRADED / CRITICAL
- GPS or manual date entry available to sync time

**US-13: Integrity Verification**
> As a user after long-term storage, I want to verify the corpus is intact so that I can trust the information.

Acceptance:
- `ark-verify` command runs full integrity check
- Progress displayed on E-ink
- Comparison against stored SHA-256 manifests
- Report shows: files checked, passed, failed
- If failures detected, offers recovery from SD backup

**US-14: Unseal for Updates**
> As an archivist updating the corpus, I want to unseal the Ark so that I can add new content before resealing.

Acceptance:
- `ark-unseal` command with confirmation prompt
- Warning about breaking archival integrity
- Partitions remounted read-write
- OLED shows "UNSEALED" status
- New seal generates fresh manifests

---

## 3. UI Specification

### 3.1 Screen Layout (1872×1404)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ ▣ Ark                           ⚡87%    🔋 READY    12:34                  │ ← Status bar (48px)
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  How do I start a fire without matches?                                    │ ← Query (36pt bold)
│                                                                             │
│  ───────────────────────────────────────────────────────────────────────── │
│                                                                             │
│  There are several methods to start a fire without matches:                │ ← Response (24pt)
│                                                                             │
│  FRICTION METHODS                                                           │
│                                                                             │
│  Bow drill: Create a spindle from dry softwood (willow, poplar, or        │
│  cottonwood work well). Carve a fireboard with a notch cut to the         │
│  center. Wrap cordage around the spindle and use a curved stick as        │
│  the bow. Apply downward pressure while sawing back and forth until       │
│  an ember forms in the notch. Transfer ember to tinder bundle.            │
│                                                                             │
│  Hand drill: Simpler setup but requires more skill and endurance.          │
│  Use a straight, dry spindle 45-60cm long. Spin between palms while       │
│  pressing down. Maintain consistent pressure and speed.                    │
│                                                                             │
│  SPARK METHODS                                                              │
│                                                                             │
│  Flint and steel: Strike high-carbon steel against sharp flint edge       │
│  to produce sparks. Direct sparks onto char cloth or dry tinder.          │
│                                                                             │
│                                                                      [▼]   │ ← Scroll indicator
├─────────────────────────────────────────────────────────────────────────────┤
│  [≡ Menu]      [✚ New]      [★ Save]      [📖 History]      [🔊 Read]     │ ← Action bar (56px)
└─────────────────────────────────────────────────────────────────────────────┘
```

**Scroll Indicator Behavior:**
- `[▼]` appears when content extends below viewport (more to scroll down)
- `[▲]` appears when content extends above viewport (more to scroll up)
- `[▲▼]` appears when content extends both directions (middle of document)
- No indicator when entire content fits in viewport
- Position: bottom-right of content area, 16px from edge
- Updates immediately on D-pad scroll input

### 3.2 OLED Status Display (128×64 or 128×32)

```
┌────────────────────────┐
│ 24.5V  65%  ◉ READY   │
│ ████████████░░░ CHG   │
└────────────────────────┘
```

States: BOOT → READY → BUSY → SLEEP
Charging indicator when solar connected.

**Voltage Range Note:** Na-ion 8S pack voltage ranges 12V (empty) to 32V (full), with 24V nominal. OLED displays actual voltage from INA219.

### 3.3 Navigation Model

```
                    ┌──────────────────────────────────────┐
                    │                HOME                  │
                    │       (last query/response)          │
                    └───────┬──────────────┬───────────────┘
                            │              │
               ┌────────────▼───┐   ┌──────▼────────┐
               │   NEW QUERY    │   │     MENU      │
               │  (keyboard)    │   │  (D-pad nav)  │
               └────────────────┘   └───────────────┘
                                          │
            ┌──────────┬──────────┬───────┴───┬──────────┬──────────┐
            │          │          │           │          │          │
       ┌────▼────┐ ┌───▼───┐ ┌────▼────┐ ┌────▼────┐ ┌───▼───┐ ┌───▼───┐
       │ Browse  │ │History│ │Bookmarks│ │ Audio   │ │Settings│ │ About │
       │ Topics  │ │       │ │         │ │ Options │ │        │ │       │
       └─────────┘ └───────┘ └─────────┘ └─────────┘ └────────┘ └───────┘
```

### 3.4 Input Mapping

| Input | Action |
|-------|--------|
| D-pad Up | Scroll up / Navigate menu up |
| D-pad Down | Scroll down / Navigate menu down |
| D-pad Left | Page up / Previous item |
| D-pad Right | Page down / Next item |
| D-pad Center | Select / Confirm |
| D-pad Center (hold 1s) | Push-to-talk voice input |
| ESC | Back / Cancel |
| Home | Return to home screen |
| Home (hold 2s) | Read current response aloud |
| Power (short) | Wake from sleep |
| Power (long 3s) | Initiate shutdown |
| Reset (recessed) | Hardware reset |

### 3.5 Typography (1872×1404 @ ~227 PPI)

| Element | Font | Size | Weight |
|---------|------|------|--------|
| Status bar | System Sans | 20pt | Regular |
| Query text | System Sans | 36pt | Bold |
| Response body | System Serif | 24pt | Regular |
| Response headings | System Sans | 28pt | Bold |
| Action bar | System Sans | 18pt | Medium |
| OLED status | Monospace | 12px | Regular |

### 3.6 Visual Indicators

| State | E-ink Indicator | OLED Indicator |
|-------|-----------------|----------------|
| Booting | "Starting..." with progress | BOOT |
| Ready/Idle | Normal display | READY |
| Processing query | Animated dots in status bar | BUSY |
| Listening (PTT) | 🎤 microphone icon | MIC |
| Speaking (TTS) | 🔊 speaker icon | SPK |
| No results found | "No results" message | READY |
| Low battery (<20%) | ⚠️ Battery warning | Flashing % |
| Critical battery (<10%) | Full-screen warning | CRIT |
| Charging | ⚡ Charging icon | CHG |
| Sleep mode | Display unchanged | SLEEP (dim) |

---

## 4. Performance Requirements

### 4.1 Response Times

| Operation | Target | Maximum |
|-----------|--------|---------|
| Wake from standby | 2 seconds | 3 seconds |
| Display query interface | 1 second | 2 seconds |
| Voice transcription (10s audio) | 3 seconds | 8 seconds |
| Complete inference (7B model) | 20 seconds | 45 seconds |
| D-pad scroll/navigate | 200 ms | 500 ms |
| TTS start (after request) | 500 ms | 1 second |
| Shutdown (graceful) | 10 seconds | 20 seconds |

### 4.2 Memory Allocation

| Component | Allocation | Notes |
|-----------|------------|-------|
| **OS + kernel** | 1.5 GB | Armbian minimal + drivers |
| **ark-main** | 512 MB | Application controller |
| **ark-display** | 128 MB | E-ink + OLED rendering |
| **ark-power** | 64 MB | Power management daemon |
| **ark-audio** | 512 MB | STT (Whisper) + TTS (Piper) |
| **rkllm-server** | 14 GB | 8B W8A8 model + 4096 context (see 04-technical-design §3.3) |
| **embed-server** | 1 GB | MiniLM + inference cache |
| **Vector index** | 2 GB | Memory-mapped FAISS (reduces RAM pressure) |
| **RAG context cache** | 1 GB | Recent retrievals |
| **Headroom** | ~11 GB | Future expansion, burst allocation |
| **Total** | 32 GB | OPi 5+ RAM |

**Notes:**
- RKLLM and llama.cpp do not run simultaneously; dispatcher selects one runtime
- Vector index is memory-mapped from NVMe, not fully resident in RAM
- 11GB headroom sufficient for kernel buffers, disk cache, and temporary allocations

### 4.3 Storage Layout

Per Hardware Specification v4.0.0 (480GB SLC NVMe → ~447 GiB formatted + 128GB SLC SD):

| Partition | Mount | Size | Purpose |
|-----------|-------|------|---------|
| p1 | /boot | 512 MB | Boot files (FAT32) |
| p2 | / | 20 GB | OS, applications, models (EXT4) |
| p3 | /var | 10 GB | Logs, cache, temp (EXT4) |
| p4 | /recovery | 35 GB | Factory Reset Image + integrity manifests (EXT4) |
| p5 | /data | 381 GB | Knowledge corpus (EXT4, read-only when sealed) |
| SD p1 | /archive | 119 GB | Critical corpus subset (EXT4, read-only) |
| | **NVMe Total** | **~446 GB** | Fits 447 GiB formatted |
| | **SD Total** | **~119 GB** | Independent backup |

**Storage Budget** (authoritative source: 05-library-manifest.md §4):

| Content | Allocation | Partition |
|---------|------------|----------|
| Operating system | 8 GB | / |
| LLM model (8B W8A8 RKLLM) | 8 GB | / |
| Embedding + STT + TTS models | 2 GB | / |
| Application code | 500 MB | / |
| Buffer (33%) | 5.5 GB | / |
| **System subtotal** | **20 GB** | **/** |
| Knowledge library (raw + processed) | 152 GB | /data |
| Library headroom (10%) | 15 GB | /data |
| Expansion reserve | 214 GB | /data |
| **Data subtotal** | **381 GB** | **/data** |
| Critical corpus subset | 119 GB | /archive (SD) |
| **Archive subtotal** | **119 GB** | **/archive** |
| Factory Reset Image | 20 GB | /recovery |
| Integrity manifests | 1 GB | /recovery |
| PAR2 parity (defense-in-depth) | 14 GB | /recovery |
| **Recovery subtotal** | **35 GB** | **/recovery** |

### 4.4 Thermal Constraints

| Condition | Action |
|-----------|--------|
| SoC temp > 70°C | Throttle inference speed (software) |
| SoC temp > 80°C | Pause inference, display warning |
| SoC temp > 85°C | KSD9700 thermal cutoff triggers |
| Ambient > 45°C | Display environmental warning |

---

## 5. Power State Machine

### 5.1 State Diagram

```
                                    ┌───────────────┐
                                    │      OFF      │
                                    │    (0 mW)     │
                                    └───────┬───────┘
                                            │ Power button (3s)
                                            ▼
                                    ┌───────────────┐
                                    │    BOOTING    │
                                    │  (18W peak)   │
                                    └───────┬───────┘
                                            │ Boot complete
                                            ▼
            ┌───────────────────────────────────────────────────────┐
            │                                                       │
            │    ┌───────────────┐                                  │
   Button   │    │    ACTIVE     │◄────────────────────┐            │
   ─────────┼───►│  (10W query)  │                     │            │
            │    │  (5W browse)  │─────────────────────┤            │
            │    └───────┬───────┘     Any button      │            │
            │            │                             │            │
            │            │ Idle 5 min (configurable)   │            │
            │            ▼                             │            │
            │    ┌───────────────┐                     │            │
   Button   │    │    STANDBY    │─────────────────────┘            │
   ─────────┼───►│   (2.5W)      │     Any button                   │
            │    └───────────────┘                                  │
            │                                                       │
            └───────────────────────────────────────────────────────┘
```

**Design Note:** RK3588 suspend-to-RAM exists but is unreliable on mainline Linux kernels (PCIe/NVMe resume failures, requires custom devicetree overlays). This simplified state machine prioritizes reliability over theoretical power savings. With 960Wh battery capacity (Na-ion 8S4P), ~11 days standby is achievable at 2.8W—sufficient for the intended use pattern of active reading sessions followed by extended standby.

### 5.2 State Definitions

| State | Description | Power | OLED | Wake Trigger |
|-------|-------------|-------|------|--------------|
| OFF | Complete shutdown | 0 mW | Off | Power 3s |
| BOOTING | System initialization | 18W peak | BOOT | N/A |
| ACTIVE (Query) | LLM inference, NPU active | 10W | BUSY | N/A |
| ACTIVE (Browse) | Reading content, light CPU | 5W | READY | N/A |
| STANDBY | Display blanked, services stopped, CPU min freq | 2.5W | SLEEP | Any button |

### 5.3 Transition Triggers

| From | To | Trigger |
|------|-----|---------|
| OFF → BOOTING | Power button long press (3s) |
| BOOTING → ACTIVE | Boot sequence complete |
| ACTIVE → STANDBY | 5 minutes idle (configurable 1-30 min) |
| STANDBY → ACTIVE | Any button press (~3s wake) |
| ANY → OFF | Power button long (3s) + confirm |
| ANY → OFF | Software LVD at 18.0V (graceful) |
| ANY → OFF | Load disconnect opens at 18.0V (prevents boot-loop) |

### 5.4 Power Consumption Targets

| Mode | Target | Maximum | Notes |
|------|--------|---------|-------|
| Active (query) | 10W | 15W | NPU inference, NVMe active |
| Active (browse) | 5W | 7W | Reading, light CPU load |
| Standby | 2.5W | 3W | Services stopped, CPU @ 408MHz, peripherals disabled |
| Off | 0 mW | 10 mW | Hardware standby current only |

**Measurement basis:** Orange Pi 5+ idle draws 2-3W with all peripherals active. Standby target achieved by: stopping inference servers, setting CPU governor to powersave, disabling HDMI/WiFi/BT, blanking display. E-ink maintains last image with zero power.

### 5.5 Runtime Estimates

Based on Na-ion 8S4P 40Ah (960Wh, usable 768Wh to 80% DoD):

| Mode | Runtime | Notes |
|------|---------|-------|
| Active Query (continuous) | ~38 hours | Sustained LLM inference @ 20W |
| Active Browse (continuous) | ~137 hours (~6 days) | Reading, occasional queries @ 5.6W |
| Standby | ~274 hours (~11 days) | Display blanked, awaiting input @ 2.8W |
| Mixed use (2h active/day) | ~35 days | Typical field use pattern |

**Realistic scenario:** User reads for 2 hours/day (browse mode), makes 10 queries (5 min total at query power), device in standby remaining 22 hours. Daily consumption: ~22Wh. Battery lasts ~35 days between charges.

**Trade-off Note:** Runtime reduced ~25% vs original LiFePO4 spec (1280Wh → 960Wh). Trade-off accepted for decade dormancy capability—the original chemistry would be dead or dangerous after 10 years storage.

---

## 6. Data Persistence

### 6.1 Storage Requirements

| Data Type | Location | Retention |
|-----------|----------|-----------|
| Query history | /data/user/history.db (SQLite) | 10,000 queries max (oldest auto-pruned; user can clear) |
| Bookmarks | /data/user/bookmarks.db (SQLite) | Permanent |
| User preferences | /data/user/config.json | Permanent |
| Session state | /data/user/session.json | Last session only |
| System logs | /var/log/ark/ (rolling) | 7 days |
| Error logs | /var/log/ark/error/ (rolling) | 30 days |
| PAR2 parity | /recovery/parity/ | Permanent |

### 6.2 Backup Strategy

| What | How | When |
|------|-----|------|
| User data | Checkpoint to SD card | Daily + on graceful shutdown |
| Preferences | Copy to SD card | On change |
| Session state | Write to NVMe | Every 5 minutes |
| Knowledge base | PAR2 parity on /recovery | At build time |

### 6.3 Corruption Recovery

| Scenario | Detection | Recovery |
|----------|-----------|----------|
| User database corrupt | SQLite integrity check | Restore from SD checkpoint |
| Preferences corrupt | JSON parse failure | Reset to defaults |
| Knowledge base corrupt | PAR2 verification | PAR2 repair attempt |
| PAR2 repair fails | Repair returns error | Boot Lifeboat, restore from /recovery |
| NVMe unresponsive | Boot failure or I/O errors | Boot Lifeboat from eMMC, guide cold spare swap |
| System files corrupt | Boot failure | Boot Lifeboat, reinstall from /recovery |

### 6.4 Data Format Specifications

**Query History Schema:**
```sql
CREATE TABLE queries (
    id INTEGER PRIMARY KEY,
    timestamp DATETIME NOT NULL,
    query_text TEXT NOT NULL,
    query_source TEXT DEFAULT 'keyboard', -- 'keyboard' or 'voice'
    response_text TEXT,
    response_sources TEXT, -- JSON array of citations
    domain TEXT,
    duration_ms INTEGER,
    tts_played BOOLEAN DEFAULT FALSE
);

CREATE TABLE bookmarks (
    id INTEGER PRIMARY KEY,
    query_id INTEGER REFERENCES queries(id),
    created DATETIME NOT NULL,
    note TEXT
);

CREATE INDEX idx_queries_timestamp ON queries(timestamp);
CREATE INDEX idx_queries_domain ON queries(domain);
```

**User Preferences Schema:**
```json
{
  "version": 1,
  "display": {
    "font_size": "medium",
    "high_contrast": false
  },
  "audio": {
    "tts_enabled": false,
    "tts_mode": "on_demand",
    "tts_voice": "default",
    "ptt_button": "center_hold"
  },
  "power": {
    "standby_timeout_minutes": 5
  },
  "privacy": {
    "save_history": true,
    "save_voice_queries": false
  }
}
```

---

## 7. Acceptance Criteria

### 7.1 Functional Acceptance

| Requirement | Test | Pass Criteria |
|-------------|------|---------------|
| FR-01 | Submit query via keyboard | Query appears on E-ink |
| FR-02 | Hold PTT, speak query | Transcription appears, response generated |
| FR-03 | Query with known answer | Relevant content retrieved |
| FR-04 | Complete query cycle | Coherent response in <45s |
| FR-05 | View response | Text rendered correctly on 1872×1404 E-ink |
| FR-06 | Press "Read" button | TTS plays response through speaker |
| FR-07 | N/A — device has no network hardware by design | Verified by architecture |
| FR-12 | Check OLED | Battery voltage ±0.1V, SOC ±5% (after calibration cycle) |
| FR-32 | Drain battery to 18.0V | Graceful shutdown initiated |

### 7.2 Performance Acceptance

| Metric | Test | Pass Criteria |
|--------|------|---------------|
| Wake time (standby) | Time from button to query screen | ≤3 seconds |
| Voice transcription | 10-second spoken query | ≤8 seconds |
| Query response | Time from submit to complete response | ≤45 seconds |
| D-pad latency | Time from press to screen update | ≤500 ms |
| Memory usage | Monitor during stress test | ≤14 GB |
| Thermal | 30-minute continuous queries | SoC ≤75°C |

### 7.3 Reliability Acceptance

| Requirement | Test | Pass Criteria |
|-------------|------|---------------|
| Power failure | Kill power during operation | Boots successfully, data intact |
| Repeated wake/sleep | 1000 cycles | No failures, no memory leaks |
| Extended operation | 8 hours continuous use | No crashes |
| Storage stress | Fill history to 90% capacity (~9,000 sessions at 10MB avg), clear, repeat 10× | No corruption |
| PAR2 recovery | Corrupt knowledge file, run repair | File restored correctly |
| Lifeboat boot | Remove NVMe, power on | eMMC Lifeboat OS boots |

### 7.4 Audio Acceptance

| Requirement | Test | Pass Criteria |
|-------------|------|---------------|
| PTT activation | Hold D-pad center 1s | Audio feedback, MIC state shown |
| Voice capture | Speak 20-word query | Accurate transcription |
| Noise rejection | Query with background noise | Beamforming isolates voice |
| TTS playback | Request read-aloud | Clear audio, no distortion |
| TTS interrupt | Press button during playback | Audio stops immediately |
| Low-power fallback | Enable low-power mode, use TTS | espeak-ng plays correctly |

### 7.5 Usability Acceptance

| Requirement | Test | Pass Criteria |
|-------------|------|---------------|
| Readability | View in direct sunlight | Text clearly visible |
| Navigation | Complete 5 tasks with D-pad only | <3 errors |
| One-handed operation | Navigate with single hand | All functions accessible |
| Error recovery | Cause error, attempt recovery | User can recover without reset |
| OLED visibility | Check status in dark room | Readable, not blinding |
| OLED visibility | Check status in sunlight | Readable |

### 7.6 Archival & Discovery Acceptance (Vault Edition)

| Requirement | Test | Pass Criteria |
|-------------|------|---------------|
| FR-39 | Run `ark-seal --mode=archival` | Command completes, seal flag set |
| FR-40 | Attempt write to /data after seal | Write fails with read-only error |
| FR-41 | Verify manifest exists | /recovery/manifests/data.sha256 present |
| FR-42 | Check manifest locations | Manifests on NVMe, SD, and eMMC |
| FR-45 | Check OLED after seal | Shows "SEALED" status |
| FR-46 | Set RTC to 2020, reboot | TIME_LOST detected |
| FR-47 | Boot with TIME_LOST | E-ink shows recovery message |
| FR-48 | Boot with manifests present | Integrity check runs automatically |
| FR-49 | View integrity results | INTACT / DEGRADED / CRITICAL displayed |
| FR-51 | Read orientation message | Clear explanation of device purpose |
| FR-52 | Connect GPS or enter date | TIME_LOST condition clears |

---

## 8. Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0.0 | Dec 2025 | Initial specification (RPi CM5, 8GB RAM, scroll wheel) |
| 2.0.0 | Dec 2025 | Complete rewrite for OPi 5+ 32GB, D-pad controls, voice I/O, 1872×1404 display, PAR2 recovery |
| 2.1.0 | Dec 2025 | CRIT-014: Replaced unrealistic light/deep sleep (500mW/160mW) with achievable standby (2.5W). Simplified power state machine. Updated runtime estimates. |
| 2.2.0 | Dec 2025 | CRIT-002: Fixed partition math overflow. Aligned storage layout with 01-hardware-specification.md v2.1.0. References 05-library-manifest.md §4 as authoritative source for library sizing (167GB). |
| 2.3.0 | Dec 2025 | CRIT-011: Fixed memory allocation table. Updated to reflect RKLLM 8B W8A8 actual usage (14GB per 04-technical-design §3.3). Reduced RAG cache 2→1GB. Corrected headroom to ~11GB. Added notes on runtime exclusivity and memory-mapping. |
| 2.4.0 | Dec 2025 | HIGH-031/032/040: Updated FR-12 to specify ±5% SOC accuracy via coulomb counting (INA219 current integration). FR-33 now specifies INA219 current sensing for charge detection. Resolves SOC accuracy and charging detection issues. |
| 2.5.0 | Dec 2025 | MED-026 through MED-032 batch: (1) US-04 generalized from "zone 7" to "my region" with note on global agricultural references. (2) PPI corrected 220→227. (3) Added scroll indicator behavior spec. (4) LLM model size corrected 4GB→8GB (W8A8 RKLLM). (5) Query history limited to 10,000 entries with auto-prune. (6) Wake/sleep reliability test increased 100→1000 cycles. MED-030 verified as non-issue (consistent 5-min timeout). |
| 2.6.0 | Dec 2025 | LOW-013: FR-07 offline test updated to "N/A — device has no network hardware by design" (verified by architecture). LOW-014: Storage stress test clarified with concrete capacity target (~9,000 sessions at 10MB avg = 90% of 100GB user space). |
| **2.7.0** | **Dec 2025** | **Lazarus-Phoenix alignment:** Updated all voltage thresholds for Na-ion 8S (24V nominal). FR-32: 12.0V→18.0V. Runtime estimates updated for 960Wh (reduced ~25% from 1280Wh). OLED display example updated. Added trade-off note explaining runtime reduction for decade dormancy capability. |
| **2.8.0** | **Dec 2025** | **VAULT EDITION:** Storage architecture aligned with Hardware Spec v4.0.0. NEW §1.7 Archival Mode requirements (FR-39 through FR-45): ark-seal command, read-only remount, SHA-256 manifests, triple redundancy, seal flag. NEW §1.8 Discovery Boot requirements (FR-46 through FR-52): TIME_LOST detection, recovery display, integrity verification, orientation message. NEW §2.3 Archival & Recovery user stories (US-11 through US-14). §4.3 Storage Layout updated for 480GB SLC NVMe + 128GB SLC SD with /archive partition. NEW §7.6 Archival & Discovery Acceptance tests. HIGH-STG-003 and HIGH-STG-004 resolved. |

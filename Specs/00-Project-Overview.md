---
version: "4.1.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
tags:
  - spec
  - universal-knowledge-ark
---

# Universal Knowledge Ark - Project Overview

**Version:** 4.1.0  
**Last Updated:** 2025-12-29  
**Status:** Specification Complete — Ready for Build (Vault Edition Architecture)

## Quick Links

- [[01-Hardware-Specification]] — BOM, power budget, components
- [[02-Assembly-Specification]] — Build procedures, wiring
- [[03-Software-Requirements]] — Functional requirements
- [[04-Technical-Design]] — Implementation architecture
- [[05-Library-Manifest]] — Knowledge base content
- [[Appendix-A-Threat-Model]] — Risk analysis
- [[Appendix-B-Glossary]] — Term definitions
- [[Appendix-C-Operations-Manual]] — Usage procedures


---

## 1. The Problem

The grid fails. Supply chains collapse. The servers holding humanity's operational knowledge go dark.

Within hours, communities face decisions they're unprepared for. A child has a compound fracture—is it safe to set? The well pump died—which wire is which? The pharmacy burned—what plants can substitute for antibiotics?

Civilization doesn't collapse from lack of knowledge. It collapses from lack of *access* to knowledge at the moment of need.

### 1.1 The Three Gaps

Every disaster creates three distinct knowledge crises:

**The Panic Gap (Hours)**  
Immediate triage. Stop the bleeding. Find safe water. Assess structural damage. People die not from ignorance, but from paralysis—overwhelmed by decisions they've never faced.

**The Infrastructure Gap (Years)**  
The grid isn't coming back. Now what? Rebuilding agriculture, sanitation, power generation, medicine production. These skills exist—but they're locked in specialists' heads or inaccessible servers.

**The Amnesia Gap (Decades)**  
History rhymes when it's remembered. Without continuity, communities repeat mistakes. Lose context. Reinvent poorly. The Dark Ages weren't dark because people were stupid—they simply forgot what Rome knew.

### 1.2 The Missing Backup

We have seed vaults for biology. Gene banks. Frozen embryos of endangered species.

But there is no backup for the *operating system of civilization*—the technical, medical, agricultural, and cultural knowledge that transforms resources into survival.

The Universal Knowledge Ark fills this void.

---

## 2. The Vision: Distributed Resilience

One device creates a leader. Ten thousand devices create a stabilization force.

### 2.1 The 10,000-Unit Mesh

The goal state is a distributed network of Arks held by community pillars: librarians, fire chiefs, nurses, ham radio operators, farmers. People already trusted. Already positioned.

The math of resilience:
- **10,000 units** distributed across population centers
- **50% survival rate** after a major event (conservative assumption)
- **5,000 functional nodes** remaining
- **No community more than a day's walk** from a working doctor, engineer, or teacher—via the AI

This is civilizational insurance. The premium is $30M in hardware. The coverage is continuity of competence.

### 2.2 What the Device Delivers

At the point of need, in any conditions, with zero infrastructure dependency:

| Gap | Capability |
|-----|------------|
| **Panic** | Action Cards—large-text, step-by-step triage. MARCH protocol. Shelter assessment. Water safety. Turns paralysis into procedure. |
| **Infrastructure** | Full Wikipedia, iFixit, WikiHow, OpenStax. How to build a generator. Purify water. Set a bone. Plant a crop rotation. |
| **Amnesia** | History. Literature. Science. Philosophy. The conversation between generations that prevents regression. |

---
## 3. Design Philosophy

To achieve this vision, the device must survive what kills everything else.

### 3.1 Core Principles

| Principle | Implementation |
|-----------|----------------|
| **Reliability over Performance** | Choose proven components over bleeding-edge; accept slower responses for guaranteed operation |
| **Field Repairability** | All repairs possible with common hand tools; no proprietary equipment required |
| **Decade Lifespan** | Component selection based on MTBF, availability of spares, and aging characteristics |
| **Complete Offline Operation** | Zero dependency on external networks; all knowledge self-contained |
| **Graceful Degradation** | System remains partially functional even with component failures |
| **Passive Survivability** | Device must function after 50 years sealed storage without maintenance (Vault Edition) |

### 3.2 Trade-off Hierarchy

When conflicts arise, priority order is:

1. Safety (thermal, electrical, physical)
2. Reliability (MTBF, failure modes)
3. Repairability (tool requirements, part availability)
4. **Survivability** (long-term storage integrity — Vault Edition; storage subsystem exempt from cost ceiling)
5. Usability (response time, interface clarity)
6. Capability (knowledge breadth, inference quality)
7. Cost (total BOM, spare parts inventory)

### 3.3 Anti-Patterns

The following approaches are explicitly rejected:

- **Bleeding-edge components** — Unproven reliability, uncertain availability
- **Proprietary connectors** — Creates dependency on specific manufacturers
- **Cloud-dependent features** — Violates offline operation requirement
- **Sealed/bonded assemblies** — Prevents field repair
- **Software requiring updates** — Must function without connectivity

---

## 4. This Build: V1 "Shield"

The current specification represents the **Shield** variant—optimized for standby readiness and distributed deployment. This is the validated platform that enables the 10,000-unit vision.

### 4.1 Core Specifications

| Parameter | Specification | Rationale |
|-----------|---------------|-----------|
| **Compute** | Orange Pi 5+ 32GB (RK3588, 6 TOPS NPU) | Industrial availability, NPU for local inference, proven platform |
| **Storage** | Swissbit X-76m 480GB SLC NVMe × 2 + Swissbit S-56 128GB SLC SD × 2 | True SLC NAND for 50-year passive retention; cold spares in Faraday bags |
| **Battery** | Na-ion 8S4P 40Ah (960 Wh) — Lazarus-Phoenix architecture | 0V-safe decade dormancy, ~35 days mixed use; aluminum collectors survive full discharge |
| **Solar** | 3× Renogy 50W foldable (150W) + Genasun GVB-8 boost MPPT | 0V battery wake capability; full charge in ~8 hours from depleted |
| **Display** | 10.3" E-ink 1872×1404 (IT8951 USB) + 1.3" OLED + analog voltmeter | Sunlight readable, <1W draw; analog meter for decade-stable status |
| **Enclosure** | Pelican 1557 Air (Orange) + full Faraday lining | IP67, MIL-SPEC drop, EMP protection (>40 dB), 196mm base depth |
| **Input** | 8× IP67 pushbuttons (D-pad + 3 function) + ReSpeaker 4-mic | Field-replaceable, voice fallback, sealed controls |
| **Operating Temp** | 0°C to +60°C (system), -20°C to +60°C (storage) | Industrial components; thermal cutoffs at 85°C (KSD9700) |
| **Weight** | ~18 kg complete | Two-person lift recommended |
| **Response Time** | <15s knowledge query (NPU), <30s (CPU fallback) | Llama 3.1 8B W8A8 via RKLLM NPU |
| **BOM Cost** | $4,776 with contingency | $4,153 base + 15% contingency ($623) — Vault Edition storage premium for 50-year survivability |
| **Deployment Scale** | Designed for 10,000-unit fleet | Critical mass for distributed resilience |

---

## 5. Document Map

### 5.1 Specification Status

| Document | Version | Status |
|----------|---------|--------|
| 00-project-overview.md | 4.1.0 | Complete — Vault Edition |
| 01-hardware-specification.md | 4.0.0 | Complete — Vault Edition |
| 02-assembly-specification.md | 4.0.0 | Complete — Vault Edition |
| 03-software-requirements.md | 2.8.0 | Complete — Vault Edition |
| 04-technical-design.md | 3.1.0 | Complete — Vault Edition |
| 05-library-manifest.md | 2.5.0 | Complete |
| appendix-d-outstanding-issues.md | 1.8.0 | 4 LOW issues remaining |

### 5.2 Specification Hierarchy

```
00-project-overview (this document)
├── 01-hardware-specification (v4.0.0 — Vault Edition)
│   ├── System architecture (5-layer cyberdeck)
│   ├── Storage subsystem (Swissbit SLC NVMe + SD for 50-year retention)
│   ├── Power system (Genasun boost MPPT, Na-ion 8S4P 40Ah, passive bootstrap)
│   └── Bill of Materials ($4,153 + $623 contingency = $4,776)
├── 02-assembly-specification (v4.0.0 — Vault Edition)
│   ├── Enclosure modification (Pelican 1557 Air, full Faraday lining)
│   ├── SLC storage preparation (identity verification, Faraday bag sealing)
│   ├── Conformal coating (MG Chemicals 422B with UV verification)
│   └── Validation tests (Lazarus wake, cold spare imaging)
├── 03-software-requirements (v2.8.0 — Vault Edition)
│   ├── Functional requirements (32GB RAM, voice I/O, archival mode)
│   ├── Archival Mode (ark-seal, integrity manifests, read-only seal)
│   ├── Discovery Boot (TIME_LOST detection, unknown handler orientation)
│   └── UI specification (1872×1404 E-ink, 8-button D-pad nav)
├── 04-technical-design (v3.1.0 — Vault Edition)
│   ├── System architecture (Armbian, Python 3.11, systemd services)
│   ├── LLM integration (Llama 3.1 8B W8A8 via RKLLM NPU + llama.cpp fallback)
│   ├── Archival Subsystem (ArchivalManager, IntegrityVerifier, DiscoveryBootHandler)
│   └── PowerMonitor class (Na-ion voltage thresholds, coulomb counting)
├── 05-library-manifest (v2.5.0)
│   ├── Content sources (ZIM: full Wikipedia 95GB, iFixit, WikiHow, OpenStax)
│   ├── Preprocessing (python-libzim + lxml, atomic import rollback)
│   └── Storage budget (167GB library / 381GB /data partition)
└── Appendices
    ├── D: Outstanding Issues (v1.8.0 — 39 issues catalogued, 4 LOW remaining)
    └── E: Na-ion Pack Assembly (v1.0.0 — 8S4P 40Ah build guide)
```

### 5.3 Reading Order

**For builders:** 01 → 02 → Appendix C (ops manual)  
**For developers:** 03 → 04 → 05  
**For evaluators:** 00 → Appendix A (threats) → 01  

### 5.4 Cross-Reference Dependencies

Key dependencies between specifications:

- **Hardware ↔ Assembly:** Component list must match layout
- **Hardware → Software:** Power states, thermal limits inform requirements
- **Software → Technical Design:** Requirements drive implementation
- **Technical Design ↔ Library:** RAG architecture determines content format
- **All Specs → Glossary:** Terminology must be defined
- **All Specs → Threat Model:** Protection claims must be substantiated

---

## 6. Key Technical Decisions

### 6.1 Hardware Platform

- **Orange Pi 5+ 32GB** selected over Raspberry Pi 5 for RK3588 NPU (6 TOPS) enabling on-device 8B inference
- **10.3" E-ink via IT8951 USB** for sunlight readability and sub-watt display power
- **Pelican 1557 Air with full Faraday lining** balances portability with MIL-SPEC protection
- **Genasun GVB-8-WP** boost MPPT (custom Na-ion profile, 0V battery wake capability)

### 6.2 Power Architecture (Lazarus-Phoenix)

- **Na-ion 8S4P 40Ah** enables decade dormancy at 0V (aluminum collectors, no copper dissolution)
- **Genasun GVB-8 boost MPPT** wakes 0V battery from PV power (no chicken-and-egg problem)
- **Passive bootstrap** (1N5408 + 47Ω) provides Ohm's Law fallback if controller fails
- **Load disconnect** (TL431 + latching relay) prevents boot-loop at low voltage
- **Analog voltmeter** provides zero-silicon status feedback after decade storage
- **Trade-off:** 25% less runtime (960Wh vs 1280Wh) for guaranteed decade survivability

### 6.3 Scavenger Protocol

The device feeds on the ruins of the old world. When purpose-built solar is lost or damaged:

- **Binding post inputs** accept bare wire from any 18-30V source (note: 24V minimum for Na-ion charging)
- **Alligator whip cables** harvest power from TWO scavenged car batteries in series (~25V), marine batteries, random solar panels
- **Genasun boost MPPT** tolerates wide input range (18-30V) for scavenged sources
- **No proprietary charging** — standard voltages, standard connections

### 6.4 Radical Repairability

An invitation to future generations to keep the legacy alive:

- **Standard fasteners only** — Phillips, hex, Torx available in any garage
- **Field-replaceable components** — buttons, fuses, cables swap without soldering
- **Self-documenting** — operations manual stored on device, survives screen death via audio
- **Cold spare strategy** — imaged NVMe in lid; full system restore in 15 minutes

### 6.5 Software Stack

- **RKLLM** as primary NPU inference with **llama.cpp** CPU fallback
- **Llama 3.1 8B W8A8** balances capability with memory constraints
- **Whisper + VOSK** dual STT for accuracy vs. speed tradeoff
- **Piper + espeak** dual TTS for quality vs. reliability tradeoff
- **FAISS IVFFlat** vector store with MiniLM-L6-v2 embeddings

### 6.6 Storage Longevity Strategy (Vault Edition)

The original design assumed active stewardship: regular maintenance, periodic refreshes, quarterly charge cycles. The Vault Edition shifts to passive survivability: the device must function after 50 years of sealed storage without any human intervention.

**SLC NAND Selection:**
- **True SLC** (Swissbit X-76m, S-56) stores 1 bit per cell with wide voltage margins
- Industry data shows 10-year retention at 55°C; extrapolation to 25°C suggests 50+ years
- Contrast with TLC (3 bits/cell): requires quarterly refreshes, fails within 1-3 years at room temperature

**Write-Once Archival Mode:**
- `ark-seal` command remounts /data and /archive read-only
- SHA-256 manifests generated and stored with triple redundancy (NVMe, SD, eMMC)
- Eliminates write amplification, maximizes retention
- Unsealing requires explicit command and regenerates manifests

**Discovery Boot Sequence:**
- Detects TIME_LOST condition (RTC year < 2025) indicating decade+ dormancy
- Displays orientation message for unknown future handler
- Runs automatic integrity verification against stored manifests
- Reports INTACT / DEGRADED / CRITICAL status

**Defense in Depth:**
- Primary: SLC NVMe with 50-year physics margin
- Secondary: SLC SD card with independent corpus subset
- Tertiary: Cold spares (NVMe + SD) in Faraday bags in lid foam
- Quaternary: PAR2 parity data for bitrot recovery

### 6.7 Content Strategy

- **ZIM extraction via python-libzim** (no Kiwix runtime dependency)
- **Full Wikipedia** (~95GB, 6.7M articles) with memory-mapped FAISS index
- **iFixit (5GB) + WikiHow (10GB) + OpenStax (3GB)** for practical knowledge domains
- **230GB utilized** within 404GB /data partition (57% capacity)

### 6.8 Action Card UI

In high-stress scenarios, people cannot read textbooks. The interface offers:

- **Panic Mode** — large-text, step-by-step cards for immediate triage
- **MARCH Protocol** cards for trauma response
- **Decision trees** not paragraphs
- Turns helpless survivors into productive responders

---

## 7. Future Variants

The V1 Shield validates the core platform. Future variants share 80%+ of the design—primarily battery chemistry and use-case optimization.

| Variant | Codename | Use Case | Key Differentiator | Status |
|---------|----------|----------|-------------------|--------|
| **Active Utility** | Prometheus | Off-grid schools, clinics, daily use | LTO cells (20,000+ cycles) for continuous operation | Concept |
| **Standby Readiness** | Shield | Distributed resilience, community caches | Na-ion 8S4P (0V-safe) + Faraday, Lazarus-Phoenix architecture | **V1 — Build Ready** |
| **Deep Archive** | Time Capsule | Decade+ dormancy, cold storage | Shield + M-Disc optical, enhanced hermetic sealing | Research validated |

### 7.1 Prometheus (Daily Driver)

For communities using the device *now*—off-grid schools, remote clinics, expedition teams. Optimized for:

- **20,000+ charge cycles** via LTO (Lithium Titanate) cells
- **Daily charge/discharge** without degradation
- **Higher upfront cost** offset by zero battery replacement over lifespan

### 7.2 Time Capsule (Deep Archive)

For caching against civilizational-scale risk. Designed to survive 20+ years of total neglect:

- **Shield baseline** with Na-ion batteries already 0V-safe
- **M-Disc optical backup** for critical content ("stone data" — 1,000-year archival rating)
- **Enhanced hermetic sealing** with ESD bags, oxygen absorbers, molecular sieve desiccant
- **Extended validation** — longer storage testing before deployment

---

## 8. Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2025-12-25 | Initial specification release |
| 2.0.0 | 2025-12-25 | Complete spec alignment: OPi5+ 32GB platform, 10.3" IT8951 E-ink, Pelican 1500 enclosure, Victron MPPT + 100Ah LiFePO4, full protection chain, RKLLM/llama.cpp hybrid, ZIM extraction pipeline |
| 2.1.0 | 2025-12-27 | Version sync across all specs. Updated to current values: Pelican 1557 Air, dual 50Ah batteries, $3,165 BOM, full Wikipedia (95GB), 230GB library budget. Added appendix-d reference (120 issues resolved). |
| 3.0.0 | 2025-12-27 | Major narrative reframe: Added "The Problem" (Three Gaps), "The Vision" (10k fleet), "Scavenger Protocol", "Radical Repairability", "Action Card UI", "Future Variants" (Prometheus/Shield/Time Capsule). Renamed current build as V1 Shield. |
| 4.0.0 | 2025-12-27 | Lazarus-Phoenix integration: Updated to Na-ion 8S4P 40Ah (960Wh) battery with 0V decade dormancy. Genasun GVB-8 boost MPPT replaces Victron. 150W solar (3×50W). Passive bootstrap + analog voltmeter added. BOM reduced $3,165→$2,712 (-$453). Updated all spec version references. Trade-off: 25% less runtime for guaranteed decade survivability. |
| **4.0.1** | **2025-12-28** | **LOW-OVW-001: Fixed Victron→Genasun references in §6.1, §6.3. Updated §5.1 spec table with actual document versions.** |
| **4.1.0** | **2025-12-29** | **VAULT EDITION:** Complete storage architecture revision for 50-year passive survivability. §3.1: Added "Passive Survivability" principle. §3.2: Inserted "Survivability" at priority #4, storage subsystem exempt from cost ceiling. §4.1: Updated storage to Swissbit SLC, BOM to $4,776. §5.1/5.2: Updated all spec versions to Vault Edition. NEW §6.6: Storage Longevity Strategy documenting SLC selection, ark-seal archival mode, discovery boot sequence, defense-in-depth. Resolves LOW-STG-012. |

---

## 9. Contributors & Review

### 9.1 Primary Author
Matt Pacione — Principal UX Designer, Systems Architecture

### 9.2 Review Status
- [ ] Hardware engineering review
- [ ] Software architecture review
- [ ] Field operations review
- [ ] Security/threat review
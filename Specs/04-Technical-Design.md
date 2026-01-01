---
version: "3.1.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
source: "~/universal-knowledge-ark/specs/04-technical-design.md"
tags:
  - spec
  - universal-knowledge-ark
  - software
  - architecture
---

# Technical Design

**Version:** 3.1.0 (Vault Edition)

## Summary

Implementation architecture for the Universal Knowledge Ark software system.

## System Architecture

- **OS:** Armbian (Debian-based) on Orange Pi 5+
- **Runtime:** Python 3.11 + systemd services
- **LLM:** Llama 3.1 8B W8A8 via RKLLM (NPU) + llama.cpp (CPU fallback)
- **RAG:** FAISS IVFFlat with MiniLM-L6-v2 embeddings
- **STT:** Whisper (accuracy) + VOSK (speed)
- **TTS:** Piper (quality) + espeak (reliability)

## Key Components

| Component | Technology |
|-----------|------------|
| Inference | RKLLM → llama.cpp fallback |
| Embeddings | MiniLM-L6-v2 (384-dim) |
| Vector Store | FAISS IVFFlat (4096 clusters) |
| Content | python-libzim ZIM extraction |
| Power Monitor | INA219 + coulomb counting |

## Vault Edition Additions

- **ArchivalManager** — `ark-seal` / `ark-unseal` commands
- **IntegrityVerifier** — SHA-256 manifest generation/verification
- **DiscoveryBootHandler** — TIME_LOST detection, orientation sequence
- **PowerMonitor** — Na-ion voltage thresholds (18V shutdown, 16V emergency)

## Dependencies

- [[03-Software-Requirements]] — Requirements this design implements
- [[05-Library-Manifest]] — Content format and preprocessing
- [[01-Hardware-Specification]] — Hardware constraints

---

## 1. System Architecture

### 1.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              USER INTERFACE                                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   E-ink     │  │   OLED      │  │  D-pad +    │  │  ReSpeaker  │        │
│  │  Display    │  │  Status     │  │  Buttons    │  │  Mic Array  │        │
│  │ (1872×1404) │  │  (128×64)   │  │  (GPIO)     │  │  (USB)      │        │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘        │
│         │ USB            │ I2C           │ GPIO           │ USB            │
└─────────┼────────────────┼───────────────┼────────────────┼────────────────┘
          │                │               │                │
          ▼                ▼               ▼                ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           APPLICATION LAYER                                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │    Query    │  │   Session   │  │   Audio     │  │   Power     │        │
│  │  Controller │  │   Manager   │  │  Controller │  │  Manager    │        │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘        │
└─────────┼────────────────┼───────────────┼────────────────┼────────────────┘
          │                │               │                │
          ▼                ▼               ▼                ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            SERVICE LAYER                                    │
│  ┌───────────────────────────────────────┐  ┌────────────────────────────┐ │
│  │          INFERENCE ENGINE             │  │      AUDIO ENGINE          │ │
│  │  ┌──────────┐  ┌────────────────────┐ │  │  ┌──────────┐  ┌─────────┐ │ │
│  │  │   RAG    │  │   LLM Inference    │ │  │  │   STT    │  │   TTS   │ │ │
│  │  │ Pipeline │  │  (RKLLM + llama)   │ │  │  │ (Whisper)│  │ (Piper) │ │ │
│  │  └────┬─────┘  └──────┬─────────────┘ │  │  └──────────┘  └─────────┘ │ │
│  └───────┼───────────────┼───────────────┘  └────────────────────────────┘ │
│          │               │                                                  │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │                       SYSTEM SERVICES                                  │ │
│  │  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐              │ │
│  │  │ Power Monitor │  │ Thermal Mgmt  │  │ Storage Mgmt  │              │ │
│  │  │ (I2C + GPIO)  │  │ (sysfs)       │  │ (PAR2 + SD)   │              │ │
│  │  └───────────────┘  └───────────────┘  └───────────────┘              │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────┘
          │               │
          ▼               ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                             DATA LAYER                                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Vector    │  │  Document   │  │    User     │  │   Config    │        │
│  │   Store     │  │   Store     │  │    Data     │  │   Store     │        │
│  │  (FAISS)    │  │  (/data)    │  │  (SQLite)   │  │   (JSON)    │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 1.2 Process Architecture

| Process | Role | Memory Budget | Notes |
|---------|------|---------------|-------|
| `ark-main` | Application controller | 512 MB | Python + orchestration |
| `ark-display` | E-ink + OLED rendering | 128 MB | IT8951 USB + I2C SSD1306 |
| `ark-power` | Power management daemon | 16 MB | Battery monitoring, LVD (I2C polling) |
| `ark-audio` | STT + TTS services | 512 MB | Whisper + Piper models |
| `rkllm-server` | LLM inference (NPU) | 14 GB | 8B W8A8 model + 4096 context (see §3.3) |
| `llama-server` | LLM inference (fallback) | 6 GB | llama.cpp CPU; not concurrent with rkllm |
| `embed-server` | Embedding generation | 1 GB | Sentence transformers |

**Note:** `rkllm-server` and `llama-server` do not run simultaneously—the model dispatcher selects one runtime. Total memory budget assumes RKLLM active (see 03-software-requirements.md §4.2).

### 1.3 IPC Mechanisms

| Communication | Mechanism | Rationale |
|---------------|-----------|-----------|
| Main ↔ Display | Unix socket | Low latency rendering commands |
| Main ↔ Power | D-Bus | System integration, signal handling |
| Main ↔ Audio | Unix socket | Streaming audio data |
| Main ↔ LLM Server | HTTP (localhost:8080) | Standard OpenAI-compatible API |
| Main ↔ Embed Server | HTTP (localhost:8081) | REST API for embeddings |
| GPIO Events | gpiod | Kernel-level input handling |

### 1.4 Technology Stack

| Layer | Technology | Notes |
|-------|------------|-------|
| Hardware | Orange Pi 5+ (RK3588) | 32GB RAM, 6 TOPS NPU |
| OS | Armbian (Debian-based) | Optimized for RK3588 |
| Runtime | Python 3.11 | Application code |
| LLM (primary) | RKLLM | NPU-accelerated inference |
| LLM (fallback) | llama.cpp | CPU inference for unsupported models |
| Embeddings | sentence-transformers | all-MiniLM-L6-v2 |
| Vector Store | FAISS | IVFFlat index, memory-mapped |
| Database | SQLite3 | User data, history |
| STT (primary) | Whisper.cpp | whisper-base.en |
| STT (fallback) | VOSK | Low-power mode |
| TTS (primary) | Piper | Neural TTS |
| TTS (fallback) | espeak-ng | Low-power mode |
| Display (E-ink) | IT8951 USB driver | Via python-IT8951 |
| Display (OLED) | luma.oled | SSD1306 I2C driver |
| Process Manager | systemd | Service supervision |
| Input | python-gpiod | D-pad + button handling |

### 1.5 Consolidated Dependencies

All Python packages required for the Ark application, with versions pinned for reproducibility:

| Package | Version | Purpose | Source Section |
|---------|---------|---------|----------------|
| sentence-transformers | 2.2.2 | Embedding generation | §2.2 |
| faiss-cpu | 1.7.4 | Vector similarity search | §2.3 |
| llama-cpp-python | 0.2.x | CPU LLM fallback | §3.7 |
| lxml | 4.9.x | Fast XML/HTML parsing | §2.4 |
| PyMuPDF | 1.23.x | PDF text extraction | §2.4 |
| ebooklib | 0.18 | EPUB parsing | §2.4 |
| python-libzim | 3.x | ZIM archive access | §2.4 |
| Pillow | 10.x | Image processing | §2.4, §5.1 |
| aiofiles | 23.x | Async file I/O | §7.1 |
| luma.oled | 3.x | SSD1306 OLED driver | §5.2 |
| RPi.GPIO or gpiod | 2.x | GPIO button input | §5.3 |
| smbus2 | 0.4.x | I2C communication (INA219, DS3231) | §6.2 |
| evdev | 1.6.x | Input device handling | §5.3 |
| watchdog | 3.x | File system monitoring | §7.1 |

**Installation:**
```bash
pip install -r requirements.txt --break-system-packages
```

**Note:** RKLLM runtime is installed separately via Rockchip SDK, not pip.

---

## 2. RAG Implementation

### 2.1 RAG Pipeline Overview

```
Query ─┬─► Embedding ─► Vector Search ─► Context Assembly ─► LLM ─► Response
       │                                                      ▲
       └─► (Voice) ─► Whisper STT ─► Text ─────────────────────┘
```

### 2.2 Embedding Model

**Selected: all-MiniLM-L6-v2**

| Parameter | Value |
|-----------|-------|
| Dimensions | 384 |
| Sequence Length | 256 tokens max |
| Model Size | ~80 MB |
| Speed | ~2000 embeddings/sec on RK3588 |

**Rationale:**
- Small enough for fast inference on ARM
- Good semantic quality for retrieval
- Well-tested, widely supported

### 2.3 Vector Store Configuration

**Selected: FAISS IndexIVFFlat (memory-mapped)**

With full Wikipedia (~6.7M articles, ~8M chunks), the index is loaded via memory-mapping to avoid consuming 14GB of RAM.

```python
# Index configuration for full Wikipedia
import faiss

dim = 384  # MiniLM dimensions
n_clusters = 4096  # ~8M vectors / ~2000 per cluster (√n rule)

quantizer = faiss.IndexFlatL2(dim)
index = faiss.IndexIVFFlat(quantizer, dim, n_clusters, faiss.METRIC_L2)

# Load with memory-mapping to reduce RAM footprint
index = faiss.read_index("/data/library/index/vectors.index", faiss.IO_FLAG_MMAP)
index.nprobe = 64  # ~1.5% of clusters searched
```

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| Index type | IVFFlat | Good speed/accuracy for large datasets |
| Clusters | 4096 | Scales to ~8M vectors (√n ≈ 2828, rounded up) |
| nprobe | 64 | 1.5% clusters searched, maintains 97%+ recall |
| Storage | Memory-mapped | 14GB on disk, ~2-4GB active RAM |
| Latency | ~150ms | +50ms vs in-memory (NVMe random read) |

### 2.4 Chunking Strategy

| Content Type | Chunk Size | Overlap | Rationale |
|--------------|------------|---------|-----------|
| Prose (articles, books) | 512 tokens | 64 tokens | Captures full paragraphs |
| Technical (iFixit, manuals) | 256 tokens | 32 tokens | Keeps procedures intact |
| Reference (Wikipedia, tables) | 384 tokens | 48 tokens | Balance structure/context |
| Q&A (WikiHow, FAQ) | Full section | 0 | Natural question-answer units |

### 2.5 Retrieval Configuration

```python
@dataclass
class RetrievalConfig:
    top_k: int = 15              # Initial retrieval
    rerank_top_k: int = 5        # After reranking
    min_similarity: float = 0.25 # Cosine similarity threshold
    max_context_tokens: int = 4096  # Fit in 7B context window
    domain_boost: float = 1.2    # Boost same-domain results
```

### 2.6 Context Assembly

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ SYSTEM PROMPT                                                               │
│ You are a knowledgeable assistant providing practical survival and         │
│ reference information. Base your answers on the provided context.          │
│ Cite sources when possible. Keep answers clear and actionable.             │
├─────────────────────────────────────────────────────────────────────────────┤
│ CONTEXT                                                                     │
│ [Source: iFixit - Small Engine Repair, Section 3.2]                        │
│ To diagnose a flooded carburetor: remove spark plug, check for wet...      │
│                                                                             │
│ [Source: Wikipedia - Carburetor]                                           │
│ A carburetor mixes air and fuel for internal combustion engines...         │
│                                                                             │
│ [Source: Survival Medicine Handbook, Ch. 12]                               │
│ Engine exhaust contains carbon monoxide. Never run engines indoors...      │
├─────────────────────────────────────────────────────────────────────────────┤
│ USER QUERY                                                                  │
│ How do I fix a flooded engine?                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. LLM Integration

### 3.1 Model Selection

**Primary: Llama 3.1 8B Instruct (W8A8 quantization for RKLLM)**

| Parameter | Value |
|-----------|-------|
| Parameters | 8 billion |
| Quantization | W8A8 (RKLLM native, 8-bit weights + 8-bit activations) |
| Model Size | ~8 GB |
| Context Window | 8192 tokens (configurable to 4096 for faster inference) |
| Inference Speed (NPU) | ~3-6 tokens/sec on RK3588 NPU |
| Inference Speed (CPU) | ~2-3 tokens/sec fallback via llama.cpp |

**Rationale:**
- Pre-converted models available on HuggingFace (no conversion tooling required)
- Significant quality improvement over smaller models
- Fits comfortably in 32GB RAM with all services (~12-16GB for model + runtime)
- W8A8 is the only quantization supported by RK3588 NPU
- Llama 3.1 8B has strong instruction-following for practical Q&A
- 3-6 tok/s acceptable for RAG use case (query-then-read, not real-time chat)

### 3.2 Hybrid Inference Architecture

```
                    ┌─────────────────────────────────────┐
                    │         MODEL DISPATCHER            │
                    │  (selects runtime based on model)   │
                    └─────────────┬───────────────────────┘
                                  │
              ┌───────────────────┼───────────────────┐
              │                   │                   │
              ▼                   ▼                   ▼
    ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
    │    RKLLM        │ │   llama.cpp     │ │   Fallback      │
    │  (NPU accel)    │ │  (CPU only)     │ │  (smaller model)│
    │                 │ │                 │ │                 │
    │  - 8B W8A8      │ │  - GGUF format  │ │  - 3B model     │
    │  - RK3588 NPU   │ │  - Any model    │ │  - Low power    │
    │  - 3-6 tok/s    │ │  - 2-3 tok/s    │ │  - ~10 tok/s    │
    └─────────────────┘ └─────────────────┘ └─────────────────┘
```

**Runtime Selection Logic:**
1. If model supported by RKLLM and NPU available → RKLLM
2. If RKLLM fails or unsupported model → llama.cpp CPU
3. If low-power mode or thermal throttle → smaller fallback model

### 3.3 RKLLM Server Configuration

```python
# RKLLM configuration for RK3588
rkllm_config = {
    "model_path": "/data/models/Meta-Llama-3.1-8B-Instruct_W8A8_RK3588.rkllm",
    "target_platform": "rk3588",
    "num_npu_cores": 3,  # RK3588 has 3 NPU cores
    "context_length": 4096,  # Can increase to 8192 if RAM permits
    "max_new_tokens": 1024,
    "temperature": 0.3,
    "top_p": 0.9,
    "top_k": 40,
    "repeat_penalty": 1.1,
}
```

**Runtime Requirements:**
- RKLLM Runtime: v1.1.2 or later
- Driver: rknpu driver v0.9.6+
- Memory: ~12-16GB for 8B model with 4096 context

### 3.4 llama.cpp Fallback Configuration

```yaml
# llama-cpp-python server config (fallback)
model: /data/models/llama-3.1-7b-instruct-q4_k_m.gguf
n_ctx: 4096
n_gpu_layers: 0  # CPU only; Mali G610 exists but llama.cpp has no ROCm/OpenCL backend for it
n_threads: 8     # Use all A76 + A55 cores
n_batch: 512
```

### 3.5 Generation Parameters

```python
generation_config = {
    "max_tokens": 1024,
    "temperature": 0.3,      # Low for factual responses
    "top_p": 0.9,
    "top_k": 40,
    "repeat_penalty": 1.1,
    "stop": ["</response>", "\nUser:", "\nQuestion:", "---"]
}
```

### 3.6 Prompt Templates

**Standard Query:**
```
<|begin_of_text|><|start_header_id|>system<|end_header_id|>

You are a knowledgeable assistant providing practical information for survival,
repair, medicine, agriculture, and general reference. Base your answers on the
provided context. If information is not in the context, say so clearly. Keep
answers concise, actionable, and cite sources when possible.

CONTEXT:
{context}
<|eot_id|><|start_header_id|>user<|end_header_id|}

{query}<|eot_id|><|start_header_id|>assistant<|end_header_id|>
```

**Follow-up Query:**
```
<|begin_of_text|><|start_header_id|>system<|end_header_id|>

Continue the conversation based on the context and previous exchange.

{context}

Previous exchange:
{previous_exchange}
<|eot_id|><|start_header_id|>user<|end_header_id|>

{query}<|eot_id|><|start_header_id|>assistant<|end_header_id|>
```

### 3.7 Model Acquisition

**Pre-converted models are available on HuggingFace:**

| Model | Repository | RKLLM Version | Size |
|-------|------------|---------------|------|
| Llama 3.1 8B (primary) | `c01zaut/Llama-3.1-8B-Instruct-rk3588-1.1.2` | 1.1.2+ | ~8 GB |
| Llama 3.2 3B (fallback) | `c01zaut/Llama-3.2-3B-Instruct-rk3588-1.1.4` | 1.1.4+ | ~3 GB |
| Dolphin 2.9.4 8B (alt) | `c01zaut/dolphin-2.9.4-llama3.1-8b-rk3588-1.1.2` | 1.1.2+ | ~8 GB |

**Download procedure:**
```bash
# Requires git-lfs
git lfs install

# Clone primary model
git clone https://huggingface.co/c01zaut/Llama-3.1-8B-Instruct-rk3588-1.1.2

# Copy to data partition
cp Llama-3.1-8B-Instruct-rk3588-1.1.2/*.rkllm /data/models/
```

**RKLLM Runtime Installation:**
```bash
# Download RKLLM SDK (requires fetch code: rkllm)
# https://github.com/airockchip/rknn-llm

# Install runtime library
cp rkllm-runtime/Linux/librkllm_api/aarch64/librkllmrt.so /usr/local/lib/
ldconfig

# Verify installation
export RKLLM_LOG_LEVEL=1
./llm_demo /data/models/Meta-Llama-3.1-8B-Instruct_W8A8_RK3588.rkllm 1024 4096
```

**Why pre-converted models:**
- Model conversion requires x86 Linux workstation with 32GB+ RAM
- RKLLM-Toolkit does not run on ARM (cannot convert on the device)
- Pre-converted models eliminate toolchain complexity
- Community maintains updated conversions for popular models

---

## 4. Audio Engine

### 4.1 Audio Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            AUDIO ENGINE                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                        SPEECH-TO-TEXT (STT)                         │   │
│  │                                                                     │   │
│  │   ReSpeaker ──► ALSA ──► VAD ──┬──► Whisper.cpp ──► Text           │   │
│  │   Mic Array     Capture   Det  │    (primary)                       │   │
│  │                                │                                     │   │
│  │                                └──► VOSK ──────────► Text           │   │
│  │                                     (low-power)                      │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                        TEXT-TO-SPEECH (TTS)                         │   │
│  │                                                                     │   │
│  │   Text ──┬──► Piper ──────────► Audio ──► ALSA ──► TPA2016D2       │   │
│  │          │    (neural)          PCM       Playback  Amplifier       │   │
│  │          │                                              │           │   │
│  │          └──► espeak-ng ──────► Audio ──────────────────┘           │   │
│  │               (low-power)       PCM                     Speaker     │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 4.2 STT Configuration

**Primary: Whisper.cpp (whisper-base)**

| Parameter | Value |
|-----------|-------|
| Model | whisper-base.en |
| Size | ~150 MB |
| Languages | English only (optimized for size/speed) |
| Accuracy | WER ~5% on clean speech |
| Speed | ~2× realtime on RK3588 CPU (ARM NEON optimized) |

**Fallback: VOSK (small model)**

| Parameter | Value |
|-----------|-------|
| Model | vosk-model-small-en-us |
| Size | ~50 MB |
| Accuracy | WER ~8% on clean speech |
| Speed | ~10× realtime (very fast) |
| Use case | Low-power mode, thermal throttling |

```python
class STTEngine:
    def __init__(self, mode: str = "normal"):
        if mode == "low_power":
            self.engine = VOSKEngine(model_path="/data/models/vosk-small")
        else:
            self.engine = WhisperEngine(model_path="/data/models/whisper-base.en")
    
    def transcribe(self, audio: np.ndarray, sample_rate: int = 16000) -> str:
        # ReSpeaker outputs 16kHz mono after beamforming
        return self.engine.transcribe(audio, sample_rate)
```

### 4.3 TTS Configuration

**Primary: Piper**

| Parameter | Value |
|-----------|-------|
| Model | en_US-lessac-medium |
| Size | ~100 MB |
| Quality | Natural, neural voice |
| Speed | ~5× realtime on RK3588 |

**Fallback: espeak-ng**

| Parameter | Value |
|-----------|-------|
| Voice | en-us |
| Size | ~5 MB |
| Quality | Robotic but clear |
| Speed | ~50× realtime |
| Use case | Low-power mode |

```python
class TTSEngine:
    def __init__(self, mode: str = "normal"):
        if mode == "low_power":
            self.engine = EspeakEngine()
        else:
            self.engine = PiperEngine(model_path="/data/models/piper-lessac")
    
    def speak(self, text: str) -> None:
        audio = self.engine.synthesize(text)
        self.audio_output.play(audio)
```

### 4.4 Push-to-Talk Implementation

**Audio Feedback Files:**

| Event | File | Description |
|-------|------|-------------|
| PTT start | `/usr/share/ark/sounds/ptt_start.wav` | 440Hz sine, 100ms, fade-in |
| PTT stop | `/usr/share/ark/sounds/ptt_stop.wav` | 880Hz sine, 100ms, fade-out |
| Error | `/usr/share/ark/sounds/error.wav` | 220Hz square, 200ms |
| Confirm | `/usr/share/ark/sounds/confirm.wav` | 660Hz sine, 50ms |
| Low battery | `/usr/share/ark/sounds/low_battery.wav` | 330Hz, 3× 100ms pulses |

**Generation (at build time):**
```bash
# Generate beep files with sox
sox -n ptt_start.wav synth 0.1 sine 440 fade t 0.02 0.1 0.02
sox -n ptt_stop.wav synth 0.1 sine 880 fade t 0.02 0.1 0.02
sox -n error.wav synth 0.2 square 220
sox -n confirm.wav synth 0.05 sine 660
sox -n low_battery.wav synth 0.1 sine 330 pad 0 0.15 repeat 2
```

```python
class PTTController:
    PTT_HOLD_THRESHOLD_MS = 1000  # 1 second hold to activate
    MAX_RECORDING_SECONDS = 30
    
    def __init__(self, gpio_pin: int, audio_engine: AudioEngine):
        self.gpio = gpiod.Chip("/dev/gpiochip0")
        self.line = self.gpio.get_line(gpio_pin)
        self.audio = audio_engine
        self.recording = False
    
    async def handle_button(self, event: ButtonEvent):
        if event.type == "hold" and event.duration_ms >= self.PTT_HOLD_THRESHOLD_MS:
            # Start recording
            self.recording = True
            await self.audio.play_beep("start")  # Audio feedback
            self.oled.set_state("MIC")
            audio_data = await self.audio.record(max_seconds=self.MAX_RECORDING_SECONDS)
            
        elif event.type == "release" and self.recording:
            # Stop recording, transcribe
            self.recording = False
            await self.audio.play_beep("stop")
            self.oled.set_state("BUSY")
            
            text = await self.stt.transcribe(audio_data)
            return text
```

---

## 5. UI Framework

### 5.1 Display Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           E-INK DISPLAY (IT8951)                            │
│                              1872 × 1404 @ 16-gray                          │
└───────────────────────────────────┬─────────────────────────────────────────┘
                                    │ USB
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            DISPLAY DAEMON                                   │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐          │
│  │   Frame Buffer   │  │   Diff Engine    │  │  Partial Update  │          │
│  │     Manager      │  │  (dirty regions) │  │     Manager      │          │
│  └──────────────────┘  └──────────────────┘  └──────────────────┘          │
└───────────────────────────────────┬─────────────────────────────────────────┘
                                    │ Unix Socket
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                             UI RENDERER                                     │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐          │
│  │   Layout Engine  │  │  Text Renderer   │  │  Widget Library  │          │
│  │   (1872×1404)    │  │  (FreeType)      │  │  (buttons, etc)  │          │
│  └──────────────────┘  └──────────────────┘  └──────────────────┘          │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                           OLED STATUS (SSD1306)                             │
│                              128 × 64 mono                                  │
└───────────────────────────────────┬─────────────────────────────────────────┘
                                    │ I2C (0x3C)
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           OLED RENDERER                                     │
│  ┌──────────────────────────────────────────────────────────────┐          │
│  │  Status Line: "12.8V  92%  ◉ READY"                         │          │
│  │  Progress Bar: ████████████░░░░ (optional)                   │          │
│  └──────────────────────────────────────────────────────────────┘          │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 5.2 E-ink Update Modes

| Mode | IT8951 Mode | Use Case | Refresh Time | Ghosting |
|------|-------------|----------|--------------|----------|
| FULL | GC16 | Full screen clear, images | ~450 ms | None |
| PARTIAL | DU | Text scroll, menu nav | ~260 ms | Low |
| FAST | A2 | Cursor, selection highlight | ~120 ms | Medium |

### 5.3 Layout System

```python
@dataclass
class LayoutSpec:
    SCREEN_WIDTH = 1872
    SCREEN_HEIGHT = 1404
    
    STATUS_BAR_HEIGHT = 48
    ACTION_BAR_HEIGHT = 56
    CONTENT_MARGIN = 32
    
    CONTENT_AREA = Rect(
        x=CONTENT_MARGIN,
        y=STATUS_BAR_HEIGHT + CONTENT_MARGIN,
        width=SCREEN_WIDTH - (2 * CONTENT_MARGIN),
        height=SCREEN_HEIGHT - STATUS_BAR_HEIGHT - ACTION_BAR_HEIGHT - (2 * CONTENT_MARGIN)
    )
    # Content area: 1808 × 1268 pixels
    
    # At 24pt body text (~32px), ~40 lines visible
    # At 36pt query text (~48px), ~26 lines visible
```

### 5.4 Input Handling

```python
import gpiod
from dataclasses import dataclass
from enum import Enum, auto

class InputEvent(Enum):
    DPAD_UP = auto()
    DPAD_DOWN = auto()
    DPAD_LEFT = auto()
    DPAD_RIGHT = auto()
    DPAD_CENTER = auto()
    DPAD_CENTER_HOLD = auto()  # PTT trigger
    ESC_PRESS = auto()
    HOME_PRESS = auto()
    HOME_HOLD = auto()  # TTS trigger
    POWER_SHORT = auto()
    POWER_LONG = auto()

@dataclass
class GPIOPin:
    """GPIO pin specification for Orange Pi 5+ (RK3588).
    
    Pin naming: GPIO{bank}_{group}{pin} where:
    - bank: 0-4 (maps to /dev/gpiochip{bank})
    - group: A=0, B=1, C=2, D=3
    - pin: 0-7
    
    gpiod line number = (group × 8) + pin
    Example: GPIO1_C6 → chip=1, line=(2×8)+6=22
    """
    chip: int      # gpiochip number (0-4)
    line: int      # line number within chip
    name: str      # Human-readable name (e.g., "GPIO1_C6")
    physical: int  # 40-pin header physical pin number

@dataclass
class GPIOMapping:
    """GPIO assignments for input buttons on Orange Pi 5+ 40-pin header.
    
    Selected pins avoid I2C5 (pins 3,5), UART0 (pins 8,10), and power rails.
    All pins configured as inputs with internal pull-up (active-low buttons).
    External 10kΩ pull-down to GND recommended if internal pulls unreliable.
    
    Reference: Orange Pi 5 V1.2 pinout (RK3588 GPIO banks 0-4)
    """
    # D-pad navigation (5 buttons in cross pattern)
    DPAD_UP:     GPIOPin = GPIOPin(chip=1, line=22, name="GPIO1_C6", physical=7)
    DPAD_DOWN:   GPIOPin = GPIOPin(chip=4, line=10, name="GPIO4_B2", physical=11)
    DPAD_LEFT:   GPIOPin = GPIOPin(chip=4, line=11, name="GPIO4_B3", physical=13)
    DPAD_RIGHT:  GPIOPin = GPIOPin(chip=0, line=28, name="GPIO0_D4", physical=15)
    DPAD_CENTER: GPIOPin = GPIOPin(chip=1, line=27, name="GPIO1_D3", physical=16)  # PTT on hold
    
    # Function buttons
    ESC:  GPIOPin = GPIOPin(chip=1, line=26, name="GPIO1_D2", physical=18)
    HOME: GPIOPin = GPIOPin(chip=2, line=28, name="GPIO2_D4", physical=22)  # TTS on hold
    
    # Note: POWER uses hardware button on OPi 5+ (connected to PMIC, not GPIO)
    # Software monitors PMIC events via /dev/input for power button presses

class InputHandler:
    """Handle GPIO button inputs with debouncing and hold detection.
    
    Uses python-gpiod v2 API with edge detection for responsive input.
    Multiple gpiochips required as OPi 5+ pins span banks 0-4.
    """
    DEBOUNCE_MS = 50
    HOLD_THRESHOLD_MS = 1000  # 1 second for hold events (PTT, TTS)
    LONG_PRESS_MS = 3000      # 3 seconds for power long press
    
    def __init__(self, gpio_map: GPIOMapping = GPIOMapping()):
        self.gpio_map = gpio_map
        self.callbacks = {}
        self.chips = {}  # Cache open gpiochip handles
        self.lines = {}  # Button name → gpiod line request
        
        # Open required gpiochips and request lines
        self._setup_gpio()
    
    def _setup_gpio(self):
        """Initialize GPIO lines for all buttons."""
        import gpiod
        from gpiod.line import Direction, Bias, Edge
        
        buttons = [
            ("DPAD_UP", self.gpio_map.DPAD_UP),
            ("DPAD_DOWN", self.gpio_map.DPAD_DOWN),
            ("DPAD_LEFT", self.gpio_map.DPAD_LEFT),
            ("DPAD_RIGHT", self.gpio_map.DPAD_RIGHT),
            ("DPAD_CENTER", self.gpio_map.DPAD_CENTER),
            ("ESC", self.gpio_map.ESC),
            ("HOME", self.gpio_map.HOME),
        ]
        
        for name, pin in buttons:
            chip_path = f"/dev/gpiochip{pin.chip}"
            if chip_path not in self.chips:
                self.chips[chip_path] = gpiod.Chip(chip_path)
            
            chip = self.chips[chip_path]
            # Request line with pull-up, falling edge detection (active-low)
            self.lines[name] = chip.request_lines(
                consumer="ark-input",
                config={
                    pin.line: gpiod.LineSettings(
                        direction=Direction.INPUT,
                        bias=Bias.PULL_UP,
                        edge_detection=Edge.BOTH,  # Detect press and release
                        debounce_period=timedelta(milliseconds=self.DEBOUNCE_MS)
                    )
                }
            )
        
    def register_callback(self, event: InputEvent, callback: Callable):
        self.callbacks[event] = callback
    
    async def poll(self):
        """Poll for GPIO events with edge detection.
        
        Uses select() on line file descriptors for efficient waiting.
        Tracks press timestamps for hold detection.
        """
        # Implementation: use asyncio with gpiod edge events
        # Track button press times, fire hold events after threshold
        pass
    
    def cleanup(self):
        """Release GPIO resources."""
        for line in self.lines.values():
            line.release()
        for chip in self.chips.values():
            chip.close()
```

### 5.5 State Management

```python
from dataclasses import dataclass, asdict
from pathlib import Path
import json

@dataclass
class UIState:
    current_screen: str  # "home", "query", "menu", "browse", etc.
    query_text: str
    response_text: str
    response_sources: list[str]
    scroll_position: int
    menu_selection: int
    battery_voltage: float
    battery_percent: int
    charging: bool
    system_state: str  # "BOOT", "READY", "BUSY", "MIC", "SPK", "SLEEP"
    audio_mode: str    # "off", "on_demand", "auto"
    
    def save(self, path: Path = Path("/data/user/session.json")):
        with open(path, "w") as f:
            json.dump(asdict(self), f)
    
    @classmethod
    def load(cls, path: Path = Path("/data/user/session.json")) -> "UIState":
        try:
            with open(path) as f:
                return cls(**json.load(f))
        except (FileNotFoundError, json.JSONDecodeError):
            return cls.default()
    
    @classmethod
    def default(cls) -> "UIState":
        return cls(
            current_screen="home",
            query_text="",
            response_text="",
            response_sources=[],
            scroll_position=0,
            menu_selection=0,
            battery_voltage=0.0,
            battery_percent=0,
            charging=False,
            system_state="BOOT",
            audio_mode="off"
        )
```

---

## 6. Power Manager

### 6.1 State Machine Implementation

```python
from enum import Enum, auto
from transitions import Machine

class PowerState(Enum):
    OFF = auto()
    BOOTING = auto()
    ACTIVE = auto()
    STANDBY = auto()

class PowerManager:
    states = [s.name for s in PowerState]
    
    transitions = [
        {"trigger": "boot", "source": "OFF", "dest": "BOOTING"},
        {"trigger": "boot_complete", "source": "BOOTING", "dest": "ACTIVE"},
        {"trigger": "idle_timeout", "source": "ACTIVE", "dest": "STANDBY",
         "before": "enter_standby"},
        {"trigger": "wake", "source": "STANDBY", "dest": "ACTIVE",
         "before": "exit_standby"},
        {"trigger": "shutdown", "source": "*", "dest": "OFF",
         "before": "graceful_shutdown"},
        {"trigger": "critical_battery", "source": "*", "dest": "OFF",
         "before": "emergency_shutdown"},
    ]
    
    # Timing configuration
    STANDBY_TIMEOUT_SECONDS = 300   # 5 minutes (configurable 1-30 min)
    
    # Voltage thresholds (Na-ion 8S, 24V nominal)
    SOFTWARE_LVD_VOLTAGE = 18.0     # Graceful shutdown
    HARDWARE_LVD_VOLTAGE = 16.0     # Load disconnect opens (prevents boot-loop)
    WARNING_VOLTAGE = 19.0          # Low battery warning
    
    def __init__(self):
        self.machine = Machine(
            model=self,
            states=self.states,
            transitions=self.transitions,
            initial="OFF"
        )
```

### 6.2 Power Monitoring

```python
class PowerMonitor:
    """Monitor battery via INA219 I2C power sensor (Adafruit #904).
    
    Hardware: INA219 placed inline after load disconnect, before KSD9700/DC-DC.
    I2C address: 0x40 (default, no conflict with DS3231 0x68 or OLED 0x3C).
    Measures: Bus voltage (0-26V), current (±3.2A via 0.1Ω shunt), power.
    
    IMPORTANT: Na-ion 8S pack reaches 32V at full charge, exceeding INA219's
    26V rating. INA219 is placed AFTER load disconnect relay, which only closes
    above 20V. Typical operating range seen by INA219: 20-32V. The 36V absolute
    max rating provides margin. Alternative: use 2:1 voltage divider on Vin+.
    
    SOC Estimation Strategy:
    - Primary: Coulomb counting (current integration over time)
    - Calibration: Reset to 100% at full charge, 0% at empty
    - Fallback: Voltage-based estimation when coulomb data unavailable
    
    Accuracy: ±5% SOC after one full charge/discharge calibration cycle.
    """
    
    VOLTAGE_SAMPLES = 10
    SAMPLE_INTERVAL_MS = 100
    
    # Software LVD thresholds for Na-ion 8S (see Hardware Spec §4.7)
    SOFTWARE_LVD_VOLTAGE = 18.0      # Graceful shutdown trigger
    WARNING_VOLTAGE = 19.0           # Low battery warning
    HARDWARE_LVD_VOLTAGE = 16.0      # Load disconnect opens (prevents boot-loop)
    
    # Battery capacity (Na-ion 8S4P: 40Ah)
    BATTERY_CAPACITY_MAH = 40_000    # 40Ah = 40,000 mAh
    
    # Charging detection thresholds
    CHARGE_CURRENT_THRESHOLD_MA = 50.0   # >50mA = charging
    FULL_CHARGE_CURRENT_MA = 100.0       # <100mA at 32V = full
    FULL_CHARGE_VOLTAGE = 32.0           # Na-ion 8S full charge (4.0V/cell)
    FULL_CHARGE_HOLD_SECONDS = 300       # 5 min at threshold = confirmed full
    
    # Na-ion 8S (24V nominal) voltage-to-SOC curve (fallback only)
    # Used when coulomb counter not yet calibrated
    # Na-ion has relatively linear discharge curve (better than LiFePO4)
    SOC_CURVE = [
        (32.0, 100), (30.0, 90), (28.0, 75), (26.0, 60),
        (24.0, 45), (22.0, 30), (20.0, 20), (18.0, 10),
        (16.0, 5), (12.0, 0)
    ]
    
    # Persistence file for coulomb state across reboots
    STATE_FILE = "/var/lib/ark/power_state.json"
    
    def __init__(self):
        import board
        import time
        from pathlib import Path
        from adafruit_ina219 import INA219
        
        i2c = board.I2C()
        self.ina219 = INA219(i2c, addr=0x40)
        self.voltage_history = []
        
        # Coulomb counting state
        self._last_sample_time = time.monotonic()
        self._full_charge_start_time = None
        
        # Load persisted state or initialize
        self._load_state()
    
    def _load_state(self):
        """Load coulomb counter state from disk."""
        import json
        from pathlib import Path
        
        try:
            state = json.loads(Path(self.STATE_FILE).read_text())
            self.coulomb_count_mah = state.get("coulomb_mah", 0.0)
            self.calibrated = state.get("calibrated", False)
            self.last_full_charge_time = state.get("last_full_charge", None)
        except (FileNotFoundError, json.JSONDecodeError):
            # First boot or corrupted state
            self.coulomb_count_mah = 0.0
            self.calibrated = False
            self.last_full_charge_time = None
    
    def _save_state(self):
        """Persist coulomb counter state to disk."""
        import json
        from pathlib import Path
        
        Path(self.STATE_FILE).parent.mkdir(parents=True, exist_ok=True)
        state = {
            "coulomb_mah": self.coulomb_count_mah,
            "calibrated": self.calibrated,
            "last_full_charge": self.last_full_charge_time
        }
        Path(self.STATE_FILE).write_text(json.dumps(state))
    
    def update(self):
        """Call periodically (~1Hz) to update coulomb counter.
        
        Integrates current over time to track mAh consumed/charged.
        Detects full charge state and recalibrates counter.
        """
        import time
        
        now = time.monotonic()
        dt_hours = (now - self._last_sample_time) / 3600.0
        self._last_sample_time = now
        
        current_ma = self.get_current_ma()
        voltage = self.get_battery_voltage()
        
        # Integrate current: mAh = mA * hours
        # Positive current = charging (decreases consumed count)
        # Negative current = discharging (increases consumed count)
        self.coulomb_count_mah -= current_ma * dt_hours
        
        # Clamp to valid range
        self.coulomb_count_mah = max(0, min(self.BATTERY_CAPACITY_MAH, 
                                            self.coulomb_count_mah))
        
        # Detect full charge: voltage at threshold AND current dropped
        if voltage >= self.FULL_CHARGE_VOLTAGE and current_ma < self.FULL_CHARGE_CURRENT_MA:
            if self._full_charge_start_time is None:
                self._full_charge_start_time = now
            elif (now - self._full_charge_start_time) >= self.FULL_CHARGE_HOLD_SECONDS:
                # Confirmed full charge - reset coulomb counter
                self._calibrate_full()
        else:
            self._full_charge_start_time = None
        
        # Detect empty: voltage at LVD threshold
        if voltage <= self.HARDWARE_LVD_VOLTAGE:
            self._calibrate_empty()
        
        # Persist state periodically (every update, ~1Hz)
        # In production, debounce to every 60s to reduce writes
        self._save_state()
    
    def _calibrate_full(self):
        """Reset coulomb counter at confirmed full charge."""
        import time
        self.coulomb_count_mah = 0.0  # 0 mAh consumed = 100% SOC
        self.calibrated = True
        self.last_full_charge_time = time.time()
        self._full_charge_start_time = None
    
    def _calibrate_empty(self):
        """Reset coulomb counter at confirmed empty."""
        self.coulomb_count_mah = self.BATTERY_CAPACITY_MAH  # All capacity consumed
        self.calibrated = True
    
    def get_battery_voltage(self) -> float:
        """Read bus voltage from INA219 with rolling average."""
        voltage = self.ina219.bus_voltage  # Returns volts
        
        # Rolling average for stability (filters noise)
        self.voltage_history.append(voltage)
        if len(self.voltage_history) > self.VOLTAGE_SAMPLES:
            self.voltage_history.pop(0)
        
        return sum(self.voltage_history) / len(self.voltage_history)
    
    def get_current_ma(self) -> float:
        """Read current in mA. Positive = charging, negative = discharging."""
        return self.ina219.current  # Returns mA
    
    def get_power_mw(self) -> float:
        """Read power consumption in mW."""
        return self.ina219.power  # Returns mW
    
    def get_battery_percent(self) -> int:
        """Get SOC percentage using coulomb counting (primary) or voltage (fallback).
        
        Returns: 0-100 percentage
        
        Accuracy:
        - Calibrated coulomb counting: ±5%
        - Uncalibrated/fallback voltage: ±15-20% (LiFePO4 flat curve)
        """
        if self.calibrated:
            # Primary: coulomb counting
            remaining_mah = self.BATTERY_CAPACITY_MAH - self.coulomb_count_mah
            percent = (remaining_mah / self.BATTERY_CAPACITY_MAH) * 100
            return int(max(0, min(100, percent)))
        else:
            # Fallback: voltage-based estimation
            return self._voltage_to_soc(self.get_battery_voltage())
    
    def _voltage_to_soc(self, voltage: float) -> int:
        """Fallback voltage-based SOC estimation.
        
        Note: Na-ion has a more linear discharge curve than LiFePO4,
        so voltage-based estimation is reasonably accurate (±10-15%).
        Still prefer coulomb counting when calibrated.
        """
        for v_threshold, soc in self.SOC_CURVE:
            if voltage >= v_threshold:
                return soc
        return 0
    
    def is_charging(self) -> bool:
        """Check if solar/scavenger charging is active.
        
        Uses INA219 current measurement. Positive current >50mA = charging.
        """
        return self.get_current_ma() > self.CHARGE_CURRENT_THRESHOLD_MA
    
    def is_discharging(self) -> bool:
        """Check if system is drawing from battery."""
        return self.get_current_ma() < -self.CHARGE_CURRENT_THRESHOLD_MA
    
    def get_charge_current_ma(self) -> float:
        """Get charging current (positive) or 0 if not charging."""
        current = self.get_current_ma()
        return current if current > 0 else 0.0
    
    def get_time_remaining_hours(self) -> float | None:
        """Estimate hours remaining based on current draw.
        
        Returns None if charging or current too low to estimate.
        """
        current_ma = self.get_current_ma()
        if current_ma >= -100:  # Charging or negligible draw
            return None
        
        remaining_mah = self.BATTERY_CAPACITY_MAH - self.coulomb_count_mah
        return remaining_mah / abs(current_ma)
    
    def get_time_to_full_hours(self) -> float | None:
        """Estimate hours to full charge based on charge current.
        
        Returns None if not charging.
        """
        current_ma = self.get_current_ma()
        if current_ma <= 100:  # Not charging significantly
            return None
        
        return self.coulomb_count_mah / current_ma
    
    def check_voltage_thresholds(self) -> str:
        """Check voltage against warning/shutdown thresholds.
        
        Returns: 'ok', 'warning', 'critical', or 'shutdown'
        """
        voltage = self.get_battery_voltage()
        
        if voltage <= self.SOFTWARE_LVD_VOLTAGE:
            return 'shutdown'  # Trigger graceful shutdown
        elif voltage <= self.WARNING_VOLTAGE:
            return 'warning'   # Show low battery warning
        else:
            return 'ok'
    
    def get_oled_status_string(self) -> str:
        """Format status for OLED display.
        
        Format: '24.5V 65% ⚡' or '22.0V 42%' or '24.5V 65%* (uncal)'
        """
        voltage = self.get_battery_voltage()
        percent = self.get_battery_percent()
        charging = '⚡' if self.is_charging() else ''
        cal_indicator = '' if self.calibrated else '*'
        return f"{voltage:.1f}V {percent}%{cal_indicator}{charging}"
```

### 6.3 Standby Implementation

```python
async def enter_standby(self):
    """Enter low-power standby mode (~2.5W).
    
    Design note: RK3588 suspend-to-RAM exists but is unreliable on mainline
    Linux (PCIe/NVMe resume failures). This implementation uses service
    shutdown + CPU frequency reduction instead, prioritizing reliability
    over theoretical power savings.
    
    Note: CPU sysfs paths verified on Armbian 24.x (kernel 6.1+). If paths
    differ on other distributions, check /sys/devices/system/cpu/cpuX/ layout.
    """
    logger.info("Entering standby")
    
    # Save state before reducing services
    self.ui_state.save()
    subprocess.run(["sync"])
    
    # Stop inference servers (major power consumers)
    subprocess.run(["systemctl", "stop", "rkllm-server"], check=False)
    subprocess.run(["systemctl", "stop", "llama-server"], check=False)
    subprocess.run(["systemctl", "stop", "embed-server"], check=False)
    subprocess.run(["systemctl", "stop", "ark-audio"], check=False)
    
    # Set CPU to minimum frequency (powersave governor)
    for cpu in range(8):
        path = f"/sys/devices/system/cpu/cpu{cpu}/cpufreq/scaling_governor"
        with open(path, "w") as f:
            f.write("powersave")
    
    # Disable unused peripherals
    # Note: HDMI, WiFi, BT already disabled at boot for this use case
    
    # E-ink display unchanged (zero power to maintain image)
    # OLED shows SLEEP status with reduced brightness
    self.oled.set_state("SLEEP", dim=True)
    
    # GPIO polling continues for button wake detection

async def exit_standby(self):
    """Resume from standby (~2-3s wake time)."""
    logger.info("Exiting standby")
    
    # Restore CPU governor
    for cpu in range(8):
        path = f"/sys/devices/system/cpu/cpu{cpu}/cpufreq/scaling_governor"
        with open(path, "w") as f:
            f.write("schedutil")
    
    # Restart services in dependency order
    subprocess.run(["systemctl", "start", "embed-server"], check=False)
    subprocess.run(["systemctl", "start", "rkllm-server"], check=False)
    # Audio started on-demand when needed
    
    self.oled.set_state("READY")
    
    # Restore last UI state
    self.ui_state = UIState.load()
```

### 6.4 Graceful Shutdown

```python
async def graceful_shutdown(self, reason: str = "user_request"):
    """Graceful shutdown with data persistence."""
    logger.info(f"Graceful shutdown initiated: {reason}")
    
    # 1. Update display
    self.oled.set_state("SHUTDOWN")
    self.eink.show_message("Saving and shutting down...")
    
    # 2. Save application state
    self.ui_state.save()
    self.session_manager.save()
    
    # 3. Checkpoint to SD card
    await self.checkpoint_to_sd()
    
    # 4. Sync filesystems
    subprocess.run(["sync"])
    
    # 5. Stop services in order
    for service in ["ark-audio", "ark-main", "rkllm-server", 
                    "llama-server", "embed-server", "ark-display"]:
        subprocess.run(["systemctl", "stop", service], check=False)
    
    # 6. Unmount data partition
    subprocess.run(["umount", "/data"], check=False)
    
    # 7. Power off
    subprocess.run(["poweroff"])
```

---

## 7. Data Flow

### 7.1 Query Processing Pipeline

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                              QUERY FLOW                                      │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  1. User Input                                                               │
│     ├── Keyboard ─────────────────────────────────────► Query string        │
│     │                                                                        │
│     └── Voice (PTT) ──► ReSpeaker ──► Whisper ──► Text ──► Query string     │
│                         (beamform)    (STT)                                  │
│                                                                              │
│  2. Preprocessing                                            ~50ms           │
│     └── Normalize text                                                       │
│         └── Expand common abbreviations                                      │
│             └── Detect query intent (optional)                               │
│                                                                              │
│  3. Embedding                                                ~50ms           │
│     └── Send to embed-server                                                 │
│         └── Generate 384-dim vector (MiniLM)                                 │
│                                                                              │
│  4. Retrieval                                               ~100ms           │
│     └── Query FAISS index (nprobe=32)                                        │
│         └── Get top-15 candidates                                            │
│             └── Load document chunks from disk                               │
│                 └── Rerank to top-5                                          │
│                                                                              │
│  5. Context Assembly                                         ~50ms           │
│     └── Format system prompt                                                 │
│         └── Insert retrieved chunks with citations                           │
│             └── Add user query                                               │
│                 └── Truncate to 4096 tokens                                  │
│                                                                              │
│  6. Inference                                            ~15-30 sec          │
│     ├── RKLLM (NPU) ──► Stream tokens (~30 tok/s) ─┐                        │
│     │                                               │                        │
│     └── llama.cpp (CPU fallback) ──► (~10 tok/s) ──┴──► Response            │
│                                                                              │
│  7. Response Handling                                        ~50ms           │
│     └── Parse response                                                       │
│         └── Extract/format citations                                         │
│             └── Update UI state                                              │
│                                                                              │
│  8. Display                                                 ~300ms           │
│     └── Render to frame buffer                                               │
│         └── Partial update to E-ink                                          │
│             └── Log to history database                                      │
│                                                                              │
│  9. Optional TTS                                          ~5-10 sec          │
│     └── User requests read-aloud                                             │
│         └── Piper synthesizes audio                                          │
│             └── Play through TPA2016D2 → speaker                             │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

### 7.2 Timing Budget

| Stage | Target | Maximum |
|-------|--------|---------|
| Input processing | 50 ms | 100 ms |
| Voice transcription (if used) | 5 s | 10 s |
| Embedding | 50 ms | 100 ms |
| Vector search + rerank | 150 ms | 300 ms |
| Context assembly | 50 ms | 100 ms |
| Inference (~200 tokens) | 10 s (NPU @ 3-6 tok/s) | 25 s (CPU @ 2-3 tok/s) |
| Display update | 300 ms | 500 ms |
| **Total (keyboard)** | **~11 s** | **~26 s** |
| **Total (voice)** | **~16 s** | **~36 s** |

### 7.3 Caching Strategy

| Cache | TTL | Size Limit | Eviction |
|-------|-----|------------|----------|
| Query embeddings | Session | 1000 entries | LRU |
| Retrieved contexts | Session | 100 entries | LRU |
| Rendered pages | Session | 50 pages | LRU |
| Recent responses | Permanent | 1000 entries | Oldest |
| Audio (TTS) | 10 minutes | 25 MB | LRU |

### 7.4 Archival Subsystem (Vault Edition)

The archival subsystem provides long-term data integrity for 50-year passive survivability. It implements the `ark-seal` and `ark-unseal` commands, integrity manifests, and discovery boot sequence.

#### 7.4.1 Architecture

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                         ARCHIVAL SUBSYSTEM                                 │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  ┌───────────────┐ │
│  │    ark-seal     │  │   ark-unseal    │  │   ark-verify    │  │ discovery-boot│ │
│  │   (seal mode)   │  │ (unseal mode)   │  │ (verify integ.) │  │ (TIME_LOST)   │ │
│  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘  └───────┬───────┘ │
│           │                  │                  │                  │             │
│           └─────────┬────────┴────────┬────────┴─────────┘             │
│                     │                  │                                    │
│                     ▼                  ▼                                    │
│           ┌───────────────────────────────────────────────────┐            │
│           │              MANIFEST MANAGER                     │            │
│           │  • Generate SHA-256 for all corpus files           │            │
│           │  • Store in /recovery/manifests/                   │            │
│           │  • Replicate to SD (/archive) and eMMC             │            │
│           └───────────────────────────────────────────────────┘            │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

#### 7.4.2 Seal Mode Implementation

```python
import hashlib
import subprocess
import json
from pathlib import Path
from datetime import datetime
from dataclasses import dataclass, asdict

@dataclass
class SealState:
    sealed: bool
    seal_timestamp: str
    seal_version: str = "1.0"
    manifest_hash: str = ""

class ArchivalManager:
    """Manage archival seal state and integrity manifests.
    
    Implements 50-year passive survivability via:
    - Read-only remount of data partitions
    - SHA-256 integrity manifests with triple redundancy
    - Seal flag preventing accidental writes
    """
    
    SEAL_FLAG = Path("/recovery/.sealed")
    SEAL_STATE = Path("/recovery/seal_state.json")
    MANIFEST_DIR = Path("/recovery/manifests")
    
    # Manifest locations (triple redundancy)
    MANIFEST_LOCATIONS = [
        Path("/recovery/manifests"),      # NVMe
        Path("/archive/.manifests"),       # SD card
        Path("/mnt/emmc/recovery/manifests")  # eMMC (mounted temporarily)
    ]
    
    def __init__(self):
        self.state = self._load_state()
    
    def _load_state(self) -> SealState:
        """Load seal state from disk."""
        if self.SEAL_STATE.exists():
            try:
                data = json.loads(self.SEAL_STATE.read_text())
                return SealState(**data)
            except (json.JSONDecodeError, TypeError):
                pass
        return SealState(sealed=False, seal_timestamp="")
    
    def _save_state(self):
        """Persist seal state."""
        self.SEAL_STATE.write_text(json.dumps(asdict(self.state)))
    
    async def seal(self, oled=None, eink=None) -> bool:
        """Execute archival seal procedure.
        
        Steps:
        1. Generate integrity manifests for /data and /archive
        2. Replicate manifests to all three locations
        3. Remount /data and /archive as read-only
        4. Set seal flag
        5. Update OLED status
        
        Returns: True if successful
        """
        if self.state.sealed:
            return True  # Already sealed
        
        if oled:
            oled.set_state("SEALING")
        if eink:
            await eink.show_message("Sealing archive...\nGenerating integrity manifests...")
        
        # Step 1: Generate manifests
        data_manifest = await self._generate_manifest("/data")
        archive_manifest = await self._generate_manifest("/archive")
        
        combined_manifest = {
            "generated": datetime.utcnow().isoformat(),
            "version": "1.0",
            "data": data_manifest,
            "archive": archive_manifest
        }
        
        # Step 2: Calculate manifest hash
        manifest_json = json.dumps(combined_manifest, sort_keys=True)
        manifest_hash = hashlib.sha256(manifest_json.encode()).hexdigest()
        
        # Step 3: Save to all locations
        self.MANIFEST_DIR.mkdir(parents=True, exist_ok=True)
        manifest_path = self.MANIFEST_DIR / "corpus_manifest.json"
        manifest_path.write_text(manifest_json)
        
        # Replicate to SD
        sd_manifest_dir = Path("/archive/.manifests")
        sd_manifest_dir.mkdir(parents=True, exist_ok=True)
        (sd_manifest_dir / "corpus_manifest.json").write_text(manifest_json)
        
        # Replicate to eMMC (mount temporarily)
        try:
            subprocess.run(["mount", "/dev/mmcblk0p1", "/mnt/emmc"], check=True)
            emmc_manifest_dir = Path("/mnt/emmc/recovery/manifests")
            emmc_manifest_dir.mkdir(parents=True, exist_ok=True)
            (emmc_manifest_dir / "corpus_manifest.json").write_text(manifest_json)
            subprocess.run(["umount", "/mnt/emmc"], check=True)
        except subprocess.CalledProcessError as e:
            # eMMC replication failed, continue with warning
            pass
        
        if eink:
            await eink.show_message("Remounting partitions read-only...")
        
        # Step 4: Remount read-only
        subprocess.run(["mount", "-o", "remount,ro", "/data"], check=True)
        subprocess.run(["mount", "-o", "remount,ro", "/archive"], check=True)
        
        # Step 5: Set seal flag
        self.SEAL_FLAG.touch()
        self.state = SealState(
            sealed=True,
            seal_timestamp=datetime.utcnow().isoformat(),
            manifest_hash=manifest_hash
        )
        self._save_state()
        
        if oled:
            oled.set_state("SEALED")
        if eink:
            await eink.show_message(
                f"ARCHIVE SEALED\n\n"
                f"Timestamp: {self.state.seal_timestamp}\n"
                f"Manifest hash: {manifest_hash[:16]}...\n\n"
                f"Data partitions are now read-only.\n"
                f"Use 'ark-unseal' to enable writes."
            )
        
        return True
    
    async def _generate_manifest(self, path: str) -> dict:
        """Generate SHA-256 manifest for all files in path."""
        manifest = {}
        root = Path(path)
        
        for file_path in root.rglob("*"):
            if file_path.is_file():
                rel_path = str(file_path.relative_to(root))
                hash_val = await self._hash_file(file_path)
                manifest[rel_path] = {
                    "sha256": hash_val,
                    "size": file_path.stat().st_size
                }
        
        return manifest
    
    async def _hash_file(self, path: Path) -> str:
        """Calculate SHA-256 hash of file."""
        sha256 = hashlib.sha256()
        with open(path, "rb") as f:
            while chunk := f.read(65536):
                sha256.update(chunk)
        return sha256.hexdigest()
    
    async def unseal(self, confirm: bool = False) -> bool:
        """Remove archival seal (requires confirmation).
        
        WARNING: Unsealing breaks integrity guarantees.
        New seal must be applied after updates.
        """
        if not self.state.sealed:
            return True  # Already unsealed
        
        if not confirm:
            raise ValueError("Unseal requires explicit confirmation")
        
        # Remount read-write
        subprocess.run(["mount", "-o", "remount,rw", "/data"], check=True)
        subprocess.run(["mount", "-o", "remount,rw", "/archive"], check=True)
        
        # Remove seal flag
        self.SEAL_FLAG.unlink(missing_ok=True)
        self.state.sealed = False
        self._save_state()
        
        return True
    
    def is_sealed(self) -> bool:
        """Check if archive is currently sealed."""
        return self.SEAL_FLAG.exists() and self.state.sealed
```

#### 7.4.3 Integrity Verification

```python
from enum import Enum
from dataclasses import dataclass

class IntegrityStatus(Enum):
    INTACT = "INTACT"         # All files match manifest
    DEGRADED = "DEGRADED"     # Some files corrupted but recoverable
    CRITICAL = "CRITICAL"     # Critical files corrupted, recovery needed

@dataclass
class VerificationResult:
    status: IntegrityStatus
    total_files: int
    passed: int
    failed: int
    missing: int
    failed_files: list  # List of corrupted file paths
    critical_files: list  # Subset of failed_files that are critical

class IntegrityVerifier:
    """Verify corpus integrity against stored manifests."""
    
    CRITICAL_PATHS = [
        "models/",           # LLM and embedding models
        "corpus/medical/",   # Medical reference
        "corpus/survival/",  # Core survival content
        "index/",            # Vector index
    ]
    
    async def verify(self, progress_callback=None) -> VerificationResult:
        """Run full integrity verification.
        
        Args:
            progress_callback: Optional callback(current, total, filename)
        
        Returns:
            VerificationResult with detailed status
        """
        # Load manifest
        manifest_path = Path("/recovery/manifests/corpus_manifest.json")
        if not manifest_path.exists():
            # Try SD backup
            manifest_path = Path("/archive/.manifests/corpus_manifest.json")
        
        if not manifest_path.exists():
            return VerificationResult(
                status=IntegrityStatus.CRITICAL,
                total_files=0, passed=0, failed=0, missing=1,
                failed_files=[], critical_files=["MANIFEST MISSING"]
            )
        
        manifest = json.loads(manifest_path.read_text())
        
        passed = 0
        failed = 0
        missing = 0
        failed_files = []
        critical_files = []
        
        # Verify /data
        data_manifest = manifest.get("data", {})
        total = len(data_manifest)
        
        for i, (rel_path, expected) in enumerate(data_manifest.items()):
            if progress_callback:
                progress_callback(i + 1, total, rel_path)
            
            file_path = Path("/data") / rel_path
            
            if not file_path.exists():
                missing += 1
                failed_files.append(rel_path)
                if self._is_critical(rel_path):
                    critical_files.append(rel_path)
                continue
            
            actual_hash = await self._hash_file(file_path)
            if actual_hash != expected["sha256"]:
                failed += 1
                failed_files.append(rel_path)
                if self._is_critical(rel_path):
                    critical_files.append(rel_path)
            else:
                passed += 1
        
        # Determine status
        if critical_files:
            status = IntegrityStatus.CRITICAL
        elif failed_files:
            status = IntegrityStatus.DEGRADED
        else:
            status = IntegrityStatus.INTACT
        
        return VerificationResult(
            status=status,
            total_files=total,
            passed=passed,
            failed=failed,
            missing=missing,
            failed_files=failed_files,
            critical_files=critical_files
        )
    
    def _is_critical(self, path: str) -> bool:
        """Check if path is in critical file set."""
        return any(path.startswith(p) for p in self.CRITICAL_PATHS)
    
    async def _hash_file(self, path: Path) -> str:
        sha256 = hashlib.sha256()
        with open(path, "rb") as f:
            while chunk := f.read(65536):
                sha256.update(chunk)
        return sha256.hexdigest()
```

#### 7.4.4 Discovery Boot Sequence

```python
from datetime import datetime
import subprocess

class DiscoveryBootHandler:
    """Handle boot after long-term dormancy (TIME_LOST condition).
    
    Detects when RTC has lost track of time (year < 2025) and provides
    orientation for an unknown future handler who may have discovered
    the device without prior knowledge of its purpose.
    """
    
    MIN_VALID_YEAR = 2025  # Device created in 2025
    
    ORIENTATION_MESSAGE = """
╔═════════════════════════════════════════════════════════════╗
║          KNOWLEDGE ARK RECOVERED                         ║
╠═════════════════════════════════════════════════════════════╣
║                                                             ║
║  This device contains preserved human knowledge.            ║
║                                                             ║
║  WHAT THIS IS:                                              ║
║  • An offline knowledge archive with AI assistant           ║
║  • Solar-powered, designed for 50+ year storage             ║
║  • Contains: medical, agricultural, technical references    ║
║                                                             ║
║  HOW TO USE:                                                ║
║  • Type questions using attached keyboard                   ║
║  • Navigate menus with D-pad buttons                        ║
║  • Charge via solar panel (fold-out)                        ║
║                                                             ║
║  INTEGRITY CHECK: Running...                                ║
║                                                             ║
╚═════════════════════════════════════════════════════════════╝
"""
    
    def __init__(self, rtc, oled, eink, verifier: IntegrityVerifier):
        self.rtc = rtc
        self.oled = oled
        self.eink = eink
        self.verifier = verifier
    
    def check_time_lost(self) -> bool:
        """Check if RTC indicates TIME_LOST condition."""
        try:
            rtc_time = self.rtc.read_datetime()
            return rtc_time.year < self.MIN_VALID_YEAR
        except Exception:
            # RTC read failure also indicates TIME_LOST
            return True
    
    async def run_discovery_sequence(self):
        """Execute discovery boot sequence.
        
        Called when TIME_LOST detected on boot.
        """
        # Show orientation on E-ink
        await self.eink.show_message(self.ORIENTATION_MESSAGE)
        self.oled.set_state("DISCOVERY")
        
        # Run integrity verification
        def progress(current, total, filename):
            percent = int((current / total) * 100)
            self.oled.show_text(f"Verify: {percent}%", filename[:16])
        
        result = await self.verifier.verify(progress_callback=progress)
        
        # Display result
        status_display = {
            IntegrityStatus.INTACT: (
                "\n\nINTEGRITY: INTACT \u2705\n\n"
                "All knowledge files verified.\n"
                "Device is ready for use."
            ),
            IntegrityStatus.DEGRADED: (
                f"\n\nINTEGRITY: DEGRADED \u26a0\ufe0f\n\n"
                f"{result.failed} files corrupted (non-critical).\n"
                f"Recovery from backup recommended.\n\n"
                f"Press HOME to attempt recovery.\n"
                f"Press ESC to continue anyway."
            ),
            IntegrityStatus.CRITICAL: (
                f"\n\nINTEGRITY: CRITICAL \u274c\n\n"
                f"Critical files corrupted:\n"
                + "\n".join(f"  • {f}" for f in result.critical_files[:5]) +
                f"\n\nRecovery required.\n"
                f"Press HOME to recover from SD backup."
            )
        }
        
        await self.eink.show_message(
            self.ORIENTATION_MESSAGE.replace(
                "INTEGRITY CHECK: Running...",
                f"INTEGRITY: {result.status.value}"
            ) + status_display[result.status]
        )
        
        self.oled.set_state(result.status.value)
        
        return result
    
    async def prompt_time_sync(self):
        """Prompt user to set current date/time."""
        await self.eink.show_message(
            "DATE/TIME REQUIRED\n\n"
            "The internal clock has lost track of time.\n\n"
            "Options:\n"
            "  1. Connect GPS dongle (auto-sync)\n"
            "  2. Enter date manually\n\n"
            "Press CENTER to enter date manually.\n"
            "Or connect GPS and press HOME to sync."
        )
    
    async def manual_date_entry(self):
        """Guide user through manual date entry."""
        # Simplified: in production, would have full date picker UI
        # For now, accept YYYY-MM-DD format via keyboard
        await self.eink.show_message(
            "ENTER CURRENT DATE\n\n"
            "Type the current date as: YYYY-MM-DD\n"
            "Example: 2075-06-15\n\n"
            "Press ENTER when done."
        )
    
    async def gps_time_sync(self) -> bool:
        """Attempt to sync time from GPS."""
        try:
            # Check for GPS device
            result = subprocess.run(
                ["gpspipe", "-w", "-n", "5"],
                capture_output=True, timeout=30
            )
            
            if result.returncode == 0:
                # Parse GPS time and set RTC
                # (simplified - would parse NMEA/JSON in production)
                subprocess.run(["hwclock", "--systohc"], check=True)
                return True
        except (subprocess.TimeoutExpired, subprocess.CalledProcessError):
            pass
        
        return False
```

#### 7.4.5 Boot Integration

The discovery boot sequence integrates with systemd via a oneshot service:

**ark-discovery.service**
```ini
[Unit]
Description=Ark Discovery Boot Handler
After=local-fs.target
Before=ark-main.service
ConditionPathExists=/recovery/.sealed

[Service]
Type=oneshot
ExecStart=/opt/ark/venv/bin/python -m ark.discovery_boot
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

**Boot Flow:**
```
power-on
    └─▶ systemd
         ├─▶ local-fs.target (mount filesystems)
         ├─▶ ark-power.service (start power monitoring)
         ├─▶ ark-discovery.service (if .sealed exists)
         │       ├─▶ Check RTC for TIME_LOST
         │       ├─▶ If TIME_LOST: run discovery sequence
         │       └─▶ Verify integrity, display status
         └─▶ ark-main.service (normal operation)
```

---

## 8. Error Handling

### 8.1 Failure Modes

| Component | Failure Mode | Detection | Recovery |
|-----------|--------------|-----------|----------|
| RKLLM Server | Process crash | Healthcheck timeout | Restart via systemd, fallback to llama.cpp |
| llama.cpp | Process crash | Healthcheck timeout | Restart via systemd |
| Embed Server | Process crash | Healthcheck timeout | Restart via systemd |
| Vector Store | Index corruption | Load error | Rebuild from /recovery |
| E-ink Display | USB disconnect | I/O error | Reconnect, show error on OLED |
| OLED Display | I2C error | Write failure | Retry, degrade gracefully |
| ReSpeaker | USB disconnect | ALSA error | Disable voice input, notify user |
| NVMe | Read errors | I/O error | PAR2 repair, if fails → Lifeboat |
| NVMe | Unresponsive | Timeout | Boot Lifeboat from eMMC |
| Power | Critical battery | Voltage monitor | Graceful shutdown at 18.0V |
| Power | Load disconnect | Hardware protection | Auto-opens at 16.0V, closes at 20.0V |
| Thermal | Overheat >70°C | Temp sensor | Throttle inference |
| Thermal | Critical >85°C | KSD9700 | Hardware cutoff, auto-reset at 65°C |

### 8.2 Storage Recovery Procedures

```python
class StorageRecovery:
    """Handle NVMe storage errors with PAR2 recovery.
    
    PAR2 Path Mapping Convention:
    - Source: /data/library/corpus/wikipedia/chunk_00001.json
    - PAR2:   /recovery/parity/library_corpus_wikipedia_chunk_00001.par2
    
    Mapping rules:
    1. Strip /data/ prefix
    2. Replace path separators with underscores
    3. Replace file extension with .par2
    4. Prepend /recovery/parity/
    """
    
    @staticmethod
    def get_par2_name(path: str) -> str:
        """Convert source path to PAR2 filename.
        
        Example: /data/models/llama.rkllm → models_llama.par2
        """
        # Strip /data/ prefix
        if path.startswith("/data/"):
            path = path[6:]
        # Replace separators and extension
        base = path.replace("/", "_")
        base = base.rsplit(".", 1)[0] if "." in base else base
        return f"{base}.par2"
    
    async def handle_read_error(self, path: str, error: IOError):
        """Handle NVMe read errors with PAR2 recovery chain."""
        logger.error(f"Read error on {path}: {error}")
        
        # Step 1: Try PAR2 repair
        if await self.par2_repair(path):
            logger.info("PAR2 repair successful")
            return True
        
        # Step 2: Check if critical system file
        if self.is_critical_path(path):
            # Step 3: Alert user, suggest Lifeboat restore
            await self.alert_user(
                "Storage Error",
                "Critical file corrupted and repair failed.\n"
                "Recommend: Boot to Lifeboat OS and restore from recovery."
            )
            return False
        
        # Non-critical file: log and continue
        logger.warning(f"Non-critical file {path} unrecoverable")
        return False
    
    async def par2_repair(self, path: str) -> bool:
        """Attempt PAR2 repair of corrupted file."""
        par2_path = f"/recovery/parity/{self.get_par2_name(path)}"
        result = subprocess.run(
            ["par2", "repair", par2_path, path],
            capture_output=True
        )
        return result.returncode == 0
    
    async def boot_lifeboat(self):
        """Guide user to boot Lifeboat OS from eMMC.
        
        Shows recovery instructions on both E-ink (primary) and OLED (backup)
        in case E-ink display has failed.
        """
        recovery_steps = [
            "1. Power off (hold 5s)",
            "2. Remove NVMe (if accessible)", 
            "3. Power on → boots from eMMC",
            "4. Follow Lifeboat recovery menu"
        ]
        
        # Primary: E-ink display (detailed instructions)
        try:
            await self.eink.show_message(
                "RECOVERY MODE\n\n" + "\n".join(recovery_steps)
            )
        except Exception as e:
            logger.error(f"E-ink display failed during recovery: {e}")
        
        # Backup: OLED display (abbreviated, always attempted)
        try:
            self.oled.set_state("RECOVERY")
            # Cycle through steps on OLED (limited space)
            for i, step in enumerate(recovery_steps, 1):
                self.oled.show_text(f"Step {i}/4", step[:20])
                await asyncio.sleep(3)
        except Exception as e:
            logger.error(f"OLED display failed during recovery: {e}")
            # Last resort: audio beep pattern for "recovery mode"
            try:
                await self.audio.play_pattern("recovery")
            except:
                pass  # All display methods exhausted
```

### 8.3 Logging Strategy

| Log Level | Destination | Retention |
|-----------|-------------|-----------|
| ERROR, CRITICAL | /var/log/ark/error.log | 30 days |
| WARNING | /var/log/ark/warn.log | 14 days |
| INFO | /var/log/ark/info.log | 7 days |
| DEBUG | /var/log/ark/debug.log | 1 day |

```python
import logging
from logging.handlers import RotatingFileHandler

LOGGING_CONFIG = {
    "version": 1,
    "handlers": {
        "error_file": {
            "class": "logging.handlers.RotatingFileHandler",
            "filename": "/var/log/ark/error.log",
            "maxBytes": 10_000_000,
            "backupCount": 5,
            "level": "ERROR",
        },
        "info_file": {
            "class": "logging.handlers.RotatingFileHandler",
            "filename": "/var/log/ark/info.log",
            "maxBytes": 10_000_000,
            "backupCount": 3,
            "level": "INFO",
        },
        "oled": {
            "class": "ark.logging.OLEDHandler",
            "level": "CRITICAL",  # Show critical errors on OLED
        },
    },
    "root": {
        "level": "DEBUG",
        "handlers": ["error_file", "info_file"],
    },
}
```

**OLEDHandler Implementation:**

```python
import time
from logging import Handler, LogRecord
from luma.core.interface.serial import i2c
from luma.oled.device import ssd1306
from PIL import ImageFont

class OLEDHandler(Handler):
    """
    Custom logging handler that displays CRITICAL errors on the OLED.
    Rate-limited to prevent screen flicker from repeated errors.
    """
    
    def __init__(self, i2c_port=1, i2c_address=0x3C):
        super().__init__()
        serial = i2c(port=i2c_port, address=i2c_address)
        self.device = ssd1306(serial)
        self.font = ImageFont.load_default()
        self.last_emit = 0
        self.min_interval = 5.0  # Minimum 5 seconds between updates
    
    def emit(self, record: LogRecord):
        now = time.time()
        if now - self.last_emit < self.min_interval:
            return  # Rate limit
        
        self.last_emit = now
        msg = self.format(record)
        # Truncate to fit 128px width (~21 chars at 6px font)
        display_msg = msg[:21] if len(msg) > 21 else msg
        
        # Display on OLED
        with canvas(self.device) as draw:
            draw.rectangle(self.device.bounding_box, fill="black")
            draw.text((0, 0), "ERR:", fill="white", font=self.font)
            draw.text((0, 12), display_msg, fill="white", font=self.font)
```

### 8.4 User-Facing Errors

| Error Type | E-ink Message | OLED | Action |
|------------|---------------|------|--------|
| No results | "I couldn't find information about that. Try different keywords or browse by topic." | READY | Show browse button |
| Inference timeout | "This is taking longer than expected. Please wait or press ESC to cancel." | BUSY | Show cancel option |
| Voice not recognized | "I didn't catch that. Please try again or type your question." | READY | Show keyboard prompt |
| Critical battery | "Battery critically low. Saving your work and shutting down." | CRIT | Auto-shutdown |
| Storage error | "Storage issue detected. Running repair..." | ERR | Run PAR2, show progress |
| Thermal warning | "System warm. Reducing speed to cool down." | HOT | Throttle, continue |
| Thermal critical | "Overheating. Shutting down for safety." | HOT | Shutdown |

### 8.5 Systemd Service Definitions

All Ark services are managed by systemd with proper dependencies and restart policies:

**ark-main.service** (Primary application)
```ini
[Unit]
Description=Universal Knowledge Ark Main Application
After=multi-user.target ark-power.service
Requires=ark-power.service
Wants=ark-oled.service

[Service]
Type=simple
User=ark
Group=ark
WorkingDirectory=/opt/ark
ExecStart=/opt/ark/venv/bin/python -m ark.main
Restart=on-failure
RestartSec=5
WatchdogSec=120
Environment=PYTHONUNBUFFERED=1

[Install]
WantedBy=multi-user.target
```

**ark-power.service** (Power monitoring - must start first)
```ini
[Unit]
Description=Ark Power Monitor (INA219 + LVD)
After=sysinit.target
Before=ark-main.service

[Service]
Type=simple
User=root
ExecStart=/opt/ark/venv/bin/python -m ark.power_monitor
Restart=always
RestartSec=2

[Install]
WantedBy=multi-user.target
```

**ark-oled.service** (Status display)
```ini
[Unit]
Description=Ark OLED Status Display
After=ark-power.service
BindsTo=ark-main.service

[Service]
Type=simple
User=ark
ExecStart=/opt/ark/venv/bin/python -m ark.oled_display
Restart=on-failure
RestartSec=3

[Install]
WantedBy=multi-user.target
```

**Service Dependency Graph:**
```
sysinit.target
    └─▶ ark-power.service (REQUIRED)
           └─▶ ark-main.service
                  └─▶ ark-oled.service (bound)
```

---

## 9. Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0.0 | Dec 2025 | Initial specification (RPi CM5, 8GB, llama.cpp only) |
| 2.0.0 | Dec 2025 | Complete rewrite: OPi 5+ 32GB, RKLLM+llama.cpp hybrid, 7B model, Whisper+VOSK STT, Piper+espeak TTS, IT8951 USB display, D-pad input, PAR2 recovery |
| 2.1.0 | Dec 2025 | CRIT-014: Replaced unrealistic suspend-to-RAM with reliable standby mode (2.5W). Simplified power state machine. |
| 2.2.0 | Dec 2025 | CRIT-009: Updated LLM spec to Llama 3.1 8B W8A8 with pre-converted HuggingFace models. Corrected performance expectations (3-6 tok/s NPU). Added §3.7 Model Acquisition with download procedures. |
| 2.3.0 | Dec 2025 | CRIT-003: Implemented PowerMonitor class using Adafruit INA219. Enables voltage/current/power sensing, SOC estimation, software LVD at 12.0V, and OLED status display. |
| 2.4.0 | Dec 2025 | CRIT-006: Assigned GPIO pins for 7 input buttons using OPi 5+ 40-pin header. Added GPIOPin dataclass with chip/line/name/physical mapping. Updated InputHandler to use multiple gpiochips (pins span banks 0-4). Power button uses hardware PMIC, not GPIO. |
| 2.5.0 | Dec 2025 | CRIT-011: Updated §1.2 process table to show rkllm-server at 14GB (matching §3.3 runtime requirements). Added note on runtime exclusivity. Aligned with 03-software-requirements.md v2.3.0. |
| 2.6.0 | Dec 2025 | CRIT-015: Updated §2.3 Vector Store for full Wikipedia (~8M chunks). FAISS IVFFlat with 4096 clusters, nprobe=64, memory-mapped loading (14GB on disk, 2-4GB active RAM). Added ~50ms latency vs in-memory. Aligned with 05-library-manifest.md v2.3.0. |
| **2.7.0** | **Dec 2025** | **HIGH-031/032/040: Complete coulomb counting implementation in PowerMonitor class. Integrates current over time for ±5% SOC accuracy. Auto-calibrates at full charge (13.6V + <100mA for 5min) and empty (11.5V). State persists across reboots via /var/lib/ark/power_state.json. Added is_charging(), get_time_remaining_hours(), get_time_to_full_hours(). OLED shows '*' when uncalibrated.** |
| **2.8.0** | **Dec 2025** | **MED-033 through MED-043: ark-power memory 64→16MB. Mali GPU clarification in llama.cpp config. Whisper speed corrected to ~2× realtime. Audio feedback files defined with sox generation. Armbian sysfs path note. Timing budget corrected (inference 10s NPU target, 16s voice total). TTS cache 50→25MB. PAR2 path mapping convention documented. Recovery mode OLED fallback added.** |
| 2.9.0 | Dec 2025 | LOW issues batch: §1.5 Added consolidated dependency table with all Python packages, versions, and purposes. §8.3 Added OLEDHandler class implementation with rate limiting. §8.5 Added systemd service definitions (ark-main, ark-power, ark-oled) with dependency graph. |
| **3.0.0** | **Dec 2025** | **Lazarus-Phoenix alignment:** Updated all voltage thresholds for Na-ion 8S (24V nominal). PowerMonitor: SOFTWARE_LVD 12.0V→18.0V, HARDWARE_LVD 11.5V→16.0V, WARNING 12.2V→19.0V, FULL_CHARGE 13.6V→32.0V. Battery capacity 100Ah→40Ah. SOC_CURVE updated from LiFePO4 to Na-ion (more linear). Added INA219 voltage range note (placed after load disconnect). PowerManager thresholds aligned. Error handling table updated. |
| **3.1.0** | **Dec 2025** | **VAULT EDITION:** Archival subsystem implementation for 50-year passive survivability. NEW §7.4 Archival Subsystem with: §7.4.1 Architecture (ark-seal, ark-unseal, ark-verify, discovery-boot). §7.4.2 Seal Mode Implementation (ArchivalManager class with manifest generation, triple redundancy, read-only remount). §7.4.3 Integrity Verification (IntegrityVerifier class with INTACT/DEGRADED/CRITICAL status). §7.4.4 Discovery Boot Sequence (DiscoveryBootHandler for TIME_LOST condition, orientation message, GPS sync). §7.4.5 Boot Integration (ark-discovery.service systemd unit). Aligned with 03-software-requirements.md v2.8.0. HIGH-STG-003 and HIGH-STG-004 resolved. |

---

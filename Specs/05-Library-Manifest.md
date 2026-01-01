---
version: "2.5.0"
status: complete
project: "[[_Brief]]"
updated: 2025-12-29
source: "~/universal-knowledge-ark/specs/05-library-manifest.md"
tags:
  - spec
  - universal-knowledge-ark
  - content
---

# Library Manifest

**Version:** 2.5.0

## Summary

Knowledge base content sources, preprocessing pipeline, and storage budget.

## Content Sources

| Source | Size | Articles |
|--------|------|----------|
| Wikipedia (full) | 95 GB | 6.7M |
| iFixit | 5 GB | — |
| WikiHow | 10 GB | — |
| OpenStax | 3 GB | — |
| **Total** | **230 GB** | — |

## Storage Budget

| Partition | Size | Used |
|-----------|------|------|
| /boot | 1 GB | — |
| /root | 64 GB | ~20 GB |
| /data | 404 GB | 230 GB (57%) |
| Reserved | ~11 GB | — |

## Preprocessing Pipeline

1. **ZIM Extraction** — python-libzim (no Kiwix runtime)
2. **HTML → Text** — lxml parsing, structure preservation
3. **Chunking** — 512 token chunks with 128 overlap
4. **Embedding** — MiniLM-L6-v2 (384-dim vectors)
5. **Indexing** — FAISS IVFFlat with 4096 clusters

## Import Format

```
/data/library/
├── wikipedia/
│   ├── chunks/          # Text chunks
│   ├── embeddings/      # Vector files
│   └── metadata.json    # Source info
├── ifixit/
├── wikihow/
└── openstax/
```

## Dependencies

- [[04-Technical-Design]] — RAG implementation uses this content
- [[01-Hardware-Specification]] — Storage hardware specs

---

## 1. Content Strategy

### 1.1 Selection Criteria

Content is selected based on:

| Criterion | Weight | Rationale |
|-----------|--------|-----------|
| **Actionability** | High | Information must be usable, not just informational |
| **Durability** | High | Content should remain valid for 10+ years |
| **Offline Completeness** | High | No external references required |
| **Illustration Value** | High | Diagrams, photos essential for practical tasks |
| **Broad Applicability** | Medium | Useful across diverse scenarios |
| **Depth vs Breadth** | Variable | Deep in critical areas, broad in reference |

### 1.2 Exclusion Criteria

Explicitly excluded content:
- Time-sensitive information (news, current events)
- Content requiring frequent updates (software versions, prices)
- Entertainment-focused material
- Highly specialized content with narrow applicability
- Content with restrictive licensing that prohibits personal use
- Duplicate coverage (prefer authoritative sources)

**Note on CC-BY-NC-SA content:** iFixit and WikiHow use CC-BY-NC-SA licenses, which permit non-commercial use. Since this is a personal-use project (not commercially distributed), this content is included in the base build. The NC-SA restriction only applies to commercial redistribution, not personal offline reference.

### 1.3 Knowledge Domain Priority

| Priority | Domain | Rationale | Primary Sources |
|----------|--------|-----------|-----------------|
| 1 - Critical | Medical/First Aid | Immediate survival value | WHO, medical handbooks |
| 1 - Critical | Water/Food Safety | Daily survival needs | EPA, USDA, foraging guides |
| 1 - Critical | Shelter/Security | Basic human needs | Construction, WikiHow |

**Domain Note:** Shelter/Security includes both physical construction (temporary/permanent structures, weatherproofing) and situational security (perimeter awareness, conflict de-escalation, community defense coordination). Content emphasizes non-violent approaches first, with physical security as last resort.

| 2 - High | Agriculture/Gardening | Long-term sustainability | USDA, permaculture |
| 2 - High | Mechanical Repair | Infrastructure maintenance | iFixit, repair manuals |
| 2 - High | Electrical/Power | Modern necessity | Solar, wiring guides |
| 3 - Medium | Preservation/Storage | Resource management | USDA, traditional methods |
| 3 - Medium | Navigation/Orientation | Mobility and rescue | Maps, celestial navigation |
| 3 - Medium | Communication | Community coordination | Radio, signaling |
| 4 - Reference | Science/Engineering | Problem-solving foundation | Wikipedia, OpenStax |
| 4 - Reference | History/Geography | Cultural preservation | Wikipedia selective |
| 5 - Educational | Mathematics | Universal tool | OpenStax |
| 5 - Educational | Language/Writing | Knowledge transmission | Reference materials |

---

## 2. Source Inventory

**Content Acquisition Model:** This manifest describes content for a personal-use device. Sources fall into three categories:

| Category | Examples | Acquisition |
|----------|----------|-------------|
| **Open license** | Wikipedia, OpenStax, iFixit, WikiHow | Included in base build (CC-BY-SA, CC-BY-NC-SA) |
| **Public domain** | USDA guides, EPA publications, pre-1928 works | Included in base build |
| **Owner-provided** | Purchased books, regional guides | Owner loads onto their own device |

Owner-provided content is not distributed with the project; the owner is responsible for legally acquiring and loading this content via the Import USB workflow (see §6.1).

### 2.1 Primary Reference Sources (ZIM Extraction)

All ZIM files are processed through python-libzim extraction pipeline and indexed into the unified RAG system. No Kiwix runtime dependency.

| Source | Format | Raw Size | Extracted | License | Notes |
|--------|--------|----------|-----------|---------|-------|
| **Wikipedia (full)** | ZIM | ~95 GB | ~95 GB | CC-BY-SA | Complete English Wikipedia with images (~6.7M articles) |
| **iFixit** | ZIM | ~5 GB | ~5 GB | CC-BY-NC-SA | Device/appliance repair guides |
| **WikiHow** | ZIM | ~12 GB | ~10 GB | CC-BY-NC-SA | Practical how-to articles |
| **OpenStax Textbooks** | ZIM/PDF | ~3 GB | ~3 GB | CC-BY | Full science/math curriculum |

**Why full Wikipedia:**
- Eliminates complex category-based selection criteria (was CRIT-015)
- Ensures no useful knowledge is accidentally excluded
- Storage budget permits (~230 GB processed, 57% of 404 GB /data partition)
- Memory-mapped FAISS index keeps RAM usage manageable
- Broader coverage supports unexpected queries (history, culture, science)

### 2.2 Medical & First Aid

| Source | Type | Size (est.) | License |
|--------|------|-------------|---------|
| WHO Emergency Guidelines | PDF | 50 MB | CC-BY |
| Survival Medicine Handbook | EPUB | 15 MB | Owner-provided |
| First Aid Manual (Red Cross style) | PDF | 30 MB | Open access |
| Wilderness First Responder Guide | PDF | 25 MB | Owner-provided |
| Where There Is No Doctor | PDF | 20 MB | CC-BY-NC |
| Where There Is No Dentist | PDF | 10 MB | CC-BY-NC |
| Medical Diagnosis Flowcharts | Custom | 10 MB | Created |
| Drug Interactions Database | SQLite | 20 MB | Public domain |

**Subtotal Medical:** ~180 MB raw

### 2.3 Water & Food Safety

| Source | Type | Size (est.) | License |
|--------|------|-------------|---------|
| Water Purification Guide (EPA) | PDF | 10 MB | Public domain |
| Food Preservation (USDA Complete) | PDF | 40 MB | Public domain |
| Ball Blue Book (canning) | PDF | 30 MB | Owner-provided |
| Foraging Guides (regional, multiple) | EPUB | 100 MB | Various |
| Fishing & Hunting Manuals | PDF | 50 MB | Various |
| Edible Plant Identification (illustrated) | Images + Text | 150 MB | CC-BY |
| Poisonous Plant Identification | Images + Text | 80 MB | CC-BY |
| Mushroom Identification (conservative) | Images + Text | 100 MB | CC-BY |

**Subtotal Food/Water:** ~560 MB raw

### 2.4 Agriculture & Gardening

| Source | Type | Size (est.) | License |
|--------|------|-------------|---------|
| Square Foot Gardening | EPUB | 20 MB | Owner-provided |
| Seed Saving Manual | PDF | 15 MB | Open source |
| Composting Guide | PDF | 10 MB | CC-BY |
| Permaculture Design Manual | EPUB | 50 MB | Owner-provided |
| USDA Plant Hardiness Data | Database | 30 MB | Public domain |
| Livestock Care Guides | PDF | 100 MB | Various |
| Soil Management | PDF | 25 MB | Public domain |
| Pest & Disease Control (organic) | PDF | 40 MB | CC-BY |

**Subtotal Agriculture:** ~290 MB raw

### 2.5 Mechanical & Repair

| Source | Type | Size (est.) | License |
|--------|------|-------------|---------|
| Basic Mechanics Handbook | PDF | 50 MB | Public domain |
| Small Engine Repair | PDF | 40 MB | Various |
| Bicycle Repair Manual | EPUB | 30 MB | CC-BY |
| Welding & Metalwork Basics | PDF | 25 MB | Open source |
| Woodworking Fundamentals | PDF | 60 MB | Various |
| Tool Maintenance Guides | Custom | 20 MB | Created |
| Plumbing Basics | PDF | 35 MB | Various |

**Note:** iFixit content is listed in §2.1 Primary Reference Sources (5 GB ZIM extraction). Not duplicated here.

**Subtotal Mechanical:** ~260 MB (excluding iFixit)

### 2.6 Electrical & Power

| Source | Type | Size (est.) | License |
|--------|------|-------------|---------|
| Solar Power Installation | PDF | 30 MB | CC-BY |
| Basic Electrical Wiring (NEC-based) | PDF | 40 MB | Various |
| Battery Systems Guide | PDF | 20 MB | Open source |
| Radio/Communication Basics | PDF | 35 MB | Public domain |
| Generator Maintenance | PDF | 15 MB | Various |
| Electronics Fundamentals | PDF | 50 MB | CC-BY |

**Subtotal Electrical:** ~190 MB raw

### 2.7 Practical How-To

| Source | Type | Size (est.) | License |
|--------|------|-------------|---------|
| **WikiHow (extracted)** | ZIM→RAG | 10 GB | CC-BY-NC-SA |

Categories prioritized:
- Home repair and maintenance
- Survival and camping
- First aid and health
- Cooking and food preparation
- Gardening
- Vehicle maintenance
- Crafts and DIY
- Emergency preparedness

**Subtotal How-To:** ~10 GB

### 2.8 Educational Reference

| Source | Type | Size (est.) | License |
|--------|------|-------------|---------|
| **OpenStax Full Set** | ZIM/PDF | 3 GB | CC-BY |
| - Biology | | 400 MB | |
| - Chemistry | | 350 MB | |
| - Physics | | 400 MB | |
| - Anatomy & Physiology | | 500 MB | |
| - Algebra & Trigonometry | | 300 MB | |
| - Calculus | | 400 MB | |
| - Psychology | | 300 MB | |
| - Economics | | 350 MB | |
| **Wikipedia (full)** | ZIM→RAG | 95 GB | CC-BY-SA |

**Subtotal Educational:** ~98 GB

### 2.9 Storage Summary

| Category | Raw Content | Notes |
|----------|-------------|-------|
| Wikipedia (full + images) | 95 GB | ~6.7M articles with diagrams |
| iFixit | 5 GB | Full repair guide collection |
| WikiHow | 10 GB | Practical articles |
| OpenStax | 3 GB | Complete textbook set |
| Medical | 180 MB | Handbooks, guidelines |
| Food/Water | 560 MB | Preservation, foraging |
| Agriculture | 290 MB | Growing, livestock |
| Mechanical | 260 MB | Non-iFixit manuals |
| Electrical | 190 MB | Power, wiring, radio |
| **Raw Content Total** | **~115 GB** | Before chunking/embedding |

---

## 3. Preprocessing Pipeline

### 3.1 Pipeline Overview

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Source    │───►│   Extract   │───►│   Clean     │───►│   Chunk     │
│   Files     │    │   Content   │    │   Text      │    │   Content   │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
       │                  │                                     │
       │           ┌──────┴──────┐                              │
       │           │             │                              │
       ▼           ▼             ▼                              ▼
   ┌───────┐  ┌───────┐    ┌───────┐                     ┌─────────────┐
   │  ZIM  │  │  PDF  │    │ EPUB  │                     │  Validate   │
   │Extract│  │Extract│    │Extract│                     │   Chunks    │
   └───────┘  └───────┘    └───────┘                     └─────────────┘
                                                                │
                                                                ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Build     │◄───│   Build     │◄───│   Generate  │◄───│   Extract   │
│   Index     │    │   Metadata  │    │  Embeddings │    │   Images    │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
```

### 3.2 ZIM Extraction

**Tool: python-libzim**

```python
from libzim.reader import Archive
from libzim.search import Query, Searcher

class ZIMExtractor:
    def __init__(self, zim_path: str, output_dir: str):
        self.archive = Archive(zim_path)
        self.output_dir = Path(output_dir)
    
    def extract_articles(self, categories: list[str] = None) -> list[Document]:
        """Extract articles, optionally filtering by category."""
        documents = []
        
        for entry in self.archive:
            if entry.is_redirect:
                continue
            
            if categories and not self.matches_category(entry, categories):
                continue
            
            content = entry.get_item().content.tobytes().decode('utf-8')
            
            # Parse HTML, extract text and images
            text, images = self.parse_html(content)
            
            doc = Document(
                id=f"zim_{self.archive.filename}_{entry.path}",
                title=entry.title,
                content=text,
                source=self.archive.filename,
                images=images,
                url=entry.path,
            )
            documents.append(doc)
        
        return documents
    
    def parse_html(self, html: str) -> tuple[str, list[Image]]:
        """Extract clean text and referenced images from HTML."""
        soup = BeautifulSoup(html, 'lxml')  # lxml ~10× faster than html.parser
        
        # Remove navigation, footers, etc.
        for tag in soup.find_all(['nav', 'footer', 'script', 'style']):
            tag.decompose()
        
        # Extract images with captions
        images = []
        for img in soup.find_all('img'):
            images.append(Image(
                src=img.get('src'),
                alt=img.get('alt', ''),
                caption=self.find_caption(img)
            ))
        
        # Get clean text
        text = soup.get_text(separator='\n', strip=True)
        
        return text, images
```

### 3.3 Text Extraction (Other Formats)

| Format | Tool | Notes |
|--------|------|-------|
| PDF | PyMuPDF (fitz) | Preserves structure, handles OCR |
| EPUB | ebooklib | Clean HTML extraction |
| DOCX | python-docx | Maintains formatting |
| HTML | BeautifulSoup + lxml | Table preservation, fast parsing |
| Markdown | Direct | Cleanest format |
| Images (scanned) | Tesseract OCR | For legacy documents |

### 3.4 Text Cleaning

```python
class TextCleaner:
    def clean(self, text: str, source_type: str) -> str:
        # 1. Normalize unicode
        text = unicodedata.normalize('NFKC', text)
        
        # 2. Fix common OCR errors (if applicable)
        if source_type == 'scanned':
            text = self.fix_ocr_errors(text)
        
        # 3. Normalize whitespace
        text = re.sub(r'\s+', ' ', text)
        text = re.sub(r'\n{3,}', '\n\n', text)
        
        # 4. Remove page artifacts (headers, footers, page numbers)
        text = self.remove_artifacts(text)
        
        # 5. Preserve meaningful structure (lists, tables)
        text = self.preserve_structure(text)
        
        # 6. Remove Wikipedia-specific cruft
        text = self.clean_wikipedia(text)
        
        return text.strip()
    
    def clean_wikipedia(self, text: str) -> str:
        """Remove Wikipedia-specific artifacts."""
        # Remove [edit] links, citation numbers, etc.
        text = re.sub(r'\[edit\]', '', text)
        text = re.sub(r'\[\d+\]', '', text)
        text = re.sub(r'\[citation needed\]', '', text)
        return text
```

### 3.5 Chunking Implementation

```python
class SemanticChunker:
    def __init__(
        self,
        max_tokens: int = 512,
        overlap_tokens: int = 64,
    ):
        self.tokenizer = AutoTokenizer.from_pretrained('bert-base-uncased')
        self.max_tokens = max_tokens
        self.overlap_tokens = overlap_tokens
    
    def chunk(self, doc: Document) -> list[Chunk]:
        # Determine chunk size based on content type
        if doc.source_type in ['ifixit', 'manual', 'howto']:
            max_tokens = 256  # Keep procedures intact
            overlap = 32
        elif doc.source_type == 'reference':
            max_tokens = 384
            overlap = 48
        else:
            max_tokens = self.max_tokens
            overlap = self.overlap_tokens
        
        # Split on semantic boundaries
        segments = self.split_semantic(doc.content)
        
        chunks = []
        current_chunk = []
        current_tokens = 0
        
        for segment in segments:
            segment_tokens = len(self.tokenizer.encode(segment))
            
            if current_tokens + segment_tokens > max_tokens:
                # Save current chunk
                chunks.append(self.create_chunk(current_chunk, doc))
                
                # Start new chunk with overlap
                overlap_text = self.get_overlap(current_chunk, overlap)
                current_chunk = [overlap_text, segment]
                current_tokens = len(self.tokenizer.encode(overlap_text)) + segment_tokens
            else:
                current_chunk.append(segment)
                current_tokens += segment_tokens
        
        # Save final chunk
        if current_chunk:
            chunks.append(self.create_chunk(current_chunk, doc))
        
        return chunks
    
    def split_semantic(self, text: str) -> list[str]:
        """Split on paragraph boundaries, headers, list items."""
        # Split on double newlines (paragraphs)
        # Also split on numbered/bulleted lists
        pattern = r'\n\n+|(?<=\.)\s+(?=[A-Z])|(?<=:)\n(?=\d\.|\*|\-)'
        return re.split(pattern, text)
```

### 3.6 Image Handling

```python
class ImageProcessor:
    """Process and store images referenced in documents."""
    
    MAX_IMAGE_SIZE = (1200, 1200)  # Max dimensions
    JPEG_QUALITY = 80
    
    def process_images(self, doc: Document, output_dir: Path) -> list[ImageRef]:
        """Extract, resize, and store images."""
        refs = []
        
        for img in doc.images:
            # Load image
            image_data = self.load_image(img.src, doc.source_path)
            if not image_data:
                continue
            
            # Resize if needed
            image = Image.open(io.BytesIO(image_data))
            if max(image.size) > max(self.MAX_IMAGE_SIZE):
                image.thumbnail(self.MAX_IMAGE_SIZE, Image.LANCZOS)
            
            # Save as JPEG
            output_path = output_dir / f"{doc.id}_{img.hash}.jpg"
            image.save(output_path, "JPEG", quality=self.JPEG_QUALITY)
            
            refs.append(ImageRef(
                doc_id=doc.id,
                path=str(output_path),
                alt=img.alt,
                caption=img.caption,
            ))
        
        return refs
```

### 3.7 Embedding Generation

```python
class EmbeddingGenerator:
    def __init__(self, model_name: str = 'all-MiniLM-L6-v2'):
        self.model = SentenceTransformer(model_name)
        self.batch_size = 128  # RK3588 can handle larger batches
    
    def generate_embeddings(self, chunks: list[Chunk]) -> np.ndarray:
        texts = [chunk.text for chunk in chunks]
        
        embeddings = self.model.encode(
            texts,
            batch_size=self.batch_size,
            show_progress_bar=True,
            convert_to_numpy=True,
            normalize_embeddings=True  # L2 normalize for cosine similarity
        )
        
        return embeddings.astype(np.float32)
```

### 3.8 Index Building

```python
class IndexBuilder:
    def build(
        self,
        embeddings: np.ndarray,
        chunks: list[Chunk],
        n_clusters: int = 2048
    ) -> faiss.Index:
        dim = embeddings.shape[1]  # 384 for MiniLM
        n_vectors = len(embeddings)
        
        # Choose index type based on size
        if n_vectors < 10000:
            # Small dataset: use flat index
            index = faiss.IndexFlatL2(dim)
        else:
            # Large dataset: use IVF
            quantizer = faiss.IndexFlatL2(dim)
            index = faiss.IndexIVFFlat(quantizer, dim, n_clusters)
            
            # Train on subset if very large
            if n_vectors > 100000:
                train_sample = embeddings[np.random.choice(
                    n_vectors, 100000, replace=False
                )]
                index.train(train_sample)
            else:
                index.train(embeddings)
        
        # Add all vectors
        index.add(embeddings)
        
        return index
    
    def save_index(self, index: faiss.Index, path: str):
        """Save index with memory-mapping support."""
        faiss.write_index(index, path)
```

---

## 4. Storage Budget

### 4.1 Detailed Allocation

| Component | Size | Notes |
|-----------|------|-------|
| **Raw Content** | | |
| Wikipedia (full + images) | 95 GB | ~6.7M articles |
| iFixit | 5 GB | Full repair guides |
| WikiHow | 10 GB | Practical how-to |
| OpenStax | 3 GB | Full textbook set |
| Medical sources | 180 MB | Handbooks, guidelines |
| Food/Water sources | 560 MB | Preservation, foraging |
| Agriculture sources | 290 MB | Growing, livestock |
| Mechanical sources | 260 MB | Non-iFixit manuals |
| Electrical sources | 190 MB | Power, wiring |
| **Raw subtotal** | **~115 GB** | |
| | | |
| **Processed Data** | | |
| Chunked text (deduplicated) | 25 GB | ~8M chunks × ~3KB avg (UTF-8 text only) |
| Embeddings (384-dim float32) | 12 GB | 8M × 384 × 4 bytes = 1.5KB/chunk |
| FAISS index (on disk) | 14 GB | IVFFlat, 4096 clusters, memory-mapped |
| Metadata database | 4 GB | SQLite, chunk→doc mapping |
| Image store (deduplicated) | 35 GB | From PDF/EPUB only; ZIM images stay in-place |
| **Processed subtotal** | **~90 GB** | |
| | | |
| **Total Library** | **~205 GB** | Raw + Processed (images not double-counted) |
| **With 10% headroom** | **~225 GB** | |

**Image deduplication note:** Wikipedia, iFixit, and WikiHow ZIM files include images in their raw sizes (~60GB of the 115GB). The "Image store" contains only: (1) images from PDF/EPUB sources, (2) reprocessed images needing format conversion, and (3) custom-created diagrams. This avoids double-counting ZIM-embedded images.

### 4.2 Fits Within Hardware Budget

**This section is the authoritative source for library sizing.** Hardware Spec v2.1.0 and Software Spec v2.2.0 reference these numbers.

Hardware Spec v2.1.0 allocates:
- /data partition: 404 GB
- Library allocation: 230 GB (210 GB + 10% headroom)
- User data: 100 GB (history, imports)
- Expansion reserve: 74 GB

**Library usage: 230 GB / 404 GB = 57%**

Room for ~74 GB additional content in future updates.

### 4.3 Memory-Mapped Index Strategy

With full Wikipedia (~8M chunks), the FAISS IVFFlat index is ~14 GB on disk. To avoid consuming all RAM:

| Strategy | Disk | RAM (active) | Latency | Accuracy |
|----------|------|--------------|---------|----------|
| Full in-memory | 14 GB | 14 GB | ~100ms | 97%+ |
| **Memory-mapped (selected)** | 14 GB | ~2-4 GB | ~150ms | 97%+ |
| Scalar quantization (SQ8) | 4 GB | 4 GB | ~120ms | 95% |

**Memory-mapped implementation:**
```python
# Load index with mmap for reduced RAM footprint
index = faiss.read_index("/data/library/index/vectors.index", faiss.IO_FLAG_MMAP)
index.nprobe = 64  # Increase nprobe to maintain accuracy with more clusters
```

NVMe random read latency (~50μs) adds minimal overhead. Working set (~2-4 GB) kept in page cache during active use.

### 4.4 Memory Budget During RAG Operations

Parallel memory usage when processing a complex query with voice input:

| Component | Peak RAM | Duration | Notes |
|-----------|----------|----------|-------|
| Whisper.cpp (base.en) | ~500 MB | ~8s | Voice transcription |
| Embedding model | ~400 MB | ~200ms | Query vectorization |
| FAISS index (active pages) | ~2-4 GB | ~150ms | Memory-mapped, not fully loaded |
| LLM context loading | ~14 GB | ~2s | RKLLM model weights |
| LLM inference | ~1 GB additional | ~10-15s | KV cache during generation |
| TTS synthesis | ~200 MB | ~5s | Piper model (if read-aloud) |
| **Peak concurrent** | **~16-18 GB** | - | Whisper unloads before LLM loads |
| **Steady state** | **~15 GB** | - | LLM + FAISS pages + app overhead |

**Note:** Components load sequentially, not simultaneously. Whisper completes and unloads before LLM inference begins. The 32GB system RAM provides ~14GB headroom for OS, page cache, and unexpected spikes.

### 4.5 Compression Strategy

| Content Type | Format | Compression |
|--------------|--------|-------------|
| Text chunks | UTF-8 plain | None (fast access) |
| Embeddings | float32 binary | None (FAISS requirement) |
| FAISS index | Native | Internal compression |
| Images | JPEG | Quality 80 (~10:1) |
| Metadata DB | SQLite | Page compression |

---

## 5. Import Format

### 5.1 Document Schema

```python
@dataclass
class ImportDocument:
    # Required fields
    id: str                     # Unique identifier (source_type_hash)
    title: str                  # Document title
    content: str                # Full text content
    source_type: SourceType     # 'wikipedia', 'ifixit', 'wikihow', 'pdf', etc.
    domain: Domain              # 'medical', 'agriculture', 'mechanical', etc.
    
    # Source tracking
    source_file: str            # Original filename or ZIM entry path
    source_url: Optional[str]   # Original URL if applicable
    
    # Optional metadata
    author: Optional[str] = None
    publication_date: Optional[date] = None
    license: Optional[str] = None
    
    # Images
    images: list[ImageRef] = field(default_factory=list)
    
    # Processing hints
    chunk_strategy: ChunkStrategy = ChunkStrategy.SEMANTIC
    priority: int = 5           # 1 (highest) to 10 (lowest)
    
    # Validation
    checksum: str = ""          # SHA-256 of content
    processed_at: datetime = field(default_factory=datetime.utcnow)

class SourceType(Enum):
    WIKIPEDIA = "wikipedia"
    IFIXIT = "ifixit"
    WIKIHOW = "wikihow"
    OPENSTAX = "openstax"
    PDF = "pdf"
    EPUB = "epub"
    MANUAL = "manual"
    CUSTOM = "custom"

class Domain(Enum):
    MEDICAL = "medical"
    FOOD_WATER = "food_water"
    AGRICULTURE = "agriculture"
    MECHANICAL = "mechanical"
    ELECTRICAL = "electrical"
    SHELTER = "shelter"
    NAVIGATION = "navigation"
    COMMUNICATION = "communication"
    SCIENCE = "science"
    EDUCATION = "education"
    REFERENCE = "reference"
```

### 5.2 Chunk Schema

```python
@dataclass
class Chunk:
    id: str                     # chunk_{doc_id}_{index}
    document_id: str            # Parent document
    text: str                   # Chunk content (≤512 tokens)
    
    # Location within document
    start_char: int
    end_char: int
    section_title: Optional[str] = None
    
    # Embedding (populated during processing)
    embedding: Optional[np.ndarray] = None  # 384-dim float32
    
    # Metadata inheritance
    domain: Domain
    source_type: SourceType
    priority: int
    
    # Image references (if chunk contains image captions)
    image_refs: list[str] = field(default_factory=list)
```

### 5.3 Metadata Index Schema

```python
@dataclass
class MetadataIndex:
    # Document registry
    documents: dict[str, DocumentMeta]  # id → metadata
    
    # Chunk mappings
    chunks_by_document: dict[str, list[str]]  # doc_id → chunk_ids
    chunks_by_domain: dict[Domain, list[str]]  # domain → chunk_ids
    
    # Image registry
    images: dict[str, ImageMeta]  # image_id → metadata
    images_by_document: dict[str, list[str]]  # doc_id → image_ids
    
    # Statistics
    total_documents: int
    total_chunks: int
    total_tokens: int
    total_images: int
    domains: dict[Domain, DomainStats]
    sources: dict[SourceType, SourceStats]
    
    # Build info
    build_date: datetime
    build_version: str
```

### 5.4 Validation Rules

```python
class ImportValidator:
    MIN_CONTENT_LENGTH = 50     # Minimum chars
    MAX_CONTENT_LENGTH = 10_000_000  # 10 MB
    MAX_TITLE_LENGTH = 500
    
    REQUIRED_FIELDS = ['id', 'title', 'content', 'source_type', 'domain']
    VALID_DOMAINS = set(Domain)
    VALID_SOURCE_TYPES = set(SourceType)
    
    def validate(self, doc: ImportDocument) -> ValidationResult:
        errors = []
        warnings = []
        
        # Check required fields
        for field in self.REQUIRED_FIELDS:
            if not getattr(doc, field):
                errors.append(f"Missing required field: {field}")
        
        # Check content length
        if len(doc.content) < self.MIN_CONTENT_LENGTH:
            errors.append(f"Content too short: {len(doc.content)} chars")
        if len(doc.content) > self.MAX_CONTENT_LENGTH:
            errors.append(f"Content too long: {len(doc.content)} chars")
        
        # Check title length
        if len(doc.title) > self.MAX_TITLE_LENGTH:
            warnings.append(f"Title truncated from {len(doc.title)} chars")
        
        # Check domain validity
        if doc.domain not in self.VALID_DOMAINS:
            errors.append(f"Invalid domain: {doc.domain}")
        
        # Check source type
        if doc.source_type not in self.VALID_SOURCE_TYPES:
            errors.append(f"Invalid source type: {doc.source_type}")
        
        # Check for near-duplicates
        if self.is_duplicate(doc):
            warnings.append("Possible duplicate content detected")
        
        # Verify checksum if provided
        if doc.checksum:
            computed = hashlib.sha256(doc.content.encode()).hexdigest()
            if computed != doc.checksum:
                errors.append("Checksum mismatch")
        
        return ValidationResult(
            valid=len(errors) == 0,
            errors=errors,
            warnings=warnings
        )
```

---

## 6. Update Procedures

### 6.1 Adding New Content

```bash
# 1. Prepare document(s) in import format
ark-prepare --input /path/to/document.pdf \
            --output /staging/document.json \
            --domain medical \
            --source-type pdf

# 2. Validate import
ark-validate /staging/document.json
# Output: ✓ Valid, 0 errors, 1 warning

# 3. Process (chunk, embed, index)
ark-import /staging/document.json
# Output: Processed 1 document, 47 chunks, 0 images

# 4. Verify integration
ark-search "test query from new document"
# Should return results from new content
```

### 6.2 Importing ZIM Files

```bash
# 1. Extract from ZIM with category filter
ark-zim-extract --input wikipedia_en.zim \
                --output /staging/wikipedia/ \
                --categories medical,agriculture,mechanical \
                --include-images

# 2. Validate extraction
ark-validate /staging/wikipedia/*.json
# Output: ✓ 15,234 documents valid

# 3. Batch import
ark-import --batch /staging/wikipedia/ \
           --parallel 4

# 4. Rebuild index
ark-reindex
```

### 6.3 Modifying Existing Content

```bash
# 1. Export current version
ark-export --document-id <id> --output /backup/

# 2. Make modifications
# (edit JSON file)

# 3. Re-import with update flag
ark-import --update /modified/document.json

# 4. Rebuild affected index segments
ark-reindex --document-id <id>
```

**Atomic Import Rollback:**

If import fails mid-operation (power loss, storage error, validation failure), the system automatically rolls back:

```bash
# Import creates staging area first
/data/library/.staging/<import-id>/
  ├── chunks.json       # New chunks pending commit
  ├── embeddings.bin    # New vectors pending commit  
  └── manifest.json     # Import metadata + checksum

# Only after validation passes:
mv .staging/<import-id>/* /data/library/
rm -rf .staging/<import-id>/

# If interrupted, on next boot:
ark-recover --check-staging
# Detects incomplete imports, removes staging dirs
# Original data untouched
```

**Rollback triggers:**
- Checksum mismatch in any file
- Embedding dimension mismatch (must be 384)
- Duplicate document ID without --update flag
- Storage write failure
- Explicit CTRL+C during import

### 6.4 Removing Content

```bash
# 1. Identify document
ark-search "title:example_document"

# 2. Remove from database and index
ark-remove --document-id <id>

# 3. Compact storage (optional, reclaims space)
ark-compact
```

### 6.5 Full Rebuild

```bash
# Complete rebuild from source files
# WARNING: Takes several hours

ark-rebuild --sources /data/sources/ \
            --output /data/library/ \
            --parallel 8

# Generates:
# - /data/library/chunks/     (text chunks)
# - /data/library/embeddings/ (vector data)
# - /data/library/index/      (FAISS index)
# - /data/library/images/     (processed images)
# - /data/library/metadata.db (SQLite)
```

### 6.6 Versioning

| Component | Version Strategy |
|-----------|-----------------|
| Individual documents | SHA-256 content hash |
| Chunk database | Incremental rowid |
| FAISS index | Build timestamp + count |
| Full library | Semantic version (2.0.0) |
| PAR2 parity | Regenerated on any change |

### 6.7 Offline Update Considerations

The ark-* CLI tools (ark-import, ark-validate, etc.) assume a Python environment with dependencies installed. For field updates on the device itself:

| Scenario | Method | Requirements |
|----------|--------|-------------|
| **Pre-built content pack** | Copy .ark-pack to Import USB, run ark-install-pack | Minimal: ark-core only |
| **New PDF/EPUB** | Copy to Import USB, run ark-quick-import | ark-core + sentence-transformers |
| **Full ZIM rebuild** | Not supported on-device | Desktop build system required |
| **Index repair** | ark-reindex --repair | ark-core only |

**Import USB workflow (§6.1):**
1. Prepare content on desktop with full toolchain
2. Package as `.ark-pack` (pre-chunked, pre-embedded, includes delta index)
3. Copy to FAT32 USB drive
4. Insert USB, device auto-detects and runs ark-install-pack
5. No Python dependencies required for pack installation

**Pre-packaged update format (.ark-pack):**
```
update-2025-12.ark-pack/
├── manifest.json       # Version, checksums, dependencies
├── chunks/            # Pre-chunked text
├── embeddings.npy     # Pre-computed 384-dim vectors  
├── index-delta.faiss  # Merge-ready index segment
├── images/            # Processed images
└── metadata.sql       # SQLite delta
```

This enables field updates without requiring the full sentence-transformers or FAISS build toolchain on the device.

### 6.8 Integrity Checks

```python
class IntegrityChecker:
    async def full_check(self) -> IntegrityReport:
        """Run complete integrity verification."""
        return IntegrityReport(
            documents=await self.verify_documents(),
            chunks=await self.verify_chunks(),
            embeddings=await self.verify_embeddings(),
            index=await self.verify_index(),
            images=await self.verify_images(),
            par2=await self.verify_par2(),
        )
    
    async def verify_documents(self) -> list[Issue]:
        """Verify all documents match their checksums."""
        issues = []
        for doc in self.document_store.all():
            computed = hashlib.sha256(doc.content.encode()).hexdigest()
            if computed != doc.checksum:
                issues.append(Issue(
                    severity="error",
                    component="document",
                    id=doc.id,
                    message=f"Checksum mismatch: {computed} != {doc.checksum}"
                ))
        return issues
    
    async def verify_embeddings(self) -> list[Issue]:
        """Verify embeddings are properly normalized."""
        issues = []
        for chunk_id, embedding in self.embedding_store.iterate():
            norm = np.linalg.norm(embedding)
            if not np.isclose(norm, 1.0, atol=0.01):
                issues.append(Issue(
                    severity="warning",
                    component="embedding",
                    id=chunk_id,
                    message=f"Not normalized: ||v|| = {norm}"
                ))
        return issues
    
    async def verify_par2(self) -> list[Issue]:
        """Verify PAR2 parity files are valid."""
        result = subprocess.run(
            ["par2", "verify", "/recovery/parity/library.par2"],
            capture_output=True
        )
        if result.returncode != 0:
            return [Issue(
                severity="error",
                component="par2",
                message="PAR2 verification failed"
            )]
        return []
```

---

## 7. Version History

| Version | Date | Summary |
|---------|------|---------|
| 1.0.0 | Dec 2025 | Initial specification (2TB RAID, generic sources) |
| 2.0.0 | Dec 2025 | Complete rewrite: 250GB budget, ZIM extraction via python-libzim, selective Wikipedia, iFixit, WikiHow, OpenStax, unified RAG pipeline, PAR2 protection |
| 2.1.0 | Dec 2025 | CRIT-002: Marked §4.2 as authoritative source for library sizing. Updated cross-references to Hardware Spec v2.1.0 (404GB /data partition). Library 167GB of 404GB (41%). |
| 2.2.0 | Dec 2025 | CRIT-008: Added Content Acquisition Model (§2) clarifying open-license, public-domain, and owner-provided categories. Changed "Purchased" → "Owner-provided" for 5 copyrighted sources. Personal-use project; owner loads their own legally-acquired content. |
| 2.3.0 | Dec 2025 | CRIT-015: Include full Wikipedia (~95GB, 6.7M articles) instead of selective subset. Eliminates category-based selection criteria. Added §4.3 memory-mapped index strategy for 8M+ chunks. Revised storage: 230GB library (57% of /data). FAISS IVFFlat with 4096 clusters, nprobe=64. |
| **2.4.0** | **Dec 2025** | **Batch 4 cleanup (MED-044→MED-050): Added CC-BY-NC-SA clarification to §1.2 (personal-use permits NC content). Fixed iFixit duplication (§2.5 now references §2.1). Changed BeautifulSoup parser to lxml (~10× faster). Corrected §4.1 chunk size math (25GB text at ~3KB/chunk, not 80GB). Clarified image deduplication (ZIM images not double-counted). Added §6.7 Offline Update Considerations with .ark-pack format for field updates without full toolchain.** |
| 2.5.0 | Dec 2025 | LOW issues batch: §1.3 Added Shelter/Security domain note (construction + situational security, non-violent emphasis). §4.4 Added parallel memory table showing component RAM usage during RAG operations. §6.3 Added atomic import rollback mechanism with staging directory and recovery procedure. |

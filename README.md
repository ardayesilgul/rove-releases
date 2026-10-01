<div align="center">

# ROVE

### High-Performance On-Device Machine Perception & Neural Indexing Engine

[![Stable Build](https://img.shields.io/badge/RELEASE-v1.0.11%20STABLE-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases/releases/tag/v1.0.11)
[![Platform](https://img.shields.io/badge/PLATFORM-WINDOWS%2010%20%2F%2011%20x64-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases/releases/tag/v1.0.11)
[![Architecture](https://img.shields.io/badge/PIPELINE-512--D%20CENTROID%20%2F%20FTS5-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases)
[![Telemetry](https://img.shields.io/badge/TELEMETRY-0%25%20AIR--GAPPED-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases)

<br/>

[**DOWNLOAD ROVE v1.0.11 (.EXE)**](https://github.com/ardayesilgul/rove-releases/releases/download/v1.0.11/Rove-Setup-1.0.11.exe)

</div>

---

## Technical Overview

Rove is an air-gapped machine perception layer for Windows engineered to index, organize, and retrieve massive local media libraries in real time. 

Built from the ground up to eliminate cloud dependencies, Rove executes deep biometric vectorization, dynamic centroid identity learning, sub-millisecond full-text tokenization, and real-time audio telemetry locally on host silicon with zero external API calls.

---

## Core Engineering Pillars

```
+-------------------------------------------------------------------------+
|                        MAIN GUI THREAD (60 FPS VSYNC)                   |
|   Seamless DWM Integration | OutBack Spring Kinematics | Zero Detached   |
+-------------------------------------------------------------------------+
                                    ^
                                    | Qt Event Bus (Thread-Safe Signals)
                                    v
+--------------------+--------------------+--------------------+----------+
|  NEURAL BIOMETRICS | FULL-TEXT INDEXER  | HARDWARE TELEMETRY | SHELF    |
|  512-D Centroid    | SQLite FTS5 / BM25 | WASAPI Core Audio  | WinRT    |
|  MTCNN + ResNet    | Sub-5ms Search     | 40Hz Peak Polling  | Local    |
+--------------------+--------------------+--------------------+----------+
```

---

### `[PILLAR_01: PROPRIETARY BIOMETRIC MANIFOLD & CENTROID CONVERGENCE]`

Rather than relying on basic landmark libraries or static matching thresholds, Rove implements an adaptive multi-stage biometric pipeline designed for unconstrained, multi-decade photo libraries:

* **Affine Kerteriz Normalization:** Multi-stage cascaded neural detection isolates face regions, correcting in-plane tilt and pitch angles to generate standardized $160 \times 160$ aligned biometric crops.
* **512-Dimensional Hyper-Sphere Mapping:** Deep residual embedding projects each face into an L2-normalized 512-D continuous vector space ($||v||_2 = 1.0$), capturing invariant facial geometries.
* **Adaptive Dynamic Centroid Reinforcement:** Identities are modeled as living cluster centers rather than static reference points. As new photos under diverse lighting, aging, and facial hair are verified, the identity centroid dynamically converges toward the true geometric center:
$$\mathbf{C}_{\text{new}} = \text{Normalize}\left( \frac{\mathbf{C}_{\text{old}} \cdot N + \mathbf{V}_{\text{new}}}{N + 1} \right)$$
* **Triple-Tier Decision Boundaries:**
  * **Tier 1 ($\ge 0.65$):** Instant autonomous association into existing identity clusters.
  * **Tier 2 ($0.50 - 0.65$):** Ambiguity resolution queue with active cluster proposals.
  * **Tier 3 ($< 0.50$):** Automated branch generation for unidentified clusters.
* **Negative Association & Blacklist Isolation:** Explicit false-match rejections immediately prune aberrant vectors from the centroid and write hash-signatures to local isolation tables (`ignored_faces`), permanently neutralizing recurring misclassifications.
* **Dynamic Chunk Virtualization:** Handles libraries with 100,000+ faces without UI stutter by streaming 48-item rendering batches based on vertical scroll geometry.

---

### `[PILLAR_02: SUB-5MS FULL-TEXT RETRIEVAL & BM25 RANKING]`

A high-throughput local document indexing engine operating directly against raw storage:

* **Native Document Ingestion:** Headless parsers extract structured textual tokens from PDF, DOCX, XLSX, and TXT files, alongside high-precision EXIF metadata from raw photo formats.
* **Deterministic FTS5 Indexing:** Tokenized streams are ingested into SQLite FTS5 virtual tables with Porter stemming and unicode61 diacritic normalization under Write-Ahead Logging (WAL).
* **BM25 Relevance Scoring:** Queries evaluate term saturation ($k_1 = 1.2$) and document length normalization ($b = 0.75$) with heavy title-frequency amplification ($3.5\times$), executing prefix queries in under 5 milliseconds across tens of thousands of documents.

---

### `[PILLAR_03: ASYNCHRONOUS ARCHITECTURE & 60 FPS ISOLATION]`

* **Strict Thread Isolation:** Heavy tensor operations, disk indexing, and audio telemetry run strictly within asynchronous QThread pools. The Main GUI Thread remains entirely decoupled from I/O, guaranteeing zero "Not Responding" stalls under heavy CPU loads.
* **Thread-Safe Event Bus:** Inter-thread communication is strictly arbitrated via serialized Qt signals/slots, preventing shared memory race conditions or SQLite database locks.

---

### `[PILLAR_04: AMBIENT HARDWARE INTEGRATION & DYNAMIC NOTCH]`

A compact, hardware-aware desktop surface that presents system state without detached popups:

* **WASAPI Core Audio Telemetry:** Directly polls `IAudioMeterInformation` at 40 Hz to sample real electrical speaker amplitude `[0.0, 1.0]`. When audio halts, the 4-band harmonic sine equalizer decays to baseline with zero synthetic noise.
* **Desktop Clearance Engine:** Seamlessly interacts with the Windows window manager via low-level hooks (`WH_MOUSE_LL`). Automatically tucks 5px upward when browser tabs near the top border, and invokes `SW_HIDE` during full-screen applications.
* **Hardware-Level DWM Styling:** Uses native Windows Desktop Window Manager composition (`DwmSetWindowAttribute`) to render seamless `#000000` pitch-black title bars and true borderless surfaces.

---

## Performance & Security Matrix

| Metric / Specification | Traditional Tagging / Cloud Indexers | Rove Local Perception Engine |
| :--- | :--- | :--- |
| **Data Privacy** | Cloud uploads / External APIs | **100% Air-Gapped (Local Host Only)** |
| **Biometric Clustering** | Static one-to-one image matching | **Adaptive Dynamic Centroid Learning** |
| **Search Query Latency** | 250ms - 1500ms (Network dependent) | **< 5ms (Local FTS5 + BM25)** |
| **Memory Footprint** | 800 MB - 2.5 GB (Electron/Web wrappers)| **~158 MB RSS (Native Compiled Binary)** |
| **UI Responsiveness** | Synchronous rendering freezes | **Strict Asynchronous 60 FPS VSync** |
| **Telemetry & Tracking** | Active analytical telemetry | **0% Network Telemetry (Zero Callbacks)** |

---

## Release Artifacts & Verification

Every build is packaged via Inno Setup and signed with an immutable SHA-256 digest.

### Stable Distribution: v1.0.11
- **File:** `Rove-Setup-1.0.11.exe`
- **Payload Size:** ~128 MB
- **SHA-256 Digest:**
  ```text
  bcaa8cf34e0b4473addac48fda466dbf7ce6958697debd982d0fd64b535da702
  ```

#### Integrity Check (PowerShell)
```powershell
Get-FileHash -Path "Rove-Setup-1.0.11.exe" -Algorithm SHA256
```

---

## Issue Tracking

For defect reports, architecture discussions, and feature proposals, submit an issue to the [Issue Tracker](https://github.com/ardayesilgul/rove-releases/issues).

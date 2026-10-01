<div align="center">

# ROVE // DESKTOP INTELLIGENCE

**Local-first, air-gapped desktop productivity layer with Dynamic Island, neural biometric clustering, and instant system-wide retrieval.**

[![Version](https://img.shields.io/badge/version-1.0.11-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases/releases/tag/v1.0.11)
[![Platform](https://img.shields.io/badge/platform-Windows%2010%20%2F%2011%20(x64)-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases/releases/tag/v1.0.11)
[![License](https://img.shields.io/badge/license-Proprietary-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases)
[![Security](https://img.shields.io/badge/telemetry-0%25%20(Local%20Only)-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases)

<br/>

[**Download Rove v1.0.11 for Windows (.exe)**](https://github.com/ardayesilgul/rove-releases/releases/download/v1.0.11/Rove-Setup-1.0.11.exe)

</div>

---

## Overview

Rove is an operating-system layer engineered for Windows that unifies ambient hardware controls, instant local file retrieval, and on-device biometric media organization into an organic, non-intrusive Dynamic Island.

Designed around strict privacy engineering: zero cloud dependencies, zero external network telemetry, and 100% local hardware acceleration.

---

## Core Capabilities

### `[MODULE_01: DYNAMIC_ISLAND]`
- **Fluid Kinematics:** Expands and collapses organically via OutBack cubic spring physics ($s = 1.70158$, 60 FPS VSync loop).
- **Environment Clearance:** Automatically tucks 5px upward when Google Chrome or Microsoft Edge tabs are active to preserve tab closure bounds; hides instantly (`SW_HIDE`) during full-screen video playback and gaming.
- **Integrated Toolkits:**
  - **Daily Kit:** Clock, weather overview, and persistent scratchpad.
  - **Productivity Kit:** Minimalist Pomodoro cycle timer and task checklist.
  - **Core Hub:** Quick access to active media telemetry, staging shelf, and system search.

### `[MODULE_02: FACE_AI_PIPELINE]`
- **Local Biometric Architecture:** On-device neural pipeline powered by MTCNN alignment and InceptionResnetV1 (512-dimensional vector embedding).
- **Centroid Profile Learning:** Automatically updates centroid identity representations as new verified angles, lighting conditions, and expressions are confirmed.
- **Negative Association & Blacklist Isolation:** Explicit false detections are written to local isolation tables, preventing recurring false proposals. Complete blacklist resets can be executed safely via in-app preferences.
- **Infinite Grid Navigation:** Dynamic chunking loads large photo libraries in smooth 48-item segments, completely bypassing UI thread latency.

### `[MODULE_03: SPOTLIGHT_SEARCH]`
- **Sub-5ms Query Retrieval:** Integrated with SQLite FTS5 (Full-Text Search) and BM25 relevance ranking.
- **Universal Indexer:** Instant deep parsing across PDF, DOCX, XLSX, TXT, and EXIF metadata without external services.
- **Zero Detached Popups:** Search results render directly within the native island body and dismiss automatically on external mouse interaction (`WH_MOUSE_LL`).

### `[MODULE_04: HARDWARE_TELEMETRY]`
- **Zero-Latency WASAPI Polling:** Real-time peak amplitude extraction via `IAudioMeterInformation` (40 Hz cycle).
- **Harmonic Sinusoidal Synthesizer:** 4-band real-time audio waveform visualizer that decays directly to baseline when playback ceases.
- **System OSD:** Seamless replacement for legacy Windows volume and brightness flyouts with hardware-level DWM presentation.

---

## Privacy & Security

| Vector | Specification |
| :--- | :--- |
| **Telemetry** | Zero outbound analytics, tracking, or network calls. |
| **Biometric Vectors** | 100% computed on local CPU/GPU; never uploaded. |
| **Database** | SQLite WAL (Write-Ahead Log) stored locally in `%LOCALAPPDATA%`. |
| **Network Footprint** | Offline-first. Only checks `version.json` over HTTPS for updates upon explicit user launch. |

---

## System Requirements

- **Operating System:** Windows 10 (64-bit) build 19041+ or Windows 11 (64-bit).
- **Memory:** 4 GB RAM minimum (8 GB recommended for large photo indexing).
- **Storage:** 400 MB disk space for executable runtime and neural weights.
- **Display:** 1280x720 minimum resolution with DWM composition active.

---

## Verification & Integrity

Every official release binary is packaged with Inno Setup and signed with an immutable SHA-256 digest.

### Current Stable Build (v1.0.11)
- **Installer:** `Rove-Setup-1.0.11.exe`
- **File Size:** ~128 MB
- **SHA-256 Checksum:**
  ```text
  bcaa8cf34e0b4473addac48fda466dbf7ce6958697debd982d0fd64b535da702
  ```

#### Verify via PowerShell
```powershell
Get-FileHash -Path "Rove-Setup-1.0.11.exe" -Algorithm SHA256
```

---

## Bug Reports & Feature Requests

Encountered an issue or wish to request an enhancement? Submit a ticket via [GitHub Issues](https://github.com/ardayesilgul/rove-releases/issues).

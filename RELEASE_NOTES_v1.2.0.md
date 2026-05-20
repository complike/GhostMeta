GhostMeta v1.2.0 — Release Notes
Release date: 2026‑05‑20
Author: Bülent Deniz
License: MIT (GhostMeta), Proprietary (GhostEngine)

Overview
GhostMeta v1.2.0 introduces a fully stable release of the structural JPEG metadata removal engine powered by GhostEngine v40 (4096‑bit ARM‑NEON).
This version focuses on reliability, forensic transparency, and zero‑dependency operation on Apple Silicon.

GhostMeta removes all metadata from JPEG files without re‑encoding, ensuring zero quality loss and forensically verifiable output.

Key Features
irreversible removal of JPEG metadata (EXIF, XMP, IPTC, C2PA/JUMBF, ICC, COM, Post‑EOI)

structural rebuild of the JPEG container without modifying the pixel stream

machine‑readable forensic report (JSON)

human‑readable forensic report (TXT)

audit log with SHA‑256 hashes (original, clean, meta‑blob)

internal cleanliness check (structural consistency + pixel preservation)

zero external dependencies (no Python, no Perl, no libraries)

Apple Silicon optimized (M2/M3/M4 Pro/Max)

Performance
GhostEngine v40 processes metadata at multi‑GB/s throughput:

50 MB JPEG → < 0.2 s

100 MB JPEG → < 0.5 s

200 MB JPEG → < 1.0 s

Constant memory usage, no latency spikes, no re‑encoding.

Platform Support
Fully supported:

macOS 14+

M2 Pro/Max, M3 Pro/Max, M4 Pro/Max

Limited support:

M1 series (reduced NEON throughput)

Not supported:

Intel Macs

Windows, Linux

Rosetta, Docker, WSL, QEMU

Build & Usage
Build:

Code
make
Run:

Code
./ghostmeta --in input.jpg --out clean.jpg
Output files:

clean.jpg

input.jpg.forensic.json

input.jpg.forensic.txt

input.jpg.audit.json

Changes Since v1.1.x
improved C2PA/JUMBF detection and removal

enhanced forensic report structure

faster metadata scanning pipeline

improved Post‑EOI detection

updated internal consistency checks

refined error codes and CLI messages

updated documentation (EN/DE)

new dual‑license model (MIT + proprietary engine)

Known Limitations
JPEG only (PNG, HEIC, PDF planned)

Apple Silicon only

no steganography detection

no GUI (optional macOS wrapper available)

Licensing
GhostMeta source code → MIT License

GhostEngine v40 binary → Proprietary License (not included)

See:

MIT License

Proprietary Engine License

Security
Please report vulnerabilities privately via GitHub Security.

See:

Security Policy

Contributing
Contributions are welcome for GhostMeta (MIT).
GhostEngine contributions cannot be accepted.

See:

Contributing Guidelines

Contact
For forensic collaboration, research inquiries, or GhostEngine licensing, contact the author.


Binary Distribution
Pre‑compiled binaries are available in the GitHub release:

dist/ghostmeta (command line)

GhostMeta.app.zip (macOS drag‑and‑drop wrapper)

No installation required — just download and run.

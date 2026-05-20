© GhostMeta v1.2.0
Structural JPEG Metadata Removal & Forensic Reporting
Apple Silicon only – 4096-bit ARM-NEON via GhostEngine v40

----------------------------------------------------------------------

Overview

GhostMeta removes all metadata from JPEG files (EXIF, XMP, IPTC,
C2PA/JUMBF, COM, Post-EOI) and rebuilds the JPEG container
structurally. The pixel stream remains unchanged – no re-encoding,
no quality loss.

For each processed image, GhostMeta generates:
  - a clean JPEG (_clean.jpg)
  - a machine-readable forensic report (JSON)
  - a human-readable forensic report (TXT)
  - an audit log (JSON)

All reports include SHA-256 hashes (original, clean, meta-blob),
segment status with payload sizes, and integrity hex anchors
(head, tail, post-EOI). In addition, GhostMeta performs an internal
cleanliness check to verify that the structural rebuild is consistent
and that the pixel stream remains untouched.

GhostMeta is designed for environments where verifiable, irreversible JPEG metadata removal is required, such as digital forensics, legal documentation, medical imaging workflows, and archival processes.

----------------------------------------------------------------------

Comparison with ExifTool

ExifTool is a general-purpose metadata reader and writer supporting
hundreds of file formats (JPEG, PNG, TIFF, HEIC, PDF, DOCX, RAW, …).
It can extract, modify, and create metadata across a wide range of
standards and vendor-specific extensions.

GhostMeta is a specialized tool with a different focus:
structural, irreversible metadata removal from JPEG files without
re-encoding, combined with forensic reporting.

GhostMeta removes the following JPEG metadata segments:
  - EXIF (APP1)
  - XMP (APP1)
  - IPTC (APP13)
  - C2PA / JUMBF (APP11)
  - ICC Profile (APP2)
  - Adobe APP14
  - COM (comment segments)
  - Post-EOI appended data

GhostMeta does NOT:
  - read or write metadata in other formats (PNG, TIFF, HEIC, PDF, …)
  - modify or create metadata
  - extract maker notes
  - geotag images
  - perform steganography analysis

GhostMeta is not a replacement for ExifTool. It is a complementary
tool for cases where irreversible, verifiable metadata removal from
JPEG files is required, with a focus on structural integrity and
forensic transparency.

----------------------------------------------------------------------

Zero-Dependency Design

GhostMeta is a standalone binary with no external runtime
dependencies. No Perl, no Python, no dynamic libraries are required.
Only macOS and an Apple Silicon CPU are needed.

This makes GhostMeta easy to deploy, script, and integrate into
forensic workflows without additional installation steps.

----------------------------------------------------------------------

Platform Support

GhostMeta v1.2.0 runs only on Apple Silicon (ARM64) with NEON support.

Fully supported:
  macOS 14+ on M4 Pro, M4 Max, M3 Pro, M3 Max, M2 Pro, M2 Max

Limited support:
  M1 / M1 Pro / M1 Max (reduced NEON throughput)

Not supported:
  Intel Macs, Windows, Linux, x86_64,
  virtualization (Rosetta, WSL, Docker, QEMU)

The binary is a standalone Mach-O ARM64 executable. The integrated
GhostEngine v40 uses a 4096-bit NEON pipeline and cannot run on
non-Apple-Silicon systems.

----------------------------------------------------------------------

Performance

GhostEngine v40 processes metadata at multiple GB/s on M4 Pro/Max.
Large JPEG files are cleaned in under one second:

  50 MB  → < 0.2 s
  100 MB → < 0.5 s
  200 MB → < 1.0 s

No re-encoding means constant memory usage and no latency spikes.

----------------------------------------------------------------------

Build & Run

Prerequisites:
  - macOS 14+ on Apple Silicon
  - clang, make

GhostMeta links statically against the proprietary libghostengine.a.
The pre-compiled binary in dist/ghostmeta already includes the engine.

Build from source:

  make

The binary is created at dist/ghostmeta.

Basic usage:

  ./ghostmeta --in input.jpg --out clean.jpg

Command line options:

  --in <file>        Input JPEG file
  --out <file>       Output cleaned JPEG file
  --log <file>       Additional audit log (JSON)
  --profile <name>   Processing profile (default: secure)
  --help, -h         Show help
  --version, -v      Show version

Exit codes:

  0   success
  1   general error (missing arguments, permission denied)
  2   unsupported format (not JPEG)
  3   I/O error

----------------------------------------------------------------------

Output Files

For input.jpg, GhostMeta produces:

  clean.jpg                         metadata-free JPEG
  input.jpg.forensic.json           machine-readable forensic report
  input.jpg.forensic.txt            human-readable forensic report
  input.jpg.audit.json              short audit log (if --log not used)

----------------------------------------------------------------------

Forensic Report Example

Below is an excerpt from the human-readable report (*.forensic.txt).
The segment matrix shows which metadata types were detected and
removed, including payload sizes.


[3] METADATA SEGMENT MATRIX

---------------------------------------------------------------------------
 Segment Type                       Before         (size)       After
---------------------------------------------------------------------------
 EXIF (APP1)                        NOT_PRESENT    (    0 B)    NOT_PRESENT
 C2PA / JUMBF (APP11)               DETECTED       (23635 B)    REMOVED   
 IPTC (APP13)                       NOT_PRESENT    (    0 B)    NOT_PRESENT
 XMP (APP1)                         NOT_PRESENT    (    0 B)    NOT_PRESENT
 Raw JUMBF Boxes                    DETECTED       (23635 B)    REMOVED   
 Comment Segments (COM)             NOT_PRESENT    (    0 B)    NOT_PRESENT
 Appended Data (Post-EOI)           NOT_PRESENT    (    0 B)    NOT_PRESENT

 Total Removed Segment Types : 2

The JSON report contains the same information in structured form,
plus SHA-256 hashes and hex anchors for independent verification.
GhostMeta computes three SHA-256 hashes per run:

  - original_sha256     – full original file
  - clean_sha256        – cleaned JPEG
  - meta_blob_sha256    – extracted metadata payload

These values allow independent, forensic verification of the
operation and the removed metadata.

Example JSON excerpt:

{
  "segments": {
    "c2pa": {
      "status_before": "DETECTED",
      "status_after": "REMOVED",
      "size_bytes": 23635
    }
  },
  "hashes": {
    "original_sha256": "bc3ebd6b68b9...",
    "clean_sha256": "4b135eaf9418...",
    "meta_blob_sha256": "1b268c8137a7..."
  }
}

The forensic reports also include a cleanliness check section
indicating whether metadata removal, structural rebuild consistency
and pixel stream preservation have passed internal validation.

----------------------------------------------------------------------

Limitations (v1.2.0)

  - JPEG only – PNG, TIFF, WebP, HEIC planned for later releases.
  - Apple Silicon only – no Intel, Windows, or Linux support.
  - Steganography in pixel data is not detected or removed
    (explicitly stated in the report).
  - No GUI – an optional macOS app wrapper (Platypus) is provided
    in the app/ directory.

----------------------------------------------------------------------

License

  - GhostMeta source code – GNU General Public License v3.0
    (see LICENSE file).
  - GhostEngine v40 binary – proprietary, statically linked,
    not covered by GPL.

GhostMeta source code – MIT License (see LICENSE file).
This allows free use, modification, distribution, and integration of the GhostMeta source code in both open‑source and proprietary projects.

GhostEngine v40 binary – proprietary, not open source, not included in this repository.
The GhostEngine is distributed as a separate, closed‑source binary under its own proprietary license.
This license explicitly forbids:

redistribution of the GhostEngine binary

reverse engineering, decompilation, or disassembly

modification or extraction of internal algorithms

integration into competing forensic or cryptographic engines

Linking (static or dynamic) to the GhostEngine binary is permitted as long as the binary remains a separate, unmodified component and is not merged or repackaged in a way that obscures its origin.

Summary:
- GhostMeta → MIT License (open source)
- GhostEngine → Proprietary License (closed source, binary‑only)

This dual‑license model allows GhostMeta to remain open and community‑driven while ensuring that the GhostEngine technology remains protected.

----------------------------------------------------------------------

Future Formats (PNG, HEIC, PDF)

GhostMeta v1.2.0 focuses exclusively on JPEG. Support for additional
formats is planned for future releases, including:

  - PNG (chunk-based metadata removal)
  - HEIC (ISO BMFF box-level metadata removal)
  - PDF (object-level metadata and XMP removal)

These modules will not be open source. They rely on proprietary
components of the GhostEngine and are intended for licensed forensic
and enterprise use.

The JPEG module in this repository remains fully open source under GPLv3.
Future format modules will be distributed separately under commercial
licensing.

----------------------------------------------------------------------

GhostEngine Integration

GhostMeta includes a statically linked copy of the proprietary GhostEngine v40.
The engine is fully embedded inside the Mach-O binary and is not shipped as a
separate file. It cannot be extracted, modified, or used independently.

No internal algorithms, NEON pipelines, state machines, or cryptographic
mechanisms of the GhostEngine are exposed in this repository. Only the public
API declared in `ghostengine_public.h` is used.

----------------------------------------------------------------------

Contact & Research

© 2026 - GhostMeta was developed by Bülent Deniz.
For forensic collaboration, research inquiries, or licensing of
GhostEngine, please contact the author.

----------------------------------------------------------------------


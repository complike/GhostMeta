© GhostMeta v1.2.0
Strukturelle JPEG-Metadatenentfernung & Forensische Analyse
Apple Silicon only – 4096‑bit ARM‑NEON via GhostEngine v40

----------------------------------------------------------------------

Übersicht

GhostMeta entfernt sämtliche Metadaten aus JPEG-Dateien (EXIF, XMP,
IPTC, C2PA/JUMBF, COM, Post‑EOI) und rekonstruiert den JPEG‑Container
strukturell neu. Der Pixelstrom bleibt unverändert – kein Re‑Encoding,
kein Qualitätsverlust.

Für jede verarbeitete Datei erzeugt GhostMeta:
  - ein bereinigtes JPEG (_clean.jpg)
  - einen maschinenlesbaren forensischen Bericht (JSON)
  - einen menschenlesbaren forensischen Bericht (TXT)
  - ein Audit‑Log (JSON)

Alle Berichte enthalten SHA‑256‑Hashes (Original, Clean, Meta‑Blob),
Segmentstatus mit Payload‑Größen sowie Integritäts‑Hex‑Anker
(Head, Tail, Post‑EOI). Zusätzlich führt GhostMeta interne
Konsistenzprüfungen durch, um sicherzustellen, dass der strukturelle
Rebuild korrekt ist und der Pixelstrom unverändert bleibt.

GhostMeta ist für Einsatzbereiche konzipiert, in denen eine nachweisbare, irreversible Entfernung von JPEG‑Metadaten erforderlich ist – etwa in der digitalen Forensik, bei Gutachten, in medizinischen Bildgebungs‑Workflows oder in rechtlich relevanten Dokumentationsprozessen.

----------------------------------------------------------------------

Vergleich mit ExifTool

ExifTool ist ein universelles Metadaten‑Werkzeug, das hunderte
Dateiformate unterstützt (JPEG, PNG, TIFF, HEIC, PDF, DOCX, RAW, …).
Es kann Metadaten auslesen, verändern und erstellen – inklusive
vendor‑spezifischer Erweiterungen.

GhostMeta verfolgt einen anderen Ansatz:
strukturelle, irreversible Entfernung aller JPEG‑Metadaten ohne
Re‑Encoding, kombiniert mit forensischer Transparenz.

GhostMeta entfernt folgende JPEG‑Metadaten‑Segmente:
  - EXIF (APP1)
  - XMP (APP1)
  - IPTC (APP13)
  - C2PA / JUMBF (APP11)
  - ICC Profile (APP2)
  - Adobe APP14
  - COM (Kommentarsegmente)
  - Post‑EOI angehängte Daten

GhostMeta kann NICHT:
  - Metadaten anderer Formate lesen oder schreiben (PNG, TIFF, HEIC, PDF, …)
  - Metadaten erstellen oder modifizieren
  - MakerNotes extrahieren
  - Bilder geotaggen
  - Steganografie erkennen

GhostMeta ersetzt ExifTool nicht. Es ergänzt es.
GhostMeta ist für Fälle gedacht, in denen eine irreversible,
nachweisbare Metadatenentfernung von JPEG‑Dateien erforderlich ist –
mit Fokus auf strukturelle Integrität und forensische Nachvollziehbarkeit.

----------------------------------------------------------------------

Zero‑Dependency‑Design

GhostMeta ist ein vollständig eigenständiges Binary ohne externe
Laufzeitabhängigkeiten. Kein Perl, kein Python, keine dynamischen
Bibliotheken. Es wird lediglich macOS und eine Apple‑Silicon‑CPU benötigt.

Dies macht GhostMeta besonders einfach zu deployen, zu skripten und in
forensische Workflows zu integrieren.

----------------------------------------------------------------------

Plattformunterstützung

GhostMeta v1.2.0 läuft ausschließlich auf Apple Silicon (ARM64) mit
NEON‑Unterstützung.

Voll unterstützt:
  macOS 14+ auf M4 Pro, M4 Max, M3 Pro, M3 Max, M2 Pro, M2 Max

Eingeschränkt unterstützt:
  M1 / M1 Pro / M1 Max (reduzierte NEON‑Durchsatzrate)

Nicht unterstützt:
  Intel‑Macs, Windows, Linux, x86_64,
  Virtualisierung (Rosetta, WSL, Docker, QEMU)

Das Binary ist ein eigenständiges Mach‑O‑ARM64‑Executable. Die integrierte
GhostEngine v40 nutzt eine 4096‑bit‑NEON‑Pipeline und kann auf
Nicht‑Apple‑Silicon‑Systemen nicht ausgeführt werden.

----------------------------------------------------------------------

Performance

GhostEngine v40 verarbeitet Metadaten mit mehreren GB/s auf M4 Pro/Max.
Große JPEG‑Dateien werden in unter einer Sekunde bereinigt:

  50 MB  → < 0.2 s
  100 MB → < 0.5 s
  200 MB → < 1.0 s

Kein Re‑Encoding bedeutet konstante Speichernutzung und keine Latenzspitzen.

----------------------------------------------------------------------

Build & Ausführung

Voraussetzungen:
  - macOS 14+ auf Apple Silicon
  - clang, make

GhostMeta linkt statisch gegen die proprietäre libghostengine.a.
Das vorgebaute Binary in dist/ghostmeta enthält die Engine bereits.

Build aus dem Quellcode:

  make

Das Binary wird unter dist/ghostmeta erzeugt.

Grundlegende Nutzung:

  ./ghostmeta --in input.jpg --out clean.jpg

Kommandozeilenoptionen:

  --in <file>        Eingabedatei (JPEG)
  --out <file>       Ausgabedatei (bereinigtes JPEG)
  --log <file>       Zusätzliches Audit‑Log (JSON)
  --profile <name>   Verarbeitungsprofil (Standard: secure)
  --help, -h         Hilfe anzeigen
  --version, -v      Version anzeigen

Exit‑Codes:

  0   Erfolg
  1   Allgemeiner Fehler (fehlende Argumente, Berechtigungen)
  2   Nicht unterstütztes Format (kein JPEG)
  3   I/O‑Fehler

----------------------------------------------------------------------

Ausgabedateien

Für input.jpg erzeugt GhostMeta:

  clean.jpg                         metadatenfreies JPEG
  input.jpg.forensic.json           maschinenlesbarer Forensik‑Report
  input.jpg.forensic.txt            menschenlesbarer Forensik‑Report
  input.jpg.audit.json              kurzes Audit‑Log (falls --log nicht genutzt)

----------------------------------------------------------------------

Forensik‑Beispiel

Auszug aus dem menschenlesbaren Bericht (*.forensic.txt):

[3] METADATA SEGMENT MATRIX

---------------------------------------------------------------------------
Segmenttyp                        Vorher         (Größe)       Nachher
---------------------------------------------------------------------------
 EXIF (APP1)                       NICHT_VORHANDEN (   0 B)     NICHT_VORHANDEN
 C2PA / JUMBF (APP11)              ERKANNT         (23635 B)    ENTFERNT
 IPTC (APP13)                      NICHT_VORHANDEN (   0 B)     NICHT_VORHANDEN
 XMP (APP1)                        NICHT_VORHANDEN (   0 B)     NICHT_VORHANDEN
 Raw JUMBF Boxes                   ERKANNT         (23635 B)    ENTFERNT
 Kommentarsegmente (COM)           NICHT_VORHANDEN (   0 B)     NICHT_VORHANDEN
 Angefügte Daten (Post‑EOI)        NICHT_VORHANDEN (   0 B)     NICHT_VORHANDEN

Gesamt entfernte Segmenttypen: 2

Der JSON‑Report enthält dieselben Informationen in strukturierter Form,
zusätzlich zu SHA‑256‑Hashes und Hex‑Ankern zur unabhängigen Verifikation.

GhostMeta berechnet drei SHA‑256‑Hashes pro Lauf:

  - original_sha256     – vollständige Originaldatei
  - clean_sha256        – bereinigtes JPEG
  - meta_blob_sha256    – extrahierte Metadaten

Die Berichte enthalten außerdem einen Cleanliness‑Check, der bestätigt,
dass der strukturelle Rebuild konsistent ist und der Pixelstrom
unverändert blieb.

----------------------------------------------------------------------

Einschränkungen (v1.2.0)

  - Nur JPEG – PNG, TIFF, WebP, HEIC sind für spätere Versionen geplant.
  - Nur Apple Silicon – keine Unterstützung für Intel, Windows oder Linux.
  - Steganografie im Pixelstrom wird nicht erkannt oder entfernt.
  - Keine GUI – eine optionale macOS‑App (Platypus) liegt im app/‑Ordner bei.

----------------------------------------------------------------------

Lizenz

  - GhostMeta Quellcode – GNU General Public License v3.0
  - GhostEngine v40 Binary – proprietär, statisch gelinkt,
    nicht durch die GPL abgedeckt

Der GhostMeta‑Quellcode steht unter der MIT‑Lizenz (siehe LICENSE).
Dies erlaubt die freie Nutzung, Veränderung, Weitergabe und Integration des GhostMeta‑Codes – sowohl in offenen als auch in proprietären Projekten.

Die GhostEngine v40 ist proprietär, nicht quelloffen und nicht Bestandteil dieses Repositories.
Sie wird als separate, geschlossene Binärkomponente unter einer eigenen Lizenz bereitgestellt.
Diese Lizenz untersagt ausdrücklich:

Weitergabe der GhostEngine‑Binary

Reverse Engineering, Dekompilierung oder Disassemblierung

Modifikation oder Extraktion interner Algorithmen

Integration in konkurrierende forensische oder kryptografische Engines

Das Linken (statisch oder dynamisch) der GhostEngine‑Binary ist erlaubt, solange die Binary unverändert bleibt und nicht in einer Weise zusammengeführt oder neu verpackt wird, die ihren Ursprung verschleiert.

Zusammenfassung:
- GhostMeta → MIT‑Lizenz (Open Source)
- GhostEngine → proprietäre Lizenz (Closed Source, Binary‑only)

Dieses Dual‑Lizenzmodell ermöglicht es, GhostMeta offen und community‑orientiert zu halten, während die Technologie der GhostEngine geschützt bleibt.

----------------------------------------------------------------------

Zukünftige Formate (PNG, HEIC, PDF)

GhostMeta v1.2.0 konzentriert sich ausschließlich auf JPEG. Unterstützung
für weitere Formate ist für zukünftige Versionen geplant:

  - PNG (Chunk‑basierte Metadatenentfernung)
  - HEIC (ISO‑BMFF‑Box‑Level‑Metadatenentfernung)
  - PDF (Objekt‑Level‑Metadaten- und XMP‑Entfernung)

Diese Module werden nicht Open Source sein. Sie basieren auf proprietären
Komponenten der GhostEngine und sind für lizenzierte forensische und
Enterprise‑Anwendungen vorgesehen.

Das JPEG‑Modul in diesem Repository bleibt vollständig Open Source
unter GPLv3. Zukünftige Formatmodule werden separat unter kommerzieller
Lizenz vertrieben.

----------------------------------------------------------------------

GhostEngine-Integration

GhostMeta enthält eine statisch gelinkte Kopie der proprietären GhostEngine v40.
Die Engine ist vollständig im Mach-O-Binary eingebettet und wird nicht als
separate Datei ausgeliefert. Sie kann weder extrahiert, modifiziert noch
unabhängig verwendet werden.

Interne Algorithmen, NEON-Pipelines, Zustandsmaschinen oder kryptografische
Mechanismen der GhostEngine werden in diesem Repository nicht offengelegt.
Es wird ausschließlich die öffentliche API aus `ghostengine_public.h` verwendet.

----------------------------------------------------------------------

Kontakt & Forschung

© 2026 – GhostMeta wurde entwickelt von Bülent Deniz.
Für forensische Zusammenarbeit, Forschungsanfragen oder Lizenzierung
der GhostEngine kontaktieren Sie bitte den Autor.

----------------------------------------------------------------------


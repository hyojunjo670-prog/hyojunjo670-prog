| [KR 한국어](./ko.md) | [US English](../../README.md) | [JP 日本語](./ja.md) | [CN 简体中文](./zh-CN.md) | [ES Español](./es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./fr.md) | [DE Deutsch](./de.md) | [RU Русский](./ru.md) | [SA العربية](./ar.md) | [IN हिन्दी](./hi.md) |

---

# Hyojun Jo (`hyojunjo670-prog`)

```text
       ___                   __               __              __
      / _ \_______  ___ ____/ /_ _____  ___ _/ /_____ _____ _/ /
     / ___/ __/ _ \/ _ `/ _  / // / _ \/ _ `/  '_/ -_) __/ _  / 
    /_/  /_/  \___/\_, /\_,_/\_, / .__/\_,_/_/\_\\__/_/  \_,_/  
                  /___/     /___/_/                             
  System- und Verteilte-Infrastruktur-Ingenieur · Seoul, Südkorea
```

> "Mechanische Sympathie, deterministische Latenz und kompromisslose Ressourceneffizienz."

Ich bin ein Software-Ingenieur für Systemprogrammierung und verteilte Infrastrukturen, der sich auf den Bau deterministischer, extrem ressourcenschonender Systeme konzentriert. Meine Arbeit widmet sich Datenpipelines mit extrem geringer Latenz, lock-freien Nebenläufigkeitsalgorithmen, Linux-Kernel-Subsystemen (`eBPF` / `io_uring`) und eingebetteten Speicher-Engines, bei denen jede Mikrosekunde und jede CPU-Cache-Zeile zählen.

---

## 🧭 Engineering-Philosophie

- **Mechanische Sympathie (Mechanical Sympathy)**: Softwareentwicklung im tiefen Einklang mit den Eigenschaften der Hardware – L1/L2/L3 CPU-Cache-Zeilen, TLB-Trefferquoten, NUMA-Speichertopologie und Verzweigungsvorhersagemuster.
- **Vorhersehbare Latenz vor Spitzendurchsatz**: Ein System, das unter Maximallast eine stabile Latenz im 99,9. Perzentil (p99.9) gewährleistet, ist einem System mit hohem Durchschnittsdurchsatz und unkontrollierten Ausreißern weit überlegen.
- **Allokationsfreie Hot-Paths (Zero-Allocation)**: Kritische Ausführungspfade dürfen niemals dynamische Heap-Allokationen, dynamischen Dispatch-Overhead oder unnötige Kontextwechsel durch Systemaufrufe verursachen.
- **Minimale externe Abhängigkeiten**: Verzicht auf unübersichtliche, fehleranfällige Abhängigkeitsbäume zugunsten robuster, transparenter POSIX- und Sprachstandardbibliotheken.

---

## 🛠️ Technisches Kernarsenal

### Programmiersprachen & Compiler

| Technologie | Erfahrungsstufe | Primäre Anwendungsbereiche |
| :--- | :--- | :--- |
| **Rust** | Tägliche Produktion | Speichersichere Systemkerne, asynchrone Runtimes, sichere Betriebssystemabstraktionen |
| **C (C11 / GNU99)** | Tägliche Produktion | Linux-Kernel-Module, eBPF-Programme, hardwarenahe POSIX-Systemprogrammierung |
| **C++ (C++20)** | Fortgeschritten | Hochperformante lock-freie Datenstrukturen, SIMD-Vektorisierung |
| **Go** | Fortgeschritten | Verteilte Steuerungsebenen, RPC-Micro-Daemons, Cloud-Netzwerke |
| **POSIX Shell / Bash** | Sicher | Automatisierte Bereitstellungsskripte, Kernel-Build-Pipelines, CI-Orchestrierung |
| **Python** | Sicher | Benchmark-Analysewerkzeuge, automatisierte Systemverifikation, Prototypen |

### Systeme, Kernel & Low-Level-Architektur

| Bereich | Schwerpunkte und Technologien |
| :--- | :--- |
| **Linux-Kernel-Interna** | eBPF (XDP / TC / tracepoints), `io_uring`, POSIX-Threads, cgroups v2, epoll |
| **Speicher & Hardware** | NUMA-optimierte Zuweisung, jemalloc-Tuning, CPU-Cache-Ausrichtung (`64-Byte`), Lock-Free-Ringe |
| **Speicherarchitektur** | LSM-Tree-Engines, Write-Ahead Logging (WAL), SSTable-Kompaktierung, Zero-Copy mmap |
| **Netzwerk & Protokolle** | Low-Latency TCP/UDP-Socket-Optimierung, QUIC-Transport, FlatBuffers / Protobuf Zero-Copy |

---

## 🚀 Wichtige Open-Source-Projekte

### 1. `pulse-ring` — Hochdurchsatz-Lock-Free-Ringpuffer
- **Sprache**: Rust
- **Beschreibung**: Ein Cache-Line-gepaddeter, beschränkter SPMC- (Single-Producer Multi-Consumer) und MPMC- (Multi-Producer Multi-Consumer) Ringpuffer mit atomarer Speicherordnung (`Acquire` / `Release`), optimiert für Sub-Mikrosekunden-Ereignisverteilung.
- **Highlights**:
  - Null Allokationen während der Übertragung; keine Mutex-Blockaden dank atomarer Operationen.
  - Auf modernen x86_64 AVX-512-Prozessoren mit über 48 Millionen Nachrichten/Sekunde getestet.

### 2. `aether-kv` — Eingebettete LSM-Tree Key-Value-Engine
- **Sprache**: C++20 / C
- **Beschreibung**: Speziell für NVMe-SSDs optimierte eingebettete Schlüssel-Wert-Speicher-Engine, die Linux `io_uring` für asynchrone direkte I/O-Batch-Operationen (`O_DIRECT`) nutzt.
- **Highlights**:
  - Segmentiertes Write-Ahead-Log (WAL) mit CRC32-Prüfsummen zur Gewährleistung von Absturzsicherheit und Datenkonsistenz.
  - Zweistufige gestaffelte Kompaktierung, die die Schreibverstärkung minimiert und die Lese-Latenz (p99) unter 120 Mikrosekunden hält.

### 3. `packet-lens` — Kernel-Space eBPF Flow-Telemetrie-Agent
- **Sprache**: C / Go
- **Beschreibung**: Schlanker Netzwerkbeobachtungs-Daemon, der über eBPF XDP/TC-Hooks angebunden ist und TCP-Round-Trip-Zeiten (RTT), Paketverlustraten und Retransmissionen mit minimalem CPU-Bedarf ermittelt.
- **Highlights**:
  - Läuft vollständig im Kernel-Space; überträgt Telemetriedaten über Ringpuffer ohne Kopiervorgänge in den User-Space.
  - Nativer Prometheus-Metrik-Exporter mit sofortigen Latenzwarnungen im Sub-Millisekunden-Bereich.

### 4. `flow-rpc` — Zero-Copy Binär-RPC-Framework
- **Sprache**: Rust / Go
- **Beschreibung**: Minimalistisches, schnelles binäres RPC-Framework zur Beseitigung von Deserialisierungsengpässen (wie bei JSON/Protobuf) über Unix Domain Sockets und schnelle TCP-Verbindungen.
- **Highlights**:
  - Direktes Mapping von Speicherpuffern ohne Duplizierung mittels FlatBuffers-Schemata.
  - Integrierte Multiplexing-Engine, die bis zu 100.000 persistente Verbindungen pro Worker-Core unterstützt.

---

## 📊 Systemarchitektur-Übersicht

```text
+-------------------------------------------------------------------------+
|                         Benutzerbereich-Anwendungen                     |
|        [ pulse-ring SPMC ] <---> [ flow-rpc ] <---> [ aether-kv ]       |
+-------------------------------------------------------------------------+
                                    |
                        (Direkte I/O via io_uring)
                                    v
+-------------------------------------------------------------------------+
|                         Linux-Kernel-Subsystem                          |
|   +-------------------+    +--------------------+    +--------------+   |
|   |   eBPF XDP Hook   |    | VFS / Block-Schicht|    |  Page Cache  |   |
|   |  ( packet-lens )  |    |  ( Async Direct )  |    |  ( Bypass )  |   |
|   +-------------------+    +--------------------+    +--------------+   |
+-------------------------------------------------------------------------+
                                    |
                                    v
+-------------------------------------------------------------------------+
|                         Physische Hardware                              |
|       [ NVMe-Speicher ]    [ PCIe Gen4 NIC ]     [ NUMA DDR5 RAM ]      |
+-------------------------------------------------------------------------+
```

---

## 📈 Aktuelle Forschung & Roadmap

- [ ] Erforschung SIMD-beschleunigter Dekodierungsalgorithmen (`AVX-512` & `NEON`) für binäre Datenpakete mit variabler Länge (varint).
- [ ] Experimente mit Linux 6.x sched-ext (eBPF-basierte benutzerdefinierte CPU-Scheduler) für extrem latenzkritische Arbeitslasten.
- [ ] Entwurf eines deterministischen verteilten Snapshot-Konsensprotokolls für bandbreitenarme Edge-Cluster.
- [ ] Verfassen tiefgehender technischer Artikel zu Linux-Speicherallokatoren und Kernel-Socket-Puffern.

---

## 📬 Kontakt & Austausch

- **Standort**: Seoul, Südkorea (UTC+9)
- **Hauptsysteme**: Arch Linux (Kernel 6.12+ LTS) / Debian GNU/Linux Server
- **E-Mail**: `hyojunjo670@gmail.com`
- **GitHub**: [`hyojunjo670-prog`](https://github.com/hyojunjo670-prog)
- **GPG-Schlüssel-Fingerprint**: `9D4F 21B0 E77C 4531 82A4 03E9 61F2 BC34 7E0A 5B22`

---

```text
[EOF] Entwickelt mit Disziplin, Präzision und Respekt vor der Hardware.
```

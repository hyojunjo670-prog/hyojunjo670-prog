| [KR 한국어](./docs/locales/ko.md) | [US English](./README.md) | [JP 日本語](./docs/locales/ja.md) | [CN 简体中文](./docs/locales/zh-CN.md) | [ES Español](./docs/locales/es.md) |
| :---: | :---: | :---: | :---: | :---: |
| [FR Français](./docs/locales/fr.md) | [DE Deutsch](./docs/locales/de.md) | [RU Русский](./docs/locales/ru.md) | [SA العربية](./docs/locales/ar.md) | [IN हिन्दी](./docs/locales/hi.md) |

---

# Hyojun Jo (`hyojunjo670-prog`)

```text
       ___                   __               __              __
      / _ \_______  ___ ____/ /_ _____  ___ _/ /_____ _____ _/ /
     / ___/ __/ _ \/ _ `/ _  / // / _ \/ _ `/  '_/ -_) __/ _  / 
    /_/  /_/  \___/\_, /\_,_/\_, / .__/\_,_/_/\_\\__/_/  \_,_/  
                  /___/     /___/_/                             
  Systems & Distributed Infrastructure Engineer · Seoul, South Korea
```

> "Mechanical sympathy, deterministic latency, and ruthless resource efficiency."

I am a systems and distributed infrastructure software engineer dedicated to building deterministic, low-overhead software. My work focuses on low-latency data pipelines, lock-free concurrency algorithms, Linux kernel subsystems (`eBPF` / `io_uring`), and embeddable storage engines where every microsecond and CPU cache line counts.

---

## 🧭 Engineering Philosophy

- **Mechanical Sympathy**: Design software with deep respect for hardware primitives—L1/L2/L3 CPU cache lines, TLB hit rates, NUMA memory node topology, and branch predictor patterns.
- **Predictable Latency Over Peak Throughput**: A system maintaining a steady p99.9 latency under heavy load is vastly superior to one boasting high average throughput plagued by tail-latency spikes.
- **Zero-Allocation Execution Paths**: Critical hot loops should never trigger memory allocations, dynamic dispatch overhead, or unnecessary syscall context switches.
- **Zero External Dependencies**: Favor robust, transparent POSIX and language standard library foundations over dependency trees prone to bloat and vulnerabilities.

---

## 🛠️ Core Technical Arsenal

### Languages & Compilers

| Technology | Proficiency Level | Primary Use Cases |
| :--- | :--- | :--- |
| **Rust** | Production Daily | Memory-safe systems engines, concurrent runtimes, safe OS abstractions |
| **C (C11 / GNU99)** | Production Daily | Linux kernel modules, eBPF programs, low-level POSIX systems programming |
| **C++ (C++20)** | Advanced | High-performance lock-free data structures, SIMD vectorization |
| **Go** | Advanced | Distributed control planes, high-throughput RPC services, cloud networking |
| **POSIX Shell / Bash** | Proficient | Production deployment scripts, kernel build pipelines, CI/CD toolchains |
| **Python** | Proficient | Benchmark analysis harnesses, regression verification, performance modeling |

### Systems, Kernel & Low-Level Architecture

| Domain | Focus Areas & Technologies |
| :--- | :--- |
| **Linux Kernel Internals** | eBPF (XDP / TC / tracepoints), `io_uring`, POSIX Threads, cgroups v2, epoll |
| **Memory & Hardware** | NUMA-aware allocation, jemalloc tuning, CPU cache alignment (`64-byte`), lock-free rings |
| **Storage Architecture** | LSM-Tree engines, Write-Ahead Logging (WAL), SSTable compaction, Zero-Copy mmap |
| **Networking & Protocols** | Low-latency TCP/UDP socket tuning, QUIC transport, FlatBuffers / Protobuf serialization |

---

## 🚀 Key Open-Source Projects

### 1. `pulse-ring` — High-Throughput Lock-Free Ring Buffer
- **Language**: Rust
- **Description**: A cache-line padded, bounded Single-Producer Multi-Consumer (SPMC) and Multi-Producer Multi-Consumer (MPMC) circular buffer engineered for sub-microsecond event distribution.
- **Highlights**:
  - Zero allocation during transmission; zero mutex contention using atomic memory ordering (`Acquire` / `Release`).
  - Benchmarked at over 48 million messages/sec throughput on modern x86_64 AVX-512 architecture.

### 2. `aether-kv` — Embedded LSM-Tree Key-Value Engine
- **Language**: C++20 / C
- **Description**: An embeddable key-value storage engine built specifically for NVMe SSDs utilizing Linux `io_uring` for asynchronous, batched direct I/O (`O_DIRECT`).
- **Highlights**:
  - Segmented Write-Ahead Log (WAL) with CRC32 checksums guaranteeing crash consistency.
  - Two-level tiered compaction minimizing write amplification while keeping read p99 below 120 microseconds.

### 3. `packet-lens` — Kernel-Space eBPF Flow Telemetry Probe
- **Language**: C / Go
- **Description**: A lightweight network observability probe attached via eBPF XDP/TC hooks to extract TCP round-trip time, packet drop metrics, and retransmission states with near-zero CPU footprint.
- **Highlights**:
  - Operates entirely within kernel space; outputs telemetry data into ring buffers without copying raw packets to user space.
  - Native Prometheus metrics exporter emitting sub-millisecond network degradation alerts.

### 4. `flow-rpc` — Zero-Copy Binary RPC Framework
- **Language**: Rust / Go
- **Description**: A minimalist, high-speed binary RPC framework designed to eliminate JSON/Protobuf deserialization bottlenecks over Unix Domain Sockets and high-speed TCP links.
- **Highlights**:
  - Direct memory buffer mapping using FlatBuffers schema validation.
  - Integrated multiplexing engine supporting up to 100,000 concurrent persistent connections per worker core.

---

## 📊 Systems Architecture Snapshot

```text
+-------------------------------------------------------------------------+
|                           User Space Applications                       |
|        [ pulse-ring SPMC ] <---> [ flow-rpc ] <---> [ aether-kv ]       |
+-------------------------------------------------------------------------+
                                    |
                        (Direct I/O via io_uring)
                                    v
+-------------------------------------------------------------------------+
|                           Linux Kernel Subsystem                        |
|   +-------------------+    +--------------------+    +--------------+   |
|   |   eBPF XDP Hook   |    |    VFS / Block     |    |  Page Cache  |   |
|   |  ( packet-lens )  |    |  ( Async Direct )  |    |  ( Bypass )  |   |
|   +-------------------+    +--------------------+    +--------------+   |
+-------------------------------------------------------------------------+
                                    |
                                    v
+-------------------------------------------------------------------------+
|                           Hardware Primitives                           |
|       [ NVMe Storage ]     [ PCIe Gen4 NIC ]     [ NUMA DDR5 RAM ]      |
+-------------------------------------------------------------------------+
```

---

## 📈 Current Research & Roadmaps

- [ ] Investigating SIMD-accelerated varint decoding for binary packet decoders (`AVX-512` & `NEON`).
- [ ] Experimenting with Linux 6.x sched-ext (BFP-based custom CPU schedulers) for tail-latency critical workloads.
- [ ] Designing a deterministic distributed snapshot consensus mechanism for low-bandwidth mesh clusters.
- [ ] Writing in-depth technical breakdowns on Linux memory allocators and kernel socket buffers.

---

## 📬 Contact & Coordination

- **Location**: Seoul, South Korea (UTC+9)
- **Primary Workstations**: Arch Linux (Kernel 6.12+ LTS) / Debian GNU/Linux Server
- **Email**: `hyojunjo670@gmail.com`
- **GitHub**: [`hyojunjo670-prog`](https://github.com/hyojunjo670-prog)
- **GPG Key Fingerprint**: `9D4F 21B0 E77C 4531 82A4 03E9 61F2 BC34 7E0A 5B22`

---

```text
[EOF] Built with discipline, precision, and respect for the hardware.
```

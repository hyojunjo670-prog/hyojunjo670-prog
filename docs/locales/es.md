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
  Ingeniero de Sistemas e Infraestructura Distribuida · Seúl, Corea del Sur
```

> "Simpatía mecánica, latencia determinista y estricta eficiencia de recursos."

Soy un ingeniero de software enfocado en sistemas e infraestructura distribuida, dedicado a construir arquitecturas de bajo overhead y comportamiento determinista. Mi trabajo se centra en canalizaciones de datos de latencia ultra baja, algoritmos de concurrencia sin bloqueo (lock-free), subsistemas del kernel Linux (`eBPF` / `io_uring`) y motores de almacenamiento embebidos donde cada microsegundo y línea de caché de CPU cuentan.

---

## 🧭 Filosofía de Ingeniería

- **Simpatía Mecánica (Mechanical Sympathy)**: Diseñar software en armonía con los mecanismos físicos del hardware: líneas de caché L1/L2/L3, tasas de acierto TLB, topología de memoria NUMA y predictores de saltos.
- **Latencia Predecible sobre Rendimiento Pico**: Un sistema que mantiene una latencia estable en el percentil 99.9 (p99.9) bajo cargas extremas es infinitamente superior a uno con alto rendimiento promedio pero afectado por picos impredecibles.
- **Rutas Críticas sin Asignación Dinámica (Zero-Allocation)**: Los bucles de procesamiento crítico jamás deben disparar asignaciones de memoria en el heap, sobrecostos de despacho dinámico ni cambios de contexto de llamadas al sistema innecesarios.
- **Minimalismo de Dependencias**: Rechazar árboles de dependencias masivos que generan sobrepeso y vulnerabilidades, priorizando las bases transparentes y robustas de POSIX y las bibliotecas estándar del lenguaje.

---

## 🛠️ Arsenal Técnico Principal

### Lenguajes de Programación y Compiladores

| Tecnología | Nivel de Competencia | Casos de Uso Principales |
| :--- | :--- | :--- |
| **Rust** | Producción Diaria | Núcleos seguros en memoria, entornos concurrentes, abstracciones del SO |
| **C (C11 / GNU99)** | Producción Diaria | Módulos del kernel Linux, programas eBPF, programación de sistemas POSIX |
| **C++ (C++20)** | Avanzado | Estructuras de datos lock-free de alto rendimiento, vectorización SIMD |
| **Go** | Avanzado | Planos de control distribuidos, micro-demonios RPC, redes en la nube |
| **POSIX Shell / Bash** | Competente | Scripts de despliegue automatizado, compilación de kernels, orquestación CI |
| **Python** | Competente | Arneses de análisis de benchmarks, verificación automatizada, prototipos |

### Sistemas, Kernel y Arquitectura de Bajo Nivel

| Dominio | Áreas de Enfoque y Tecnologías |
| :--- | :--- |
| **Internos del Kernel Linux** | eBPF (XDP / TC / tracepoints), `io_uring`, Hilos POSIX, cgroups v2, epoll |
| **Memoria y Hardware** | Asignación consciente de NUMA, ajuste de jemalloc, alineación de caché (`64-byte`), anillos lock-free |
| **Arquitectura de Almacenamiento** | Motores LSM-Tree, Write-Ahead Logging (WAL), compactación SSTable, mmap Zero-Copy |
| **Redes y Protocolos** | Ajuste de sockets TCP/UDP de baja latencia, transporte QUIC, serialización FlatBuffers / Protobuf |

---

## 🚀 Proyectos Open-Source Destacados

### 1. `pulse-ring` — Búfer Circular Lock-Free de Alto Rendimiento
- **Lenguaje**: Rust
- **Descripción**: Búfer circular acotado SPMC (Single-Producer Multi-Consumer) y MPMC (Multi-Producer Multi-Consumer) con alineación de línea de caché y ordenamiento atómico de memoria (`Acquire` / `Release`), diseñado para distribución de eventos en sub-microsegundos.
- **Aspectos Destacados**:
  - Cero asignaciones dinámicas en tránsito; sin contención de bloqueos mutex.
  - Más de 48 millones de mensajes/segundo comprobados en procesadores x86_64 AVX-512 modernos.

### 2. `aether-kv` — Motor de Clave-Valor Embebido Basado en LSM-Tree
- **Lenguaje**: C++20 / C
- **Descripción**: Motor de almacenamiento clave-valor optimizado específicamente para unidades SSD NVMe mediante el uso de Linux `io_uring` para operaciones de E/S directa por lotes y asíncrona (`O_DIRECT`).
- **Aspectos Destacados**:
  - Registro de escritura previa (WAL) segmentado con sumas de comprobación CRC32 para garantizar consistencia ante caídas del sistema.
  - Compactación escalonada en dos niveles que minimiza la amplificación de escritura manteniendo el p99 de lectura por debajo de 120 microsegundos.

### 3. `packet-lens` — Agente de Telemetría de Flujos eBPF en Espacio de Kernel
- **Lenguaje**: C / Go
- **Descripción**: Demonio de observabilidad de red ultraligero acoplado a ganchos eBPF XDP/TC que extrae tiempos de ida y vuelta TCP (RTT), tasas de pérdida de paquetes y retransmisiones con mínimo impacto en CPU.
- **Aspectos Destacados**:
  - Opera íntegramente en el espacio del kernel; transfiere métricas mediante búferes circulares sin copiar paquetes crudos al espacio de usuario.
  - Exportador nativo de métricas para Prometheus con alertas de degradación de red en milisegundos.

### 4. `flow-rpc` — Framework RPC Binario de Copia Cero (Zero-Copy)
- **Lenguaje**: Rust / Go
- **Descripción**: Framework RPC binario minimalista de alta velocidad concebido para suprimir los cuellos de botella de deserialización JSON/Protobuf sobre sockets de dominio Unix y enlaces TCP rápidos.
- **Aspectos Destacados**:
  - Mapeo directo de búferes de memoria validado con esquemas FlatBuffers sin duplicación de memoria.
  - Motor multiplexor integrado capaz de gestionar hasta 100,000 conexiones concurrentes persistentes por núcleo de procesamiento.

---

## 📊 Diagrama de Arquitectura de Sistemas

```text
+-------------------------------------------------------------------------+
|                        Aplicaciones en Espacio de Usuario               |
|        [ pulse-ring SPMC ] <---> [ flow-rpc ] <---> [ aether-kv ]       |
+-------------------------------------------------------------------------+
                                    |
                        (E/S Directa Asíncrona vía io_uring)
                                    v
+-------------------------------------------------------------------------+
|                        Subsistema del Kernel Linux                      |
|   +-------------------+    +--------------------+    +--------------+   |
|   |   Hook eBPF XDP   |    | Capa VFS / Bloques |    | Caché Página |   |
|   |  ( packet-lens )  |    |  ( Async Direct )  |    |  ( Bypass )  |   |
|   +-------------------+    +--------------------+    +--------------+   |
+-------------------------------------------------------------------------+
                                    |
                                    v
+-------------------------------------------------------------------------+
|                           Hardware Físico                               |
|       [ Disco NVMe ]       [ Tarjeta PCIe Gen4 ]     [ Memoria DDR5 ]   |
+-------------------------------------------------------------------------+
```

---

## 📈 Investigación Actual y Objetivos

- [ ] Estudio de algoritmos SIMD (`AVX-512` y `NEON`) para la decodificación ultra rápida de enteros de longitud variable (varint) en paquetes binarios.
- [ ] Experimentación con Linux 6.x sched-ext (planificadores de CPU personalizados basados en eBPF) para cargas sensibles a latencias de cola.
- [ ] Diseño de un mecanismo determinista de consenso para instantáneas distribuidas en clústeres de baja conectividad.
- [ ] Publicación de artículos técnicos sobre los mecanismos internos de los asignadores de memoria en Linux y la optimización de búferes de socket.

---

## 📬 Contacto y Colaboración

- **Ubicación**: Seúl, Corea del Sur (UTC+9)
- **Entorno de Trabajo Principal**: Arch Linux (Kernel 6.12+ LTS) / Debian GNU/Linux Server
- **Correo Electrónico**: `hyojunjo670@gmail.com`
- **GitHub**: [`hyojunjo670-prog`](https://github.com/hyojunjo670-prog)
- **Huella Digital de Clave GPG**: `9D4F 21B0 E77C 4531 82A4 03E9 61F2 BC34 7E0A 5B22`

---

```text
[EOF] Diseñado con disciplina, precisión y respeto por el hardware.
```

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
  Engenheiro de Sistemas e Infraestrutura Distribuída · Seul, Coreia do Sul
```

> "Simpatia mecânica, latência determinística e máxima eficiência de recursos."

Sou um engenheiro de software focado em sistemas de baixo nível e infraestrutura distribuída, dedicado a construir arquiteturas de mínimo overhead e previsibilidade absoluta. Minha atuação foca em pipelines de dados de latência ultrabaixa, algoritmos de concorrência sem bloqueio (lock-free), subsistemas do kernel Linux (`eBPF` / `io_uring`) e motores de armazenamento embarcados onde cada microssegundo e linha de cache de CPU fazem a diferença.

---

## 🧭 Filosofia de Engenharia

- **Simpatia Mecânica (Mechanical Sympathy)**: Projetar software em perfeita harmonia com os princípios físicos do hardware: linhas de cache L1/L2/L3, taxas de acerto do TLB, topologia de memória NUMA e preditores de desvio.
- **Latência Previsível Acima da Vazão de Pico**: Um sistema que sustenta latência estável no percentil 99,9 (p99.9) sob estresse severo é infinitamente superior a um sistema com alta vazão média que sofre de picos imprevisíveis na cauda.
- **Caminhos Críticos Sem Alocação Dinâmica (Zero-Allocation)**: Loops críticos de processamento nunca devem disparar alocações de memória na heap, custos de despacho dinâmico ou trocas de contexto desnecessárias por chamadas de sistema.
- **Sobriedade em Dependências Externas**: Evitar árvores de dependências volumosas e vulneráveis, priorizando as fundações sólidas e transparentes de POSIX e bibliotecas padrão das linguagens.

---

## 🛠️ Arsenal Técnico Principal

### Linguagens de Programação e Compiladores

| Tecnologia | Nível de Proficiência | Casos de Uso Primários |
| :--- | :--- | :--- |
| **Rust** | Produção Diária | Motores de sistema com segurança de memória, runtimes concorrentes |
| **C (C11 / GNU99)** | Produção Diaria | Módulos do kernel Linux, programas eBPF, programação POSIX de baixo nível |
| **C++ (C++20)** | Avançado | Estruturas de dados lock-free de alto desempenho, vetorização SIMD |
| **Go** | Avançado | Planos de controle distribuídos, micro-daemons RPC, redes em nuvem |
| **POSIX Shell / Bash** | Fluente | Scripts de implantação automatizada, compilação de kernels, automação CI |
| **Python** | Fluente | Ferramentas de análise de benchmarks, testes automatizados, protótipos |

### Sistemas, Kernel e Arquitetura de Baixo Nível

| Domínio | Áreas de Foco e Tecnologias |
| :--- | :--- |
| **Internos do Kernel Linux** | eBPF (XDP / TC / tracepoints), `io_uring`, Threads POSIX, cgroups v2, epoll |
| **Memória e Hardware** | Alocação com suporte a NUMA, ajuste de jemalloc, alinhamento de cache (`64-byte`), lock-free |
| **Arquitetura de Armazenamento** | Motores LSM-Tree, Write-Ahead Logging (WAL), compactação SSTable, mmap Zero-Copy |
| **Redes e Protocolos** | Ajuste de sockets TCP/UDP de baixa latência, transporte QUIC, FlatBuffers / Protobuf |

---

## 🚀 Principais Projetos Open-Source

### 1. `pulse-ring` — Buffer Circular Lock-Free de Altíssima Vazão
- **Linguagem**: Rust
- **Descrição**: Buffer circular limitado SPMC (Single-Producer Multi-Consumer) e MPMC (Multi-Producer Multi-Consumer) com padding de linha de cache e ordenação atômica de memória (`Acquire` / `Release`), desenvolvido para distribuição de eventos em escala sub-microssegundo.
- **Destaques**:
  - Zero alocações dinâmicas em trânsito; sem contenção de mutex através de operações atômicas puras.
  - Testado com vazão de mais de 48 milhões de mensagens/segundo em processadores x86_64 AVX-512 modernos.

### 2. `aether-kv` — Motor Chave-Valor Embarcado Baseado em LSM-Tree
- **Linguagem**: C++20 / C
- **Descrição**: Motor de armazenamento chave-valor de alta performance otimizado para SSDs NVMe, empregando Linux `io_uring` para operações de E/S direta assíncrona em lote (`O_DIRECT`).
- **Destaques**:
  - Log pré-gravação (WAL) segmentado com somas de verificação CRC32, garantindo consistência contra falhas do sistema.
  - Compactação escalonada em dois níveis, minimizando a amplificação de escrita e mantendo a latência p99 de leitura abaixo de 120 microssegundos.

### 3. `packet-lens` — Agente de Telemetria de Fluxo eBPF em Espaço de Kernel
- **Linguagem**: C / Go
- **Descrição**: Daemon ultraleve de observabilidade de rede conectado a ganchos eBPF XDP/TC, capturando RTT de conexões TCP, taxa de perda de pacotes e retransmissões com custo mínimo de CPU.
- **Destaques**:
  - Opera integralmente no espaço do kernel; transfere métricas via buffers circulares sem copiar pacotes brutos para o espaço do usuário.
  - Exportador nativo de métricas para Prometheus com alertas de degradação de rede em milissegundos.

### 4. `flow-rpc` — Framework RPC Binário com Zero-Copy
- **Linguagem**: Rust / Go
- **Descrição**: Framework RPC binário minimalista criado para eliminar gargalos de desserialização JSON/Protobuf sobre Unix Domain Sockets e redes TCP de alta velocidade.
- **Destaques**:
  - Mapeamento direto de buffers de memória a partir de esquemas FlatBuffers sem duplicação de dados.
  - Motor de multiplexação integrado capaz de sustentar até 100.000 conexões persistentes simultâneas por núcleo de processamento.

---

## 📊 Diagrama de Arquitetura de Sistemas

```text
+-------------------------------------------------------------------------+
|                        Aplicações no Espaço de Usuário                  |
|        [ pulse-ring SPMC ] <---> [ flow-rpc ] <---> [ aether-kv ]       |
+-------------------------------------------------------------------------+
                                    |
                        (E/S Direta Assíncrona via io_uring)
                                    v
+-------------------------------------------------------------------------+
|                        Subsistema do Kernel Linux                       |
|   +-------------------+    +--------------------+    +--------------+   |
|   |   Hook eBPF XDP   |    | Camada VFS / Bloco |    |  Page Cache  |   |
|   |  ( packet-lens )  |    |  ( Async Direct )  |    |  ( Bypass )  |   |
|   +-------------------+    +--------------------+    +--------------+   |
+-------------------------------------------------------------------------+
                                    |
                                    v
+-------------------------------------------------------------------------+
|                           Hardware Físico                               |
|       [ Armazenamento NVMe ] [ Placa PCIe Gen4 ] [ Memória DDR5 NUMA ]  |
+-------------------------------------------------------------------------+
```

---

## 📈 Pesquisas Atuais e Objetivos

- [ ] Estudo de algoritmos SIMD (`AVX-512` e `NEON`) para decodificação veloz de inteiros de comprimento variável (varint) em pacotes binários.
- [ ] Experimentação com Linux 6.x sched-ext (escalonadores customizados de CPU baseados em eBPF) para cargas de trabalho sensíveis à latência de cauda.
- [ ] Concepção de protocolo de consenso determinístico para snapshots distribuídos em clusters de baixa conectividade.
- [ ] Elaboração de artigos técnicos aprofundados sobre o funcionamento de alocadores de memória no Linux e ajuste de buffers de socket.

---

## 📬 Contato e Conexões

- **Localização**: Seul, Coreia do Sul (UTC+9)
- **Estações de Trabalho Principais**: Arch Linux (Kernel 6.12+ LTS) / Servidor Debian GNU/Linux
- **E-mail**: `hyojunjo670@gmail.com`
- **GitHub**: [`hyojunjo670-prog`](https://github.com/hyojunjo670-prog)
- **Impressão Digital da Chave GPG**: `9D4F 21B0 E77C 4531 82A4 03E9 61F2 BC34 7E0A 5B22`

---

```text
[EOF] Construído com rigor, precisão e respeito pelo hardware.
```

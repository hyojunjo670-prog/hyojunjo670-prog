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
  Ingénieur Systèmes et Infrastructures Distribuées · Séoul, Corée du Sud
```

> "Sympathie mécanique, latence déterministe et rigueur dans l'efficacité des ressources."

Je suis un ingénieur logiciel spécialisé dans les systèmes et les infrastructures distribuées, dédié à la conception de logiciels à faible surcoût et au comportement hautement prévisible. Mes travaux portent sur les pipelines de données à très faible latence, les algorithmes de concurrence sans verrou (lock-free), les sous-systèmes du noyau Linux (`eBPF` / `io_uring`) et les moteurs de stockage embarqués où chaque microseconde et chaque ligne de cache CPU sont déterminantes.

---

## 🧭 Philosophie d'Ingénierie

- **Sympathie Mécanique (Mechanical Sympathy)** : Concevoir les logiciels en harmonie avec le matériel physique sous-jacent : lignes de cache L1/L2/L3, taux de succès du TLB, topologie mémoire NUMA et logique des prédicteurs de branchement.
- **Latence Prévisible plutôt que Débit de Crête** : Un système maintenant une latence stable au 99,9e centile (p99.9) sous forte charge est infiniment supérieur à un système affichant un débit moyen élevé mais sujet à d'importants pics imprévisibles.
- **Zéro Allocation Dynamique sur les Chemins Critiques (Zero-Allocation)** : Les boucles d'exécution critiques ne doivent jamais déclencher d'allocation mémoire sur le tas, de dispatching dynamique ou de changements de contexte d'appels système inutiles.
- **Sobriété en Dépendances Externes** : Refuser les arbres de dépendances pléthoriques et fragiles au profit de bases POSIX solides et des bibliothèques standard du langage.

---

## 🛠️ Arsenal Technique Principal

### Langages de Programmation et Compilateurs

| Technologie | Niveau de Maîtrise | Principaux Cas d'Usage |
| :--- | :--- | :--- |
| **Rust** | Production Quotidienne | Moteurs système sûrs en mémoire, runtimes asynchrones, abstractions système |
| **C (C11 / GNU99)** | Production Quotidienne | Modules noyau Linux, programmes eBPF, programmation système POSIX bas niveau |
| **C++ (C++20)** | Avancé | Structures de données lock-free haute performance, vectorisation SIMD |
| **Go** | Avancé | Plans de contrôle distribués, services RPC à haut débit, mise en réseau cloud |
| **POSIX Shell / Bash** | Compétent | Scripts de déploiement en production, pipelines de compilation du noyau, chaînes CI/CD |
| **Python** | Compétent | Outils d'analyse de benchmarks, vérification de régression, modélisation de performance |

### Systèmes, Noyau et Architecture Bas Niveau

| Domaine | Domaines d'Expertise et Technologies |
| :--- | :--- |
| **Internes du Noyau Linux** | eBPF (XDP / TC / tracepoints), `io_uring`, Threads POSIX, cgroups v2, epoll |
| **Mémoire et Matériel** | Allocation adaptée NUMA, optimisation jemalloc, alignement cache (`64 octets`), anneaux lock-free |
| **Architecture de Stockage** | Moteurs LSM-Tree, Write-Ahead Logging (WAL), compactage SSTable, mmap Zero-Copy |
| **Réseaux et Protocoles** | Optimisation sockets TCP/UDP basse latence, transport QUIC, sérialisation FlatBuffers / Protobuf |

---

## 🚀 Principaux Projets Open-Source

### 1. `pulse-ring` — Anneau Circulaire Sans Verrou à Très Haut Débit
- **Langage** : Rust
- **Description** : Tampon circulaire borné SPMC (Single-Producer Multi-Consumer) et MPMC (Multi-Producer Multi-Consumer) avec remplissage de ligne de cache et ordre mémoire atomique (`Acquire` / `Release`), optimisé pour la distribution d'événements à l'échelle de la sous-microseconde.
- **Points Forts** :
  - Zéro allocation dynamique en cours de transit ; aucune contention mutex grâce aux opérations atomiques pures.
  - Testé à plus de 48 millions de messages/seconde sur processeurs modernes x86_64 AVX-512.

### 2. `aether-kv` — Moteur Clé-Valeur Embarqué Basé sur LSM-Tree
- **Langage** : C++20 / C
- **Description** : Moteur de stockage clé-valeur embarqué conçu pour les disques SSD NVMe, exploitant Linux `io_uring` pour des opérations d'E/S directes et asynchrones par lots (`O_DIRECT`).
- **Points Forts** :
  - Journal de pré-écriture (WAL) segmenté avec sommes de contrôle CRC32 garantissant l'intégrité après panne.
  - Compactage étagé à deux niveaux minimisant l'amplification d'écriture tout en maintenant la latence de lecture p99 sous les 120 microsecondes.

### 3. `packet-lens` — Sonde de Télémétrie eBPF en Espace Noyau
- **Langage** : C / Go
- **Description** : Démon d'observabilité réseau ultra-léger attaché aux points d'ancrage eBPF XDP/TC, extrayant le temps d'aller-retour TCP (RTT), les pertes de paquets et l'état des retransmissions sans surcoût CPU notable.
- **Points Forts** :
  - Fonctionne entièrement dans l'espace noyau ; transfère les métriques via des tampons circulaires sans copier les paquets bruts vers l'espace utilisateur.
  - Exportateur de métriques natif pour Prometheus avec alertes de dégradation réseau à la milliseconde.

### 4. `flow-rpc` — Framework RPC Binaire Zéro-Copie (Zero-Copy)
- **Langage** : Rust / Go
- **Description** : Framework RPC binaire minimaliste conçu pour éliminer les goulots d'étranglement liés à la désérialisation JSON/Protobuf sur les sockets de domaine Unix et les liaisons TCP haut débit.
- **Points Forts** :
  - Mappage direct de la mémoire tampon sans copie additionnelle grâce aux schémas FlatBuffers.
  - Moteur de multiplexage intégré gérant jusqu'à 100 000 connexions persistantes simultanées par cœur de traitement.

---

## 📊 Schéma d'Architecture Système

```text
+-------------------------------------------------------------------------+
|                       Applications en Espace Utilisateur                |
|        [ pulse-ring SPMC ] <---> [ flow-rpc ] <---> [ aether-kv ]       |
+-------------------------------------------------------------------------+
                                    |
                        (E/S Directe Asynchrone via io_uring)
                                    v
+-------------------------------------------------------------------------+
|                        Sous-Système du Noyau Linux                      |
|   +-------------------+    +--------------------+    +--------------+   |
|   |   Hook eBPF XDP   |    |  Couche VFS / Bloc |    | Cache Page   |   |
|   |  ( packet-lens )  |    |  ( Async Direct )  |    | ( Contourné) |   |
|   +-------------------+    +--------------------+    +--------------+   |
+-------------------------------------------------------------------------+
                                    |
                                    v
+-------------------------------------------------------------------------+
|                           Matériel Physique                             |
|       [ Stockage NVMe ]    [ Carte PCIe Gen4 ]   [ Mémoire DDR5 NUMA ]  |
+-------------------------------------------------------------------------+
```

---

## 📈 Recherches Actuelles et Feuille de Route

- [ ] Recherche sur le décodage accéléré par SIMD (`AVX-512` et `NEON`) d'entiers à longueur variable (varint) pour paquets binaires.
- [ ] Expérimentations avec Linux 6.x sched-ext (ordonnanceurs CPU personnalisés via eBPF) pour les charges sensibles aux latences de queue.
- [ ] Conception d'un protocole de consensus déterministe pour instantanés distribués sur grappes à faible bande passante.
- [ ] Rédaction d'articles techniques approfondis sur les allocations mémoire sous Linux et le paramétrage des tampons réseau du noyau.

---

## 📬 Contact et Échanges

- **Localisation** : Séoul, Corée du Sud (UTC+9)
- **Environnements Principaux** : Arch Linux (Noyau 6.12+ LTS) / Serveur Debian GNU/Linux
- **Courriel** : `hyojunjo670@gmail.com`
- **GitHub** : [`hyojunjo670-prog`](https://github.com/hyojunjo670-prog)
- **Empreinte de Clé GPG** : `9D4F 21B0 E77C 4531 82A4 03E9 61F2 BC34 7E0A 5B22`

---

```text
[EOF] Bâti avec rigueur, précision et respect pour le matériel.
```

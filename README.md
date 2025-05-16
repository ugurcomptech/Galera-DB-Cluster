# Galera MariaDB Cluster (3 Node Setup)

Bu proje, **3 düğümlü (node)** bir Galera Cluster kurulumu gerçekleştirmek isteyen sistem yöneticileri ve geliştiriciler için hazırlanmıştır. MariaDB veritabanı, Galera ile senkronize çalışarak yüksek erişilebilirlik (HA) ve veri tutarlılığı sağlar.

---

## 🧩 Mimaride Kullanılan Bileşenler

- **Galera**: Synchronous Multi-Master Replication.
- **MariaDB 10.6.x**: MySQL uyumlu, gelişmiş açık kaynaklı veritabanı.
- **rsync**: SST (State Snapshot Transfer) için tercih edilen yöntem.
- **Ubuntu 22.04 LTS**: Tüm node’lar için önerilen işletim sistemi.

---

## 🌐 Cluster Yapılandırması

| Node  | IP Adresi   | Hostname |
|-------|-------------|----------|
| Node1 | 10.0.0.1    | node1    |
| Node2 | 10.0.0.2    | node2    |
| Node3 | 10.0.0.3    | node3    |

> Cluster ismi: `galera_cluster`

---

## 📊 Galera Cluster Mimarisi

```mermaid
flowchart LR
    A[Node1 - 10.0.0.1] <--> B[Node2 - 10.0.0.2]
    B <--> C[Node3 - 10.0.0.3]
    C <--> A
    subgraph Galera Cluster
        A
        B
        C
    end
````

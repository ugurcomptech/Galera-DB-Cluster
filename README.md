# Galera MariaDB Cluster (3 Node Setup)

Bu proje, **3 düğümlü (node)** bir Galera Cluster kurulumu gerçekleştirmek isteyen sistem yöneticileri ve geliştiriciler için hazırlanmıştır. MariaDB veritabanı, Galera ile senkronize çalışarak yüksek erişilebilirlik (HA) ve veri tutarlılığı sağlar.

## 🧩 Mimaride Kullanılan Bileşenler

- **Galera**: Synchronous Multi-Master Replication.
- **MariaDB 10.6.x**: MySQL uyumlu, gelişmiş açık kaynaklı veritabanı.
- **rsync**: SST (State Snapshot Transfer) için tercih edilen yöntem.
- **Ubuntu 22.04 LTS**: Tüm node’lar için önerilen işletim sistemi.

## 🌐 Cluster Yapılandırması

| Node  | IP Adresi   | Hostname |
|-------|-------------|----------|
| Node1 | 10.0.0.1    | node1    |
| Node2 | 10.0.0.2    | node2    |
| Node3 | 10.0.0.3    | node3    |

## 📊 Galera Cluster Mimarisi

```mermaid
flowchart TD
    subgraph Galera Cluster
        Node1["🖥️ Node 1\nIP: 10.0.0.1"]
        Node2["🖥️ Node 2\nIP: 10.0.0.2"]
        Node3["🖥️ Node 3\nIP: 10.0.0.3"]
    end

    subgraph Replikasyon Türü
        SST["📦 SST\n(State Snapshot Transfer)"]
        IST["🔁 IST\n(Incremental State Transfer)"]
    end

    subgraph Diğer Bileşenler
        Quorum["🔗 Quorum\n(>50% aktif node)"]
        Provider["📚 Galera Provider\n(libgalera_smm.so)"]
    end

    Client1["👤 Client"] --> Node1
    Client2["👤 Client"] --> Node2
    Client3["👤 Client"] --> Node3

    Node1 <--> Node2
    Node2 <--> Node3
    Node3 <--> Node1

    Node1 --> SST
    Node2 --> IST

    Node1 --> Quorum
    Node2 --> Quorum
    Node3 --> Quorum

    Node1 --> Provider
    Node2 --> Provider
    Node3 --> Provider

    classDef node fill:#e0f7fa,stroke:#00796b,stroke-width:2px;
    classDef logic fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    class Node1,Node2,Node3 node;
    class SST,IST,Quorum,Provider logic;

```

## ⚙️ Örnek `my.cnf` Yapılandırması

(README içeriğinde tekrar yazılmadı, `node1/`, `node2/`, `node3/` klasörlerinde mevcut.)

## 🔧 Kurulum Adımları

1. **MariaDB ve Galera kurulumu:**
```bash
sudo apt update
sudo apt install mariadb-server galera-4 rsync
```

2. **Yapılandırma dosyasını düzenle.**

3. **Firewall Ayarları (Tüm Node'larda):**
```bash
sudo ufw allow 3306/tcp
sudo ufw allow 4567/tcp
sudo ufw allow 4568/tcp
sudo ufw allow 4444/tcp
```

4. **İlk Node'da Cluster başlat:**
```bash
sudo galera_new_cluster
```

5. **Diğer Node'ları başlat:**
```bash
sudo systemctl start mariadb
```

## 🩺 Kontrol Komutu

```bash
mysql -u root -p -e "SHOW STATUS LIKE 'wsrep_cluster_size';"
```

## 🛡️ Lisans

MIT License

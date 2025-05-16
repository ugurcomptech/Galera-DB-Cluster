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
    subgraph Galera_Cluster
        N1["Node1\n10.0.0.1"]
        N2["Node2\n10.0.0.2"]
        N3["Node3\n10.0.0.3"]
    end

    subgraph Clients
        C1["Client App 1"]
        C2["Client App 2"]
    end

    C1 --> N1
    C2 --> N2

    N1 <--> N2
    N2 <--> N3
    N3 <--> N1

    SST["SST"]
    IST["IST"]
    Quorum["Quorum > 50%"]
    Provider["Galera Provider"]

    N1 --> SST
    N2 --> IST
    N1 --> Quorum
    N2 --> Quorum
    N3 --> Quorum
    N1 --> Provider
    N2 --> Provider
    N3 --> Provider

    note right of SST
        State Snapshot Transfer:\n
        Yeni node eklendiğinde\ngüncel veriler tam olarak aktarılır.
    end

    note right of IST
        Incremental State Transfer:\n
        Eksik güncellemeler küçük parçalar\nşeklinde iletilir.
    end

    note bottom of Quorum
        Cluster’ın çalışabilmesi için\nnode’ların çoğunluğu gerekir.
    end

    note bottom of Provider
        Galera replication’ı sağlayan\nlibgalera_smm.so eklentisidir.
    end

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

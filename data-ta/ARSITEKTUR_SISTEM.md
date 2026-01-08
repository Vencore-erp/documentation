# Penjelasan Arsitektur Sistem

```mermaid
graph TB
    subgraph "Frontend Layer"
        WEB["Web Portal<br/>(Next.js 14)"]
        VENDOR["Vendor Portal<br/>(Next.js 14)"]
        MOBILE["Mobile App<br/>(React Native)"]
    end

    subgraph "API Gateway"
        GW["Spring Cloud Gateway<br/>• JWT Auth<br/>• Rate Limit<br/>• Load Balance"]
    end

    subgraph "Core Services"
        AUTH["Auth Service<br/>(Spring Boot)"]
        USER["User Service<br/>(Spring Boot)"]
        PROC["Procurement Service<br/>(Spring Boot)"]
        VEND["Vendor Service<br/>(Spring Boot)"]
        FIN["Finance Service<br/>(Spring Boot)"]
    end

    subgraph "Supporting Services"
        NOTIF["Notification Service"]
        AUDIT["Audit Service"]
        DOC["Document Service"]
        INV["Inventory Service"]
        REPORT["Reporting Service"]
    end

    subgraph "Event Bus"
        KAFKA["Apache Kafka"]
    end

    subgraph "Data Stores"
        PG["PostgreSQL"]
        REDIS["Redis Cache"]
        MINIO["MinIO<br/>(Object Storage)"]
        ES["Elasticsearch"]
    end

    WEB --> GW
    VENDOR --> GW
    MOBILE --> GW

    GW --> AUTH
    GW --> USER
    GW --> PROC
    GW --> VEND
    GW --> FIN

    AUTH --> KAFKA
    PROC --> KAFKA
    FIN --> KAFKA
    VEND --> KAFKA

    KAFKA --> NOTIF
    KAFKA --> AUDIT
    KAFKA --> REPORT

    AUTH --> PG
    USER --> PG
    PROC --> PG
    VEND --> PG
    FIN --> PG

    AUTH --> REDIS
    DOC --> MINIO
    AUDIT --> ES
```

**Lapisan Presentasi dan Gerbang API (Frontend & Gateway)**
Sistem ini dirancang menggunakan arsitektur *microservices* yang modern dan terdistribusi. Pada lapisan presentasi (*User Interface*), terdapat pemisahan yang jelas antara **Web Portal** untuk pengguna internal, **Vendor Portal** untuk rekanan, dan **Mobile App** untuk akses on-the-go, di mana semuanya dibangun menggunakan teknologi terkini seperti Next.js 14 dan React Native. Seluruh akses dari klien-klien ini tidak langsung menyentuh layanan backend, melainkan melalui **API Gateway** (Spring Cloud Gateway) yang berfungsi sebagai pintu gerbang tunggal. Gateway ini bertugas menangani keamanan terpusat (JWT Authentication), pembatasan akses (*rate limiting*), dan penyeimbangan beban (*load balancing*) untuk memastikan keamanan dan stabilitas sistem sebelum permintaan diteruskan ke layanan di belakangnya.

**Layanan Inti dan Komunikasi <i>Event-Driven</i>**
Logika bisnis utama dipecah menjadi beberapa **Core Services** independen berbasis Spring Boot, meliputi layanan Autentikasi, User/Role, Pengadaan (Procurement), Vendor, dan Keuangan. Untuk menjaga performa dan independensi antar-layanan (*decoupling*), sistem menerapkan pola komunikasi *asynchronous* menggunakan **Apache Kafka** sebagai *Event Bus*. Ketika terjadi transaksi penting (seperti 'PR Disetujui' atau 'Invoice Masuk'), layanan inti akan mempublikasikan *event* ke Kafka. Layanan pendukung (*Supporting Services*) seperti Notifikasi, Audit, dan Pelaporan kemudian akan mengonsumsi *event* tersebut secara terpisah. Hal ini memastikan proses berat seperti pengiriman email atau pencatatan log audit tidak memperlambat respon transaksi utama pengguna.

**Strategi Penyimpanan Data (Data Persistence)**
Untuk pengelolaan data, sistem menerapkan strategi *polyglot persistence* yang memilih teknologi penyimpanan terbaik sesuai kebutuhan kasus penggunaan. **PostgreSQL** digunakan sebagai database relasional utama untuk menjamin integritas data transaksional pada setiap *microservice*. Sistem juga memanfaatkan **Redis** sebagai lapisan *caching* berkecepatan tinggi untuk mengurangi beban database, **MinIO** sebagai *Object Storage* yang aman untuk penyimpanan dokumen fisik (seperti file kontrak atau faktur), serta **Elasticsearch** untuk kebutuhan pencarian teks cepat dan agregasi log audit. Kombinasi infrastruktur ini menciptakan ekosistem penyimpanan yang skalabel, andal, dan efisien.

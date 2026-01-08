# 4.6.2 Perancangan Arsitektur

## 4.6.2.1 Tinjauan Umum Arsitektur

Arsitektur sistem e-Procurement ini dirancang menggunakan pendekatan *Microservices* yang terdistribusi, memungkinkan skalabilitas tinggi dan pemisahan tanggung jawab yang jelas antar modul. Sistem terdiri dari tiga lapisan utama:
1.  **Frontend Layer**: Antarmuka pengguna yang terbagi menjadi Web Portal, Vendor Portal, dan Mobile App.
2.  **API Gateway**: Gerbang tunggal yang menangani autentikasi, load balancing, dan *routing*.
3.  **Backend Services**: Kumpulan layanan mikro (Auth, User, Procurement, Vendor, Finance) yang berkomunikasi secara *asynchronous* melalui Event Bus (Kafka).

Berikut adalah gambaran visual arsitektur sistem secara keseluruhan:

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

---

## 4.6.2.2 Arsitektur Perangkat Lunak (Package Diagram)

Diagram paket berikut menggambarkan struktur modular internal dari sistem *Monorepo* atau *Multi-repo* microservices. Setiap layanan memiliki struktur layer yang seragam (Controller, Service, Repository) sesuai dengan standar *Clean Architecture* atau *Layered Architecture* pada Spring Boot.

```mermaid
graph TB
    subgraph Frontend_Package [Frontend Application]
        direction TB
        NextJS([NextJS App])
        subgraph FE_Components [Components]
            Pages
            Hooks
            Utils
        end
        NextJS --- FE_Components
    end

    subgraph Gateway_Package [API Gateway Layer]
        direction TB
        SCG([Spring Cloud Gateway])
        subgraph GW_Components [Gateway Modules]
            RouteLocator
            GlobalFilter
            JwtAuthFilter
        end
        SCG --- GW_Components
    end

    subgraph Core_Package [Core Business Services]
        direction TB
        Auth([Auth Service])
        Proc([Procurement Service])
        Vend([Vendor Service])
        Fin([Finance Service])
    end

    subgraph Shared_Package [Shared Libraries]
        direction TB
        Common([Common Lib])
        subgraph Lib_Components [Library Modules]
            GlobalExceptionHandler
            BaseEntity
            ApiResponse
            KafkaProducer
        end
        Common --- Lib_Components
    end

    %% Relationships
    NextJS -.->|HTTP/REST| SCG
    SCG -.->|Proxy| Auth
    SCG -.->|Proxy| Proc
    SCG -.->|Proxy| Vend
    SCG -.->|Proxy| Fin

    Auth --> Common
    Proc --> Common
    Vend --> Common
    Fin --> Common
```

Struktur paket di atas menunjukkan bahwa setiap microservice (Auth, Procurement, Vendor, Finance) berdiri sendiri namun berbagi pustaka umum (`Common Lib`) untuk menangani hal-hal standar seperti format respons API (`ApiResponse`), penanganan error global (`GlobalExceptionHandler`), dan utilitas logging audit.

---

## 4.6.2.3 Class Diagram

Berikut adalah rancangan detail kelas (Class Diagram) untuk setiap layanan utama, yang menggambarkan atribut data dan operasi (metode) yang dimiliki oleh setiap entitas bisnis.

### 1. Auth Service & User Management
```mermaid
classDiagram
    class User {
        -UUID id
        -String username
        -String email
        -String passwordHash
        -Boolean isActive
        -Boolean isMfaEnabled
        +login()
        +logout()
    }
    class Role {
        -UUID id
        -String name
        -Boolean isSystemRole
    }
    class Permission {
        -UUID id
        -String code
    }
    class UserRole {
        -UUID userId
        -UUID roleId
    }
    class UserProfile {
        -UUID userId
        -String employeeId
        -String fullName
        -UUID departmentId
        -UUID managerId
    }
    class Department {
        -UUID id
        -String name
        -UUID parentId
    }

    User "1" --> "*" UserRole
    Role "1" --> "*" UserRole
    Role "1" --> "*" Permission
    UserProfile "1" -- "1" User : extends
    Department "1" --> "*" UserProfile : employs
```

### 2. Procurement Service (Core)
```mermaid
classDiagram
    class PurchaseRequisition {
        -UUID id
        -String prNumber
        -UUID requesterId
        -BigDecimal totalAmount
        -String status
        +submit()
        +approve()
    }
    class PRItem {
        -UUID prId
        -String description
        -BigDecimal quantity
        -BigDecimal unitPrice
    }
    class RFQ {
        -UUID id
        -UUID prId
        -String rfqNumber
        -LocalDateTime closeDate
        -String status
        +publish()
        +award()
    }
    class Quotation {
        -UUID id
        -UUID rfqId
        -UUID vendorId
        -BigDecimal totalAmount
        +submit()
    }
    class PurchaseOrder {
        -UUID id
        -String poNumber
        -UUID quotationId
        -String status
        +issue()
    }

    PurchaseRequisition "1" --> "*" PRItem : contains
    PurchaseRequisition "1" --> "1" RFQ : generates
    RFQ "1" --> "*" Quotation : receives
    Quotation "1" --> "0..1" PurchaseOrder : wins
```

### 3. Vendor Service
```mermaid
classDiagram
    class Vendor {
        -UUID id
        -String companyName
        -String taxId
        -String status
        -Boolean isBlacklisted
        +register()
        +verify()
    }
    class VendorDocument {
        -UUID vendorId
        -String type
        -LocalDate expiryDate
    }
    class VendorScorecard {
        -UUID vendorId
        -Integer overallScore
    }

    Vendor "1" --> "*" VendorDocument : uploads
    Vendor "1" --> "*" VendorScorecard : has
```

### 4. Finance Service
```mermaid
classDiagram
    class Invoice {
        -UUID id
        -UUID poId
        -BigDecimal amount
        -String status
        +submit()
        +match3Way()
    }
    class Payment {
        -UUID invoiceId
        -BigDecimal amount
        -LocalDate paymentDate
        -String status
        +execute()
    }
    class BudgetAllocation {
        -UUID costCenterId
        -BigDecimal totalBudget
        -BigDecimal utilized
        +checkAvailability()
        +lockFunds()
    }

    Invoice "1" --> "1" Payment : triggers
```

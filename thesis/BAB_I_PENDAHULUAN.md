# BAB I: PENDAHULUAN

> **Judul Tugas Akhir:** Pembangunan Sistem Internal Pengadaan Barang dan Jasa PT XYZ Menggunakan Arsitektur Microservices

---

## 1.1 Latar Belakang

PT XYZ merupakan salah satu institusi keuangan terkemuka di Indonesia yang beroperasi di sektor perbankan. Seiring dengan pertumbuhan bisnis dan kompleksitas operasional, PT XYZ menghadapi tantangan signifikan dalam mengelola proses pengadaan barang dan jasa secara efisien, transparan, dan sesuai dengan regulasi yang berlaku.

Saat ini, proses pengadaan di PT XYZ masih menggunakan sistem semi-manual yang menimbulkan berbagai permasalahan:

1. **Risiko Fraud**: Kurangnya kontrol otomatis dalam validasi vendor dan persetujuan transaksi
2. **Inefisiensi Operasional**: Siklus pengadaan yang memakan waktu rata-rata 30-45 hari kerja
3. **Kurangnya Audit Trail**: Kesulitan dalam melacak riwayat persetujuan dan perubahan dokumen
4. **Fragmentasi Data**: Sistem yang terpisah antara pengadaan, keuangan, dan manajemen vendor

Untuk mengatasi permasalahan tersebut, PT XYZ membutuhkan sistem e-Procurement internal yang mampu:
- Menegakkan **Segregation of Duties (SoD)** guna mencegah konflik kepentingan
- Memastikan **audit trail** yang lengkap untuk setiap transaksi
- Mengotomatisasi proses verifikasi vendor dan pemeriksaan blacklist
- Menyediakan visibilitas real-time terhadap penggunaan anggaran

### 1.1.1 Perkembangan Teknologi Microservices

Arsitektur microservices telah menjadi standar industri untuk pengembangan sistem enterprise yang scalable dan maintainable. Berbeda dengan arsitektur monolitik tradisional, microservices memungkinkan:

| Aspek | Monolitik | Microservices |
|:---|:---|:---|
| Deployment | Seluruh aplikasi | Per service independen |
| Scaling | Horizontal seluruh app | Scaling granular per service |
| Teknologi | Single stack | Polyglot |
| Fault Tolerance | Single point of failure | Isolated failure |
| Team Development | Bottleneck | Parallel development |

### 1.1.2 Event-Driven Architecture

Untuk sistem yang membutuhkan loose coupling dan asynchronous processing, Event-Driven Architecture (EDA) menawarkan keunggulan:

- **Decoupling**: Services tidak perlu mengetahui implementasi service lain
- **Scalability**: Event consumers dapat di-scale independen
- **Auditability**: Event log menjadi audit trail natural
- **Resilience**: Sistem tetap berjalan meski beberapa service down

---

## 1.2 Rumusan Masalah

Berdasarkan latar belakang yang telah diuraikan, penelitian ini merumuskan permasalahan sebagai berikut:

1. **Bagaimana merancang arsitektur sistem e-Procurement berbasis microservices yang scalable dan maintainable?**

2. **Bagaimana mengimplementasikan event-driven architecture untuk memastikan loose coupling antar services dan audit trail yang komprehensif?**

3. **Bagaimana menerapkan kontrol keamanan sesuai standar ISO 27001 dan SOX dalam sistem e-Procurement?**

4. **Bagaimana membangun workflow approval yang mendukung Segregation of Duties (SoD) dan prinsip Four-Eyes?**

5. **Bagaimana mengintegrasikan validasi vendor dengan pemeriksaan KYC/AML dan daftar hitam (Do Not Transact)?**

---

## 1.3 Batasan Masalah

Untuk memfokuskan penelitian, ditetapkan batasan-batasan sebagai berikut:

### 1.3.1 Ruang Lingkup Fungsional (In-Scope)

| Modul | Fungsi |
|:---|:---|
| **Authentication & Authorization** | Login, MFA, RBAC, Session Management |
| **User Management** | CRUD User Internal, External, Vendor |
| **Procurement** | Purchase Requisition, RFQ, Purchase Order |
| **Vendor Management** | Registrasi, Kualifikasi, KYC, Blacklist |
| **Finance** | Invoice Verification, 3-Way Match, Payment |
| **Approval Workflow** | Multi-level Approval, Delegation, Escalation |
| **Audit & Compliance** | Audit Trail, Reporting, Compliance Checks |

### 1.3.2 Diluar Ruang Lingkup (Out-of-Scope)

1. Integrasi langsung dengan Core Banking System (CBS) - hanya spesifikasi interface
2. Modul Human Resource (HR) - Penggajian dan Kepegawaian
3. Physical Inventory Management - Gudang dan Logistik
4. Mobile Native Application - Fokus pada Web Progressive

---

## 1.4 Tujuan Penelitian

### 1.4.1 Tujuan Umum

Membangun sistem internal pengadaan barang dan jasa untuk PT XYZ menggunakan arsitektur microservices yang scalable, maintainable, dan memenuhi kebutuhan audit perusahaan.

### 1.4.2 Tujuan Khusus

1. **Merancang arsitektur microservices** yang terdiri dari 11+ services dengan separation of concerns yang jelas

2. **Mengimplementasikan event-driven communication** menggunakan Apache Kafka untuk memastikan loose coupling dan audit trail

3. **Membangun sistem Role-Based Access Control (RBAC)** yang mendukung Segregation of Duties (SoD)

4. **Mengembangkan workflow approval engine** yang fleksibel dengan dukungan:
   - Multi-level approval berdasarkan threshold
   - Approval delegation
   - Automatic escalation
   - Four-Eyes principle untuk transaksi kritikal

5. **Mengimplementasikan integrasi vendor management** dengan:
   - Automated KYC verification
   - Real-time blacklist checking
   - Vendor performance scoring

6. **Membangun dashboard analytics** untuk monitoring real-time procurement metrics

---

## 1.5 Manfaat Penelitian

### 1.5.1 Manfaat Teoritis

1. Memberikan kontribusi pada pengembangan body of knowledge mengenai implementasi microservices dalam domain enterprise procurement

2. Menyediakan referensi arsitektur untuk event-driven systems yang memenuhi compliance requirements perbankan

3. Menghasilkan best practices dalam penerapan ISO 27001 controls pada aplikasi berbasis microservices

### 1.5.2 Manfaat Praktis

**Bagi PT XYZ:**
- Mengurangi cycle time procurement dari 30-45 hari menjadi < 15 hari
- Meningkatkan transparansi dan akuntabilitas proses pengadaan
- Mengurangi risiko fraud melalui automated controls
- Memudahkan proses audit internal dan eksternal

**Bagi Pengembang Software:**
- Menyediakan blueprint arsitektur microservices yang dapat di-adopt untuk proyek serupa
- Memberikan referensi implementasi Spring Boot + Next.js

**Bagi Akademisi:**
- Menjadi studi kasus untuk mata kuliah Software Architecture dan Enterprise Systems

---

## 1.6 Metodologi Penelitian

Penelitian ini menggunakan metodologi **Design Science Research (DSR)** yang terdiri dari:

```
┌─────────────────────────────────────────────────────────────────┐
│                    DESIGN SCIENCE RESEARCH                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐        │
│  │   Problem    │ → │   Design &   │ → │   Build &    │        │
│  │Identification│   │  Development │   │Demonstration │        │
│  └──────────────┘   └──────────────┘   └──────────────┘        │
│         │                   │                   │               │
│         ▼                   ▼                   ▼               │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────┐        │
│  │  Literature  │   │  Artifact    │   │  Prototype   │        │
│  │    Review    │   │    Design    │   │  Development │        │
│  └──────────────┘   └──────────────┘   └──────────────┘        │
│                                                                  │
│  ┌──────────────┐   ┌──────────────┐                           │
│  │  Evaluation  │ ← │Communication │                           │
│  └──────────────┘   └──────────────┘                           │
│         │                   │                                   │
│         ▼                   ▼                                   │
│  ┌──────────────┐   ┌──────────────┐                           │
│  │   Testing &  │   │   Thesis &   │                           │
│  │  Validation  │   │    Paper     │                           │
│  └──────────────┘   └──────────────┘                           │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 1.6.1 Tahapan Penelitian

| Tahap | Aktivitas | Output |
|:---:|:---|:---|
| 1 | Problem Identification & Literature Review | Problem Statement, Literature Matrix |
| 2 | Requirements Analysis | Use Case Specifications, NFR Document |
| 3 | Architecture Design | System Architecture Document, API Specs |
| 4 | Implementation | Working Prototype |
| 5 | Testing & Evaluation | Test Reports, Performance Metrics |
| 6 | Documentation & Communication | Thesis Document, Publication |

---

## 1.7 Sistematika Penulisan

Penulisan thesis ini disusun dengan sistematika sebagai berikut:

**BAB I: PENDAHULUAN**
Berisi latar belakang, rumusan masalah, batasan masalah, tujuan, manfaat, metodologi, dan sistematika penulisan.

**BAB II: TINJAUAN PUSTAKA**
Berisi landasan teori mengenai e-Procurement, Microservices Architecture, Event-Driven Architecture, ISO 27001, dan penelitian terdahulu yang relevan.

**BAB III: METODOLOGI PENELITIAN**
Berisi detail metodologi Design Science Research, tahapan pengembangan, dan tools yang digunakan.

**BAB IV: ANALISIS DAN PERANCANGAN**
Berisi analisis kebutuhan sistem, perancangan arsitektur, desain database, dan spesifikasi API.

**BAB V: IMPLEMENTASI DAN PENGUJIAN**
Berisi implementasi sistem, screenshot antarmuka, hasil pengujian fungsional dan non-fungsional.

**BAB VI: PENUTUP**
Berisi kesimpulan penelitian dan saran untuk pengembangan lebih lanjut.

**LAMPIRAN**
Berisi dokumentasi teknis lengkap termasuk use case details, database schema, API specifications, dan UI mockups.

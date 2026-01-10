# BAB I: PENDAHULUAN

> **Judul Tugas Akhir:** Pembangunan Sistem Internal Pengadaan Barang dan Jasa PT XYZ Menggunakan Arsitektur Microservices

---

## 1.1 Latar Belakang

PT XYZ adalah perusahaan yang bergerak di bidang keuangan dengan volume operasional yang terus meningkat. Dalam menjalankan aktivitas bisnisnya, proses pengadaan barang dan jasa memegang peranan vital untuk mendukung kelancaran operasional. Namun, saat ini proses pengadaan di PT XYZ masih didominasi oleh pendekatan konvensional yang bersifat semi-manual. Ketergantungan pada komunikasi manual, seperti pertukaran email dan formulir fisik, seringkali menyebabkan hambatan birokrasi, lambatnya alur persetujuan, dan kurangnya transparansi data.

Permasalahan ini berdampak signifikan pada efisiensi waktu, di mana siklus pengadaan menjadi panjang dan status pengajuan sulit untuk dilacak secara real-time. Ketidakefisienan ini tidak hanya memperlambat pemenuhan kebutuhan operasional perusahaan tetapi juga meningkatkan risiko kesalahan manusia (human error) dan redundansi data. Selain itu, dengan sistem yang belum terintegrasi, pemantauan terhadap riwayat transaksi dan evaluasi kinerja pengadaan menjadi sulit dilakukan.

Seiring dengan kebutuhan bisnis yang semakin dinamis, sistem yang ada saat ini dinilai kurang mampu beradaptasi, terutama dalam hal skalabilitas dan kecepatan pemrosesan data. Oleh karena itu, dibutuhkan sebuah solusi sistem e-procurement yang tidak hanya dapat mendigitalkan proses manual tersebut, tetapi juga memiliki fleksibilitas arsitektur untuk menangani kompleksitas proses pengadaan yang terus berkembang. Berdasarkan permasalahan tersebut, penelitian ini mengusulkan pengembangan Proof of Concept (PoC) sistem e-procurement internal yang dirancang untuk mengatasi inefisiensi dan keterbatasan komunikasi manual tersebut.

---

## 1.2 Rumusan Masalah

Berdasarkan latar belakang tersebut, permasalahan yang ingin dijawab dalam penelitian ini adalah:

1. Bagaimana merancang sistem e-procurement yang dapat mempercepat proses pengadaan dan mengurangi ketergantungan pada komunikasi manual?
2. Sejauh mana sistem e-procurement berbasis microservices lebih efektif dibandingkan sistem konvensional dalam meningkatkan efisiensi, skalabilitas, dan transparansi?

## 1.3 Batasan Penelitian

Agar penelitian ini tetap fokus dan terarah, batasan masalah ditetapkan sebagai berikut:

1. Sistem dikembangkan sebagai Proof of Concept (PoC) untuk alur bisnis inti, mencakup manajemen pengguna, katalog, pengajuan permintaan (purchase request), dan pemesanan (purchase order).
2. Sistem dibangun menggunakan arsitektur microservices dengan Spring Boot, Apache Kafka sebagai message broker, dan Next.js untuk antarmuka.
3. Lingkungan pengujian terbatas pada simulasi dengan data uji, belum mencakup penerapan pada sistem produksi berskala penuh.
4. Penelitian ini tidak membahas aspek keamanan tingkat lanjut.
5. Penelitian ini tidak mencakup integrasi langsung dengan sistem core banking PT XYZ atau sistem eksternal lain di luar ruang lingkup pengadaan.

## 1.4 Pertanyaan Penelitian

1) Bagaimana merancang arsitektur microservices untuk sistem e-procurement yang mampu mempercepat proses pengadaan dan mengurangi ketergantungan pada komunikasi manual?
2) Bagaimana mengimplementasikan komunikasi antar layanan menggunakan event bus (Apache Kafka) agar proses pengadaan dapat berjalan lebih terdistribusi, real-time, dan responsif?
3) Bagaimana tingkat efektivitas dan efisiensi sistem e-procurement berbasis microservices dibandingkan dengan sistem konvensional setelah dilakukan pengujian melalui simulasi proses pengadaan?

## 1.5 Tujuan Penelitian

1) Merancang arsitektur sistem e-procurement berbasis microservices yang mampu mempercepat proses pengadaan dan mengurangi ketergantungan pada komunikasi manual.
2) Mengimplementasikan komunikasi antar layanan menggunakan event bus (Apache Kafka) untuk mendukung proses pengadaan yang terdistribusi, real-time, dan responsif.
3) Mengevaluasi efektivitas sistem e-procurement berbasis microservices dibandingkan dengan sistem konvensional, ditinjau dari aspek efisiensi, skalabilitas, dan transparansi melalui pengujian berbasis simulasi.

---

## 1.6 Manfaat Penelitian

### 1.6.1 Manfaat Teoritis

1. Memberikan kontribusi pada pengembangan body of knowledge mengenai implementasi microservices dalam domain enterprise procurement

2. Menyediakan referensi arsitektur untuk event-driven systems yang memenuhi compliance requirements perbankan

3. Menghasilkan best practices dalam penerapan ISO 27001 controls pada aplikasi berbasis microservices

### 1.6.2 Manfaat Praktis

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

## 1.7 Metodologi Penelitian

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

### 1.7.1 Tahapan Penelitian

| Tahap | Aktivitas | Output |
|:---:|:---|:---|
| 1 | Problem Identification & Literature Review | Problem Statement, Literature Matrix |
| 2 | Requirements Analysis | Use Case Specifications, NFR Document |
| 3 | Architecture Design | System Architecture Document, API Specs |
| 4 | Implementation | Working Prototype |
| 5 | Testing & Evaluation | Test Reports, Performance Metrics |
| 6 | Documentation & Communication | Thesis Document, Publication |

---

## 1.8 Sistematika Penulisan

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

# Sprint Plan 5: Integration & Finalization
**Goal:** Menggabungkan Frontend & Backend menjadi satu sistem utuh (MVP).
**Durasi:** 1-2 Minggu.

---

## 🎯 Objectives
1.  **Frontend Integration:** Ganti Mock dengan Real API.
2.  **Authentication Guard:** Protect routes dengan Real Token check.
3.  **End-to-End Testing.**
4.  **Deployment Final.**

---

## 📋 Task List

### 1. Frontend Integration
- [ ] **Auth:** Login Screen -> POST `/api/auth/login`. Simpan Token di Cookie/LocalStorage.
- [ ] **Middleware:** Validasi Token di setiap request Next.js (Server Component).
- [ ] **API Clients:** Replace `setTimeout` mocks dengan `axios` calls.
  - [ ] `useContractList` -> GET `/api/contracts`.
  - [ ] `useCreateInvoice` -> POST `/api/invoices`.

### 2. Monitoring & Logging
- [ ] **Setup:** Actuator & Prometheus Exporter di Spring Boot.
- [ ] **Dashboard:** Setup Grafana sederhana (Traffic, Error Rate, JVM Stats).
- [ ] **Logging:** Centralized Logging (ELK Stack atau Simple File Logging).

### 3. Final Testing
- [ ] **UAT 2:** Demo ulang ke user dengan data real backend.
- [ ] **Load Test:** Simple JMeter test (verify concurrency requirements).
- [ ] **Security Scan:** Basic OWASP ZAP scan.

### 4. Documentation & Thesis
- [ ] **Generate:** Swagger/OpenAPI Docs terbaru.
- [ ] **Write:** Bab IV & V Tesis (Implementasi & Pengujian).
- [ ] **Screenshot:** Ambil bukti-bukti layar untuk lampiran.

---

## 🧪 Definition of Done
- [ ] Sistem berjalan End-to-End dari Login -> PR -> PO -> Invoice -> Paid.
- [ ] Tidak ada error critical di Console/Logs.
- [ ] Dokumen Tesis lengkap dengan data hasil pengujian.
- [ ] Siap Sidang! 🎓

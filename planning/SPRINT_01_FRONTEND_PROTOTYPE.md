# Sprint Plan 1: Frontend Prototype Polish
**Goal:** Menyiapkan Frontend untuk Usability Testing (Data Gathering Tesis).
**Durasi:** 3-5 Hari.

---

## 🎯 Objectives
1.  Memastikan flow **Operator** (Termin/Kontrak) lengkap.
2.  Memastikan flow **Finance** (Invoice Entry) berjalan mulus secara visual.
3.  Menambahkan mekanisme **Reset State** agar mudah dites berulang.
4.  Deployment ke Vercel agar bisa diakses remote tester.

---

## 📋 Task List

### 1. Feature: Contract Management (Operator)
- [x] Page: Contract List UI.
- [x] Page: Contract Detail UI (Milestone Table).
- [ ] **Action:** Make "Create Bill" button functional (Mock: Create Draft Invoice).
- [ ] **Page:** Billing Request Form (`/operator/billing/create`).

### 2. Feature: Invoice Entry (Finance)
- [x] Page: Invoice Create Form.
- [ ] **Logic:** Auto-calculate Tax (PPN 11%) saat input amount mock.
- [ ] **Mock:** Submit form -> Muncul di Invoice List (Temporary State).

### 3. Utility: Testing Aid
- [ ] **Component:** `ResetButton` (Clear LocalStorage + Redirect to Login).
- [ ] **Feature:** Pre-fill Login Credentials (biar tester ga capek ngetik).
  - [ ] Klik "Login as Operator" -> Auto fill email/pass.

### 4. Polish & Deployment
- [ ] **Review:** Cek typo bahasa/label.
- [ ] **Deploy:** Push to GitHub & Connector to Vercel/Netlify.

---

## 🏃 Next Step (Immediate)
Jalankan command ini di terminal untuk membuat folder billing (Task 1.3):
```bash
mkdir -p frontend/app/operator/billing/create
```
Dan buat file `page.tsx`-nya.

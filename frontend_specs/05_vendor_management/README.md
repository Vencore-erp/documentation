# Modul Vendor Management

## Deskripsi
Modul untuk mengelola data vendor, kualifikasi, dan monitoring performa vendor.

---

## Halaman yang Perlu Dibangun

### 1. Vendor List (`/vendors`)
**Prioritas:** 🟠 P1

**Komponen:**
- Data table dengan advanced filter
- Status filter (Active, Pending, Blacklisted, Inactive)
- Category filter
- Export to Excel
- Add vendor (manual) button

**Columns:**
- Vendor Code, Name, Category, Status, Rating, Last Transaction, Actions

---

### 2. Vendor Detail (`/vendors/:id`)
**Prioritas:** 🟠 P1

**Tabs:**
1. **Profile**
   - Company information
   - Contact details
   - Bank information

2. **Documents**
   - Legal documents (SIUP, TDP, NPWP)
   - Certifications
   - Document expiry alerts

3. **KYC/Compliance**
   - KYC verification status
   - Blacklist/DNT check result
   - Compliance score

4. **Transaction History**
   - POs with this vendor
   - Total transaction value
   - Payment history

5. **Performance**
   - Delivery performance
   - Quality score
   - Response time metrics
   - Rating trend chart

6. **Contracts**
   - Active contracts
   - Contract terms
   - SLA details

---

### 3. Vendor Registration Review (`/vendors/pending`)
**Prioritas:** 🟠 P1

**For Operator/Supervisor to review vendor registrations**

**Komponen:**
- Pending list
- Detail review form
- Document verification checklist
- Approve/Reject with comments

---

### 4. Vendor Qualification (`/vendors/:id/qualification`)
**Prioritas:** 🟡 P2

**Komponen:**
- Qualification criteria checklist
- Score input
- Supporting evidence upload
- Qualification decision

---

### 5. Blacklist Management (`/vendors/blacklist`)
**Prioritas:** 🟡 P2

**Komponen:**
- Blacklisted vendor list
- Blacklist reason
- Duration
- Remove from blacklist (with approval)

---

### 6. Vendor Scorecard (`/vendors/scorecard`)
**Prioritas:** 🟡 P2

**Komponen:**
- Vendor performance ranking
- Filter by category
- Score breakdown
- Download report

---

### 7. Contract Management (`/vendors/:id/contracts`)
**Prioritas:** 🟡 P2

**Komponen:**
- Contract list
- Create new contract
- Contract detail with milestones
- SLA tracking
- Penalty calculation

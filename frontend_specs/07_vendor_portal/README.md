# Modul Vendor Portal

## Deskripsi
Portal khusus untuk Vendor eksternal mengakses sistem, submit quotation, dan mengelola transaksi mereka.

---

## Halaman yang Perlu Dibangun

### 1. Profile Management

#### 1.1 Vendor Profile (`/vendor/profile`)
**Prioritas:** 🔴 P0

**Komponen:**
- Company information (editable)
- Contact persons management
- Bank account information
- Document upload/renewal

**Tabs:**
1. Company Info
2. Contact Persons
3. Bank Accounts
4. Documents

#### 1.2 Profile Completion Wizard (`/vendor/profile/complete`)
**Untuk vendor baru yang belum lengkap**
- Step-by-step completion guide
- Required vs optional fields
- Progress percentage

---

### 2. RFQ & Quotation

#### 2.1 RFQ Invitations (`/vendor/rfq`)
**Prioritas:** 🔴 P0

**Komponen:**
- List of RFQ invitations
- Deadline countdown
- Participation status (Interested, Declined, Submitted)

**Columns:**
- RFQ Number, Title, Deadline, Items Count, Status, Actions

#### 2.2 RFQ Detail (`/vendor/rfq/:id`)
**Komponen:**
- RFQ requirements
- Items list
- Terms & conditions
- Question/Clarification section
- Accept/Decline invitation

#### 2.3 Submit Quotation (`/vendor/rfq/:id/quote`)
**Komponen:**
- Item pricing form
- Terms proposal
- Delivery timeline
- Supporting documents
- Bid submission confirmation

#### 2.4 My Quotations (`/vendor/quotations`)
**Komponen:**
- Submitted quotations list
- Status tracking (Under Review, Won, Lost)
- Award notifications

---

### 3. Purchase Orders

#### 3.1 PO List (`/vendor/po`)
**Prioritas:** 🔴 P0

**Komponen:**
- Active POs
- Filter by status
- Acknowledge action

**Columns:**
- PO Number, Date, Items, Total, Delivery Date, Status

#### 3.2 PO Detail (`/vendor/po/:id`)
**Komponen:**
- PO information
- Line items
- Delivery instructions
- Acknowledge/Reject
- Delivery status update
- Advance Shipping Notice (ASN) submission

---

### 4. Invoice & Payment

#### 4.1 Invoice List (`/vendor/invoices`)
**Prioritas:** 🟠 P1

**Komponen:**
- Submitted invoices
- Status tracking (Submitted, Verified, Paid)

#### 4.2 Create Invoice (`/vendor/invoices/new`)
**Komponen:**
- Select PO/GR
- Invoice details
- Tax calculation
- Invoice document upload
- Submit for verification

#### 4.3 Payment History (`/vendor/payments`)
**Komponen:**
- Payment receipts
- Download remittance

---

### 5. Dispute Management

#### 5.1 My Disputes (`/vendor/disputes`)
**Komponen:**
- Open disputes
- Create new dispute
- Resolution status

#### 5.2 Create Dispute (`/vendor/disputes/new`)
**Komponen:**
- Dispute type selection
- Related document (PO, Invoice)
- Description
- Evidence upload

#### 5.3 Dispute Detail (`/vendor/disputes/:id`)
- Dispute timeline
- Communication thread
- Resolution updates

---

### 6. Communication

#### 6.1 Messages (`/vendor/messages`)
**Komponen:**
- Message inbox
- Thread view
- Compose message
- Attachment support

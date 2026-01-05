# Modul Procurement

## Deskripsi
Core module untuk proses pengadaan dari Purchase Requisition (PR) hingga Purchase Order (PO).

---

## Halaman yang Perlu Dibangun

### 1. Purchase Requisition (PR)

#### 1.1 PR List (`/procurement/pr`)
**Komponen:**
- Data table dengan tabs (All, Draft, Pending, Approved, Rejected)
- Search & advanced filter
- Create PR button
- Bulk actions

**Columns:**
- PR Number, Date, Requester, Department, Total, Status, Actions

#### 1.2 Create PR (`/procurement/pr/new`)
**Komponen:**
- Header form (department, urgency, budget code)
- Line items table (add/edit/remove)
- File attachment
- Submit for approval

**Line Item Fields:**
- Item description, quantity, UoM, estimated price, category

#### 1.3 PR Detail (`/procurement/pr/:id`)
**Komponen:**
- Header information (read-only after submit)
- Line items
- Approval history timeline
- Attached documents
- Action buttons (Edit, Submit, Cancel) - conditional

#### 1.4 PR Approval (`/procurement/pr/:id/approve`)
**Supervisor Only**
- PR details (read-only)
- Approval form (approve/reject with comments)
- Budget impact preview
- Previous approver comments

---

### 2. Request for Quotation (RFQ)

#### 2.1 RFQ List (`/procurement/rfq`)
**Komponen:**
- Data table dengan status filter
- Create RFQ button

**Columns:**
- RFQ Number, Title, Deadline, Invited Vendors, Responses, Status

#### 2.2 Create RFQ (`/procurement/rfq/new`)
**Komponen:**
- RFQ header (title, description, deadline)
- Items from approved PR
- Vendor selection (multi-select)
- Terms & conditions
- Bid type selection (Open/Sealed)

#### 2.3 RFQ Detail (`/procurement/rfq/:id`)
**Komponen:**
- RFQ information
- Invited vendors list
- Received quotations summary
- Comparison table (after deadline)
- Award decision

#### 2.4 RFQ Comparison (`/procurement/rfq/:id/compare`)
**Komponen:**
- Side-by-side vendor comparison
- Price per item breakdown
- Total cost comparison
- Terms comparison
- Vendor rating/history

---

### 3. Purchase Order (PO)

#### 3.1 PO List (`/procurement/po`)
**Komponen:**
- Data table dengan status tabs
- Create PO button (from RFQ winner)

**Columns:**
- PO Number, Vendor, Date, Total, Status, GR Status

#### 3.2 Create PO (`/procurement/po/new`)
**Komponen:**
- Vendor selection (from awarded RFQ)
- Auto-populated items
- Delivery terms
- Payment terms
- PO preview

#### 3.3 PO Detail (`/procurement/po/:id`)
**Komponen:**
- PO header & vendor info
- Line items
- Delivery tracking
- Goods Receipt status
- Invoice linkage
- Print/Export PO

#### 3.4 PO Approval (`/procurement/po/:id/approve`)
**Komponen:**
- PO details
- Budget verification status
- Approval decision form

---

### 4. Goods Receipt (GR)

#### 4.1 GR List (`/procurement/gr`)
**List received goods**

#### 4.2 Create GR (`/procurement/gr/new`)
**Komponen:**
- Select PO
- Received items (quantity received vs ordered)
- Delivery note upload
- Quality check notes

#### 4.3 GR Detail (`/procurement/gr/:id`)
- GR information
- PO linkage
- Discrepancy notes if any

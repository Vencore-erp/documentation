# Modul Admin Panel

## Deskripsi
Modul untuk System Administrator mengelola konfigurasi sistem, user, role, dan master data.

---

## Halaman yang Perlu Dibangun

### 1. User Management

#### 1.1 User List (`/admin/users`)
**Komponen:**
- Data table dengan pagination
- Search & filter (role, status, department)
- Bulk actions (activate, deactivate)
- Export to Excel

**Columns:**
- Name, Email, Role, Department, Status, Last Login, Actions

#### 1.2 User Detail/Edit (`/admin/users/:id`)
**Komponen:**
- User profile form
- Role assignment
- Permission override (optional)
- Activity history
- Password reset action

#### 1.3 Create User (`/admin/users/new`)
**Komponen:**
- User creation form
- Role selector
- Department selector
- Email invitation option

---

### 2. Role & Permission Management

#### 2.1 Role List (`/admin/roles`)
**Komponen:**
- List of roles dengan user count
- Create new role button
- Edit/Delete actions

#### 2.2 Role Detail (`/admin/roles/:id`)
**Komponen:**
- Role name & description
- Permission matrix (checklist grid)
- Users with this role

---

### 3. Master Data Management

#### 3.1 Category Management (`/admin/master/categories`)
- CRUD halaman untuk kategori barang/jasa

#### 3.2 Department Management (`/admin/master/departments`)
- CRUD halaman untuk departemen

#### 3.3 Currency Management (`/admin/master/currencies`)
- Daftar mata uang aktif
- Exchange rate (jika multi-currency)

#### 3.4 Tax Configuration (`/admin/master/taxes`)
- Konfigurasi tarif pajak (PPN, PPh)

#### 3.5 Approval Workflow (`/admin/master/workflows`)
- Visual workflow builder
- Approval threshold configuration

---

### 4. Audit Management

#### 4.1 Audit Trail (`/admin/audit`)
**Komponen:**
- Log table dengan filter tanggal, user, action
- Detail action (before/after values)
- Export capability

#### 4.2 Login History (`/admin/audit/login`)
- Login attempts dengan IP, device, status

---

### 5. System Configuration

#### 5.1 General Settings (`/admin/settings`)
- Company profile
- Logo upload
- System preferences

#### 5.2 Email Templates (`/admin/settings/email-templates`)
- Template editor untuk notifikasi email

#### 5.3 Integration Settings (`/admin/settings/integrations`)
- API keys configuration
- External system connections

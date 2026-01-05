# Modul Notifications

## Deskripsi
Sistem notifikasi untuk memberikan informasi real-time kepada pengguna.

---

## Komponen yang Perlu Dibangun

### 1. Notification Bell (Header Component)
**Lokasi:** Header aplikasi

**Fitur:**
- Unread count badge
- Dropdown preview (5 recent)
- Mark as read
- "View all" link

---

### 2. Notification Center (`/notifications`)
**Halaman Full Notifications**

**Komponen:**
- All notifications list
- Filter by type
- Mark all as read
- Pagination
- Date grouping (Today, Yesterday, This week, Older)

---

## Notification Types

### 1. Approval Notifications
| Event | Recipient | Message Example |
|:---|:---|:---|
| PR Submitted | Supervisor | "PR-2024-0001 membutuhkan persetujuan Anda" |
| PR Approved | Operator | "PR-2024-0001 telah disetujui" |
| PR Rejected | Operator | "PR-2024-0001 ditolak: [reason]" |
| PO Pending Approval | Supervisor | "PO-2024-0001 membutuhkan persetujuan Anda" |
| Invoice Pending | Finance | "Invoice baru dari [Vendor] menunggu verifikasi" |

### 2. Vendor Notifications
| Event | Recipient | Message Example |
|:---|:---|:---|
| RFQ Invitation | Vendor | "Anda diundang untuk RFQ-2024-0001" |
| PO Issued | Vendor | "PO baru diterbitkan untuk Anda" |
| Payment Sent | Vendor | "Pembayaran sebesar Rp X telah ditransfer" |

### 3. System Notifications
| Event | Recipient | Message Example |
|:---|:---|:---|
| Document Expiring | Vendor | "SIUP akan expired dalam 30 hari" |
| Password Expiry | All Users | "Password akan expired dalam 7 hari" |
| Budget Alert | Supervisor | "Budget Department X mencapai 80%" |

### 4. Deadline Notifications
| Event | Recipient | Message Example |
|:---|:---|:---|
| RFQ Deadline | Vendor | "RFQ-2024-0001 deadline dalam 2 hari" |
| Payment Due | Finance | "3 invoice jatuh tempo hari ini" |
| Contract Expiring | Operator | "Kontrak dengan [Vendor] expired dalam 30 hari" |

---

## Notification Preferences (`/profile/notifications`)
**User Settings**

**Options per Notification Type:**
- Email notification: On/Off
- In-app notification: On/Off
- Push notification: On/Off (if PWA)

---

## Technical Implementation

### Real-time Updates
- WebSocket connection for instant notifications
- Fallback: Polling every 30 seconds

### Notification State
```typescript
interface Notification {
  id: string;
  type: NotificationType;
  title: string;
  message: string;
  link?: string;        // Deep link to related page
  isRead: boolean;
  createdAt: Date;
  data?: Record<string, any>;  // Additional context
}
```

### Toast Notifications
- Auto-dismiss after 5 seconds
- Action button for navigation
- Stack multiple toasts

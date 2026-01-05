# Shared Components

## Deskripsi
Komponen UI reusable yang digunakan di seluruh aplikasi untuk konsistensi desain.

---

## Layout Components

### 1. AppShell
- Sidebar navigation
- Header with user menu
- Main content area
- Footer (optional)

### 2. Sidebar
- Collapsible navigation
- Role-based menu items
- Active state indicator
- Nested menu support

### 3. Header
- Logo
- Global search
- Notification bell
- User menu dropdown

### 4. Breadcrumb
- Dynamic path display
- Clickable navigation

---

## Data Display Components

### 5. DataTable
**Features:**
- Pagination
- Sorting
- Column visibility toggle
- Row selection
- Bulk actions
- Search
- Advanced filters
- Empty state
- Loading skeleton

### 6. Card
- Various variants (stat card, info card, action card)
- With/without header
- Footer actions

### 7. StatusBadge
**Status Types:**
- Draft (gray)
- Pending (yellow)
- Approved (green)
- Rejected (red)
- In Progress (blue)
- Completed (green)
- Cancelled (gray)

### 8. Timeline
- Activity timeline
- Approval history
- Status changes

---

## Form Components

### 9. Form Fields
- Input (text, number, email, password)
- Textarea
- Select / Multi-select
- Date picker / Date range picker
- File upload (single/multiple)
- Currency input
- Number with formatting

### 10. FormWizard
- Multi-step form
- Progress indicator
- Step validation
- Back/Next/Submit buttons

### 11. SearchableSelect
- Async search
- Create option
- Multi-select variant

---

## Feedback Components

### 12. Alert/Toast
- Success
- Error
- Warning
- Info

### 13. Modal
- Confirmation modal
- Form modal
- Full-screen modal

### 14. LoadingSpinner / Skeleton
- Button loading
- Page loading
- Table skeleton
- Card skeleton

### 15. EmptyState
- No data illustration
- Action button
- Custom message

---

## Navigation Components

### 16. Tabs
- Horizontal tabs
- Vertical tabs
- With counts/badges

### 17. Dropdown
- User menu
- Actions menu
- Multi-level

---

## Approval Components

### 18. ApprovalActions
- Approve button
- Reject button
- Comments textarea
- Confirmation modal

### 19. ApprovalTimeline
- Approval flow visualization
- Current step highlight
- Approver info

---

## Document Components

### 20. FileUpload
- Drag & drop
- File type validation
- Size limit
- Progress bar
- Preview

### 21. DocumentViewer
- PDF preview
- Image preview
- Download action

---

## Chart Components

### 22. Charts (using Recharts)
- Line chart
- Bar chart
- Pie/Donut chart
- Area chart

---

## Design Tokens

```css
/* Colors */
--primary: #0066CC;
--secondary: #6B7280;
--success: #10B981;
--warning: #F59E0B;
--danger: #EF4444;
--info: #3B82F6;

/* Typography */
--font-family: 'Inter', sans-serif;
--heading-1: 2rem / bold;
--heading-2: 1.5rem / semibold;
--body: 1rem / regular;
--caption: 0.875rem / regular;

/* Spacing */
--spacing-xs: 0.25rem;
--spacing-sm: 0.5rem;
--spacing-md: 1rem;
--spacing-lg: 1.5rem;
--spacing-xl: 2rem;

/* Border Radius */
--radius-sm: 0.25rem;
--radius-md: 0.5rem;
--radius-lg: 1rem;
```

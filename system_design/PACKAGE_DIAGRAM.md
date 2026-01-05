# Package Diagram: Sistem e-Procurement Enterprise

## 1. Frontend Package Structure

```mermaid
graph TB
    subgraph Frontend["📦 Frontend Application (Next.js)"]
        subgraph Core["📁 core"]
            Auth["🔐 auth<br/>- AuthProvider<br/>- useAuth hook<br/>- guards"]
            API["🌐 api<br/>- axios instance<br/>- interceptors<br/>- endpoints"]
            Store["💾 store<br/>- zustand stores<br/>- global state"]
        end

        subgraph Features["📁 features"]
            AuthFeat["authentication<br/>├─ login<br/>├─ register<br/>├─ forgot-password<br/>└─ 2fa"]
            Dashboard["dashboard<br/>├─ admin<br/>├─ operator<br/>├─ supervisor<br/>├─ finance<br/>└─ vendor"]
            Admin["admin<br/>├─ users<br/>├─ roles<br/>├─ master-data<br/>└─ audit"]
            Procurement["procurement<br/>├─ pr (requisition)<br/>├─ rfq<br/>├─ po (order)<br/>└─ gr (receipt)"]
            VendorMgmt["vendor-management<br/>├─ vendor-list<br/>├─ qualification<br/>├─ contracts<br/>└─ scorecard"]
            Finance["finance<br/>├─ invoices<br/>├─ payments<br/>├─ budget<br/>└─ disputes"]
            VendorPortal["vendor-portal<br/>├─ profile<br/>├─ rfq<br/>├─ po<br/>├─ invoices<br/>└─ disputes"]
            Reports["reporting<br/>├─ procurement<br/>├─ vendor<br/>├─ finance<br/>└─ audit"]
        end

        subgraph Shared["📁 shared"]
            Components["components<br/>├─ ui (primitives)<br/>├─ layout<br/>├─ data-display<br/>├─ forms<br/>└─ feedback"]
            Hooks["hooks<br/>├─ useTable<br/>├─ useForm<br/>├─ useNotification<br/>└─ usePermission"]
            Utils["utils<br/>├─ formatters<br/>├─ validators<br/>└─ constants"]
            Types["types<br/>├─ api.d.ts<br/>├─ models.d.ts<br/>└─ enums.ts"]
        end
    end

    Core --> Features
    Shared --> Features
```

---

## 2. Backend Microservices Package

```mermaid
graph TB
    subgraph Backend["📦 Backend Microservices (Spring Boot)"]
        subgraph Infrastructure["🏗️ Infrastructure Layer"]
            Gateway["API Gateway<br/>- Routing<br/>- Rate Limiting<br/>- Auth Filter"]
            Discovery["Service Discovery<br/>- Eureka Server"]
            MQ["Message Queue<br/>- RabbitMQ/Kafka<br/>- Event Bus"]
        end

        subgraph CoreServices["🔧 Core Services"]
            AuthSvc["Auth Service<br/>├─ authentication<br/>├─ authorization<br/>├─ role-management<br/>└─ session-management"]
            UserSvc["User Service<br/>├─ user-internal<br/>├─ user-external<br/>└─ vendor-entity"]
            NotifSvc["Notification Service<br/>├─ email<br/>├─ in-app<br/>└─ push"]
            AuditSvc["Audit Service<br/>├─ logging<br/>└─ trail"]
            FileSvc["File Service<br/>├─ upload<br/>├─ storage<br/>└─ retrieval"]
        end

        subgraph BusinessServices["💼 Business Services"]
            VendorSvc["Vendor Service<br/>├─ registration<br/>├─ qualification<br/>├─ kyc-aml<br/>└─ blacklist"]
            ProcureSvc["Procurement Service<br/>├─ purchase-requisition<br/>├─ request-for-quotation<br/>├─ purchase-order<br/>└─ goods-receipt"]
            CatalogSvc["Catalog Service<br/>├─ items<br/>├─ categories<br/>└─ pricing"]
            ContractSvc["Contract Service<br/>├─ agreements<br/>├─ sla<br/>└─ penalties"]
            PaymentSvc["Payment Service<br/>├─ invoice<br/>├─ verification<br/>├─ payment<br/>└─ reconciliation"]
            WorkflowSvc["Workflow Service<br/>├─ approval-engine<br/>├─ escalation<br/>└─ delegation"]
            ReportSvc["Reporting Service<br/>├─ generators<br/>├─ schedulers<br/>└─ exports"]
        end
    end

    Gateway --> CoreServices
    Gateway --> BusinessServices
    CoreServices --> MQ
    BusinessServices --> MQ
```

---

## 3. Feature Module Internal Structure

```mermaid
graph TB
    subgraph FeatureModule["📦 Feature Module Template"]
        subgraph API["api/"]
            Endpoints["endpoints.ts<br/>- API calls"]
            Queries["queries.ts<br/>- React Query hooks"]
            Mutations["mutations.ts<br/>- Mutation hooks"]
        end

        subgraph Components["components/"]
            Page["pages/<br/>- List page<br/>- Detail page<br/>- Form page"]
            UI["ui/<br/>- Feature-specific<br/>  components"]
            Modals["modals/<br/>- Create/Edit modals<br/>- Confirmation"]
        end

        subgraph Hooks["hooks/"]
            CustomHooks["useFeature.ts<br/>- Business logic hooks"]
        end

        subgraph Store["store/"]
            FeatureStore["feature.store.ts<br/>- Local state"]
        end

        subgraph Types["types/"]
            TypeDefs["index.ts<br/>- Feature types<br/>- DTOs"]
        end

        Index["index.ts<br/>- Public exports"]
    end

    API --> Components
    Hooks --> Components
    Store --> Hooks
    Types --> API
    Components --> Index
```

---

## 4. Shared Components Package

```mermaid
graph TB
    subgraph SharedComponents["📦 Shared Components"]
        subgraph Primitives["ui/ (Primitives)"]
            Button["Button"]
            Input["Input"]
            Select["Select"]
            Checkbox["Checkbox"]
            Dialog["Dialog/Modal"]
            Toast["Toast"]
            Badge["Badge"]
            Avatar["Avatar"]
        end

        subgraph Layout["layout/"]
            AppShell["AppShell"]
            Sidebar["Sidebar"]
            Header["Header"]
            PageContainer["PageContainer"]
            Breadcrumb["Breadcrumb"]
        end

        subgraph DataDisplay["data-display/"]
            DataTable["DataTable"]
            Card["Card/StatCard"]
            Timeline["Timeline"]
            StatusBadge["StatusBadge"]
            EmptyState["EmptyState"]
        end

        subgraph Forms["forms/"]
            FormField["FormField"]
            FormWizard["FormWizard"]
            FileUpload["FileUpload"]
            DatePicker["DatePicker"]
            CurrencyInput["CurrencyInput"]
        end

        subgraph Charts["charts/"]
            LineChart["LineChart"]
            BarChart["BarChart"]
            PieChart["PieChart"]
            DonutChart["DonutChart"]
        end

        subgraph Approval["approval/"]
            ApprovalActions["ApprovalActions"]
            ApprovalTimeline["ApprovalTimeline"]
            ApprovalBadge["ApprovalBadge"]
        end
    end
```

---

## 5. API Layer Structure

```mermaid
graph LR
    subgraph APILayer["📦 API Layer"]
        subgraph Client["HTTP Client"]
            Axios["axios instance<br/>- Base URL<br/>- Timeout<br/>- Headers"]
        end

        subgraph Interceptors["Interceptors"]
            ReqInt["Request Interceptor<br/>- Auth token<br/>- Request ID"]
            ResInt["Response Interceptor<br/>- Error handling<br/>- Token refresh"]
        end

        subgraph Endpoints["Endpoints"]
            AuthAPI["auth.api.ts"]
            UserAPI["user.api.ts"]
            VendorAPI["vendor.api.ts"]
            PRAPI["pr.api.ts"]
            RFQAPI["rfq.api.ts"]
            POAPI["po.api.ts"]
            InvoiceAPI["invoice.api.ts"]
            PaymentAPI["payment.api.ts"]
            ReportAPI["report.api.ts"]
        end
    end

    Client --> Interceptors
    Interceptors --> Endpoints
```

---

## 6. State Management

```mermaid
graph TB
    subgraph StateManagement["📦 State Management"]
        subgraph GlobalState["Global State (Zustand)"]
            AuthStore["authStore<br/>- user<br/>- token<br/>- permissions"]
            UIStore["uiStore<br/>- sidebar<br/>- theme<br/>- loading"]
            NotifStore["notificationStore<br/>- notifications<br/>- unreadCount"]
        end

        subgraph ServerState["Server State (React Query)"]
            Queries["Queries<br/>- GET data<br/>- Caching<br/>- Refetching"]
            Mutations["Mutations<br/>- POST/PUT/DELETE<br/>- Optimistic updates<br/>- Invalidation"]
        end

        subgraph LocalState["Local State (React)"]
            ComponentState["useState<br/>- Form state<br/>- UI toggles"]
            ContextState["useContext<br/>- Feature context"]
        end
    end

    GlobalState --> |"Global access"| Components["Components"]
    ServerState --> |"Data fetching"| Components
    LocalState --> |"Component-specific"| Components
```

---

## 7. Folder Structure Overview

```
src/
├── app/                          # Next.js App Router
│   ├── (auth)/                   # Auth group routes
│   │   ├── login/
│   │   ├── register/
│   │   └── forgot-password/
│   ├── (dashboard)/              # Authenticated routes
│   │   ├── admin/
│   │   ├── operator/
│   │   ├── supervisor/
│   │   ├── finance/
│   │   └── vendor/
│   ├── layout.tsx
│   └── page.tsx
│
├── core/                         # Core functionality
│   ├── api/
│   ├── auth/
│   └── store/
│
├── features/                     # Feature modules
│   ├── authentication/
│   ├── dashboard/
│   ├── admin/
│   ├── procurement/
│   ├── vendor-management/
│   ├── finance/
│   ├── vendor-portal/
│   └── reporting/
│
├── shared/                       # Shared resources
│   ├── components/
│   │   ├── ui/
│   │   ├── layout/
│   │   ├── data-display/
│   │   ├── forms/
│   │   └── charts/
│   ├── hooks/
│   ├── utils/
│   └── types/
│
└── styles/                       # Global styles
    ├── globals.css
    └── variables.css
```

---

## 8. Dependency Flow

```mermaid
flowchart TB
    subgraph External["External Dependencies"]
        React["React 18+"]
        Next["Next.js 14+"]
        TailwindCSS["Tailwind CSS"]
        ShadcnUI["shadcn/ui"]
        ReactQuery["React Query"]
        Zustand["Zustand"]
        RHF["React Hook Form"]
        Zod["Zod"]
        Axios["Axios"]
        Recharts["Recharts"]
    end

    subgraph Internal["Internal Packages"]
        Core["@/core"]
        Shared["@/shared"]
        Features["@/features/*"]
    end

    React --> Next
    Next --> TailwindCSS
    TailwindCSS --> ShadcnUI
    ShadcnUI --> Shared
    ReactQuery --> Core
    Zustand --> Core
    RHF --> Shared
    Zod --> Shared
    Axios --> Core
    Recharts --> Shared
    
    Core --> Features
    Shared --> Features
```

---

## Catatan untuk Developer

1. **Feature-first Architecture**: Setiap fitur memiliki folder sendiri dengan struktur konsisten
2. **Barrel Exports**: Gunakan `index.ts` untuk public exports di setiap module
3. **Absolute Imports**: Gunakan path alias (`@/core`, `@/shared`, `@/features`)
4. **Co-location**: Tempatkan test files berdampingan dengan source files
5. **Lazy Loading**: Gunakan dynamic imports untuk feature modules

# Microservices Overview

## Service Catalog

This document provides detailed documentation for each microservice in the e-Procurement system.

---

## 1. Auth Service

**Port:** 8081  
**Package:** `com.tugas_akhir.auth_service`  
**Description:** Handles authentication, authorization, and role management for the entire system.

### Responsibilities
- User authentication (login/logout)
- JWT token generation and validation
- Password reset functionality
- Role and permission management
- Session management with Redis
- Vendor profile registration

### Key Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/auth/login` | User authentication |
| POST | `/api/v1/auth/logout` | Session termination |
| POST | `/api/v1/auth/refresh` | Token refresh |
| POST | `/api/v1/auth/reset/request` | Request password reset |
| POST | `/api/v1/auth/reset/confirm` | Confirm password reset |
| GET | `/api/v1/auth/validate` | Validate JWT token |

### Database Tables
- `users` - User credentials and status
- `roles` - System roles
- `user_roles` - User-role mappings
- `password_reset_requests` - Reset token tracking
- `vendor_profiles` - Vendor registration data

### Dependencies
- PostgreSQL (user data)
- Redis (session storage, token blacklist)
- Kafka (event publishing)

### Configuration
```properties
server.port=8081
spring.datasource.url=jdbc:postgresql://localhost:5432/masterdb
spring.data.redis.host=localhost
spring.kafka.bootstrap-servers=localhost:9092
```

---

## 2. User Service

**Port:** 8082  
**Package:** `com.tugas_akhir.user_service`  
**Description:** Manages user profiles for internal employees, external users, and vendors.

### Responsibilities
- Internal user management (employees)
- External user management (clients)
- Vendor user management
- User profile CRUD operations
- Soft delete support

### User Types
| Type | Description |
|------|-------------|
| `UserInternal` | Company employees |
| `UserExternal` | External clients/partners |
| `Vendor` | Supplier organizations |

### Database Tables
- `user_internal` - Internal employee records
- `user_external` - External user records
- `vendor` - Vendor organization records

### Key Features
- Soft delete pattern (`is_deleted` flag)
- Department association
- Status management (ACTIVE, INACTIVE, SUSPENDED)

---

## 3. Vendor Service

**Port:** 8085  
**Package:** `com.tugasakhir.vendor_service`  
**Description:** Manages vendor registration, verification, and ongoing vendor relationships.

### Responsibilities
- Vendor registration and onboarding
- Vendor verification process
- Vendor scheduling and availability
- Document management for vendors
- Vendor status tracking

### Key Components
- `VendorController` - REST API endpoints
- `VendorService` - Business logic
- `VendorRepository` - Data access
- Kafka integration for event publishing

### Scheduler Features
- Automatic reminder scheduling
- Verification deadline tracking

---

## 4. Procurement Service

**Port:** 8084  
**Package:** `com.tugas_akhir.procurement_service`  
**Description:** Core service managing the entire procurement lifecycle from requisition to purchase order.

### Responsibilities
- Purchase Requisition (PR) management
- Purchase Order (PO) creation
- Approval workflow processing
- Delivery tracking
- Document management
- Dashboard data for operators and supervisors

### Domain Modules
| Module | Description |
|--------|-------------|
| `domain.pr` | Purchase Requisition management |
| `domain.po` | Purchase Order management |
| `domain.delivery` | Delivery tracking |
| `domain.audit` | Approval and audit trail |
| `domain.dashboard` | Dashboard data aggregation |
| `domain.inventory` | Inventory integration |

### PR Workflow States
```
DRAFT → SUBMITTED → APPROVED → PO_CREATED
                  ↘ REJECTED
                  ↘ RETURNED
                  ↘ ESCALATED
                  ↘ CANCELLED
```

### PR Types
- `GOODS` - Physical goods procurement
- `SERVICE` - Service procurement

### Priority Levels
- `NORMAL` - Standard processing
- `IMPORTANT` - Elevated priority
- `URGENT` - Immediate attention required

### Kafka Topics
```properties
procurement.pr.submitted
procurement.pr.approved
procurement.pr.rejected
procurement.pr.returned
procurement.pr.reminder
procurement.pr.escalated
procurement.po.created
procurement.inventory.stock-alert
```

### Database Tables
- `procurement_requests` - Main PR records
- `procurement_items` - Line items
- `delivery_details` - Delivery information
- `additional_documents` - Attachments
- `approval_histories` - Approval audit trail
- `termin_details` - Payment terms
- `po_*` - Purchase order tables
- `receiving_records` - Goods receipt

---

## 5. Master Data Service

**Port:** 8083  
**Package:** `com.tugas_akhir.master_data`  
**Description:** Manages reference data and master records used across the system.

### Responsibilities
- Catalog management (items, categories)
- Location management
- Cost center management
- General configuration

### Domain Modules
| Module | Description |
|--------|-------------|
| `catalog` | Product/service catalog |
| `location` | Geographic locations |
| `costcenter` | Cost center definitions |
| `config` | System configurations |

### Key Features
- Hierarchical category structure
- UOM (Unit of Measure) management
- Tax configuration

---

## 6. Inventory Service

**Port:** 8093  
**Package:** `com.tugas_akhir.inventory_service`  
**Description:** Manages warehouse operations, stock levels, and inventory transactions.

### Responsibilities
- Warehouse management
- Stock level tracking
- Stock transactions (in/out)
- Stock alerts and notifications

### Modules
| Module | Description |
|--------|-------------|
| `warehouse` | Warehouse CRUD |
| `stock` | Stock level management |
| `transaction` | Stock movements |

### Database Tables
- `warehouse` - Warehouse locations
- `stock` - Current stock levels
- `stock_transaction` - Movement history

---

## 7. Admin Service

**Port:** 8087  
**Package:** `com.tugas_akhir.admin_service`  
**Description:** Provides administrative functions and cross-service management capabilities.

### Responsibilities
- System administration
- Cross-service data aggregation
- Employee management
- Calendar management
- Centralized logging

### Modules
| Module | Description |
|--------|-------------|
| `audit` | Audit log viewing |
| `calendar` | Calendar management |
| `employee` | Employee administration |
| `lokasi` | Location management |
| `masterdata` | Master data admin |
| `notification` | Notification management |
| `procurement` | Procurement oversight |
| `vendor` | Vendor administration |
| `verification` | Verification workflows |

---

## 8. Gateway Service

**Port:** 8080  
**Package:** `com.tugas_akhir.gateway_service`  
**Description:** API Gateway that routes incoming requests to appropriate microservices.

### Responsibilities
- Request routing
- Rate limiting
- CORS handling
- Circuit breaker patterns
- JWT validation

### Route Configuration
```properties
# Auth Service
Path=/api/auth/** → http://localhost:8081

# User Service
Path=/api/v1/users/** → http://localhost:8082

# Vendor Service
Path=/api/v1/vendors/** → http://localhost:8085

# Procurement Service
Path=/api/v1/procurements/** → http://localhost:8084

# Admin Service
Path=/api/v1/admin/** → http://localhost:8087
```

### Features
- **Rate Limiting**: 100 requests/minute (burst: 200)
- **Circuit Breaker**: Resilience4j with 50% failure threshold
- **CORS**: All origins allowed for development

---

## 9. Notification Service

**Port:** 8088  
**Package:** `com.tugas_akhir.notification_service`  
**Description:** Handles notification delivery across multiple channels.

### Responsibilities
- Email notifications
- In-app notifications
- Push notifications
- Notification templates
- Kafka event consumption

### Kafka Consumers
- Listens to procurement events
- Triggers notifications based on event type

---

## 10. Audit Service

**Port:** 8090  
**Package:** `com.tugas_akhir.audit_service`  
**Description:** Captures and stores audit logs for compliance and traceability.

### Responsibilities
- Audit event capture
- Log storage and retrieval
- Compliance reporting
- Event correlation

### Features
- Kafka-based event consumption
- Structured audit log storage
- Query API for log retrieval

---

## 11. Workflow Service

**Port:** 8091  
**Package:** `com.tugas_akhir.workflow_service`  
**Description:** Orchestrates business workflows and approval processes.

### Responsibilities
- Workflow definition
- Approval routing
- Escalation handling
- Reminder scheduling

### Features
- Configurable approval chains
- Auto-escalation rules
- SLA monitoring

---

## 12. Reporting Service

**Port:** 8092  
**Package:** `com.tugas_akhir.reporting_service`  
**Description:** Provides analytics, dashboards, and report generation.

### Responsibilities
- Report generation
- Data aggregation
- Dashboard APIs
- Export functionality

---

## 13. Document Service

**Port:** 8089  
**Package:** `com.tugas_akhir.document_service`  
**Description:** Manages file storage and document lifecycle.

### Responsibilities
- File upload/download
- Document versioning
- Storage management
- Access control

---

## Service Dependencies Matrix

| Service | DB | Redis | Kafka | Depends On |
|---------|:--:|:-----:|:-----:|------------|
| Auth | ✓ | ✓ | ✓ | - |
| User | ✓ | - | ✓ | Auth |
| Vendor | ✓ | - | ✓ | Auth, User |
| Procurement | ✓ | ✓ | ✓ | Auth, Vendor, Master Data |
| Master Data | ✓ | - | - | - |
| Inventory | ✓ | - | ✓ | Master Data |
| Admin | ✓ | - | ✓ | All services |
| Gateway | - | ✓ | - | All services |
| Notification | ✓ | - | ✓ | - |
| Audit | ✓ | - | ✓ | - |
| Workflow | ✓ | - | ✓ | - |
| Reporting | ✓ | - | - | All services |
| Document | - | - | - | - |

---

## Next Steps
- [API Reference](./API_REFERENCE.md) - Complete API documentation
- [Database Schema](./DATABASE_SCHEMA.md) - Table definitions
- [Getting Started](./GETTING_STARTED.md) - Development setup

# Dokumentasi Microservices Detail
**Enterprise e-Procurement ERP**
**Versi:** 2.0 | **Framework:** Spring Boot 3.2

---

## 1. Struktur Project Microservice

### 1.1 Standard Maven Structure

```
{service-name}/
├── src/
│   ├── main/
│   │   ├── java/com/eprocurement/{service}/
│   │   │   ├── config/              # Configuration classes
│   │   │   ├── controller/          # REST controllers
│   │   │   ├── service/             # Business logic
│   │   │   ├── repository/          # Data access
│   │   │   ├── entity/              # JPA entities
│   │   │   ├── dto/                 # Data transfer objects
│   │   │   ├── mapper/              # Entity-DTO mappers
│   │   │   ├── event/               # Kafka events
│   │   │   │   ├── producer/
│   │   │   │   └── consumer/
│   │   │   ├── exception/           # Custom exceptions
│   │   │   └── {Service}Application.java
│   │   └── resources/
│   │       ├── application.yml
│   │       ├── application-dev.yml
│   │       └── application-prod.yml
│   └── test/
├── Dockerfile
├── pom.xml
└── README.md
```

---

## 2. Detail Per Service

### 2.1 Auth Service

| Atribut | Detail |
|:---|:---|
| **Port** | 8081 |
| **Database** | auth_db |
| **Kafka Topics** | auth-events |

#### Endpoints

| Method | Endpoint | Description |
|:---|:---|:---|
| POST | `/api/auth/login` | User login |
| POST | `/api/auth/logout` | Logout, invalidate token |
| POST | `/api/auth/refresh` | Refresh access token |
| POST | `/api/auth/mfa/verify` | Verify MFA code |
| POST | `/api/auth/password/reset` | Request password reset |

#### Entity

```java
@Entity
@Table(name = "refresh_tokens")
public class RefreshToken {
    @Id
    private UUID id;
    private String userId;
    private String token;
    private Instant expiresAt;
    private boolean revoked;
    private String deviceInfo;
    private Instant createdAt;
}
```

---

### 2.2 User Service

| Atribut | Detail |
|:---|:---|
| **Port** | 8082 |
| **Database** | user_db |
| **Kafka Topics** | user-events |

#### Endpoints

| Method | Endpoint | Description |
|:---|:---|:---|
| GET | `/api/users` | List users (paginated) |
| GET | `/api/users/{id}` | Get user detail |
| POST | `/api/users` | Create user |
| PUT | `/api/users/{id}` | Update user |
| DELETE | `/api/users/{id}` | Soft delete user |
| GET | `/api/roles` | List roles |
| POST | `/api/users/{id}/roles` | Assign role |

#### Entities

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    private UUID id;
    private String email;
    private String fullName;
    private String department;
    private String costCenter;
    private UserStatus status;
    private boolean mfaEnabled;
    
    @ManyToMany
    private Set<Role> roles;
    
    private Instant createdAt;
    private Instant updatedAt;
    private Instant deletedAt;
}

@Entity
@Table(name = "roles")
public class Role {
    @Id
    private UUID id;
    private String name;
    private String description;
    
    @ManyToMany
    private Set<Permission> permissions;
}
```

---

### 2.3 Procurement Service

| Atribut | Detail |
|:---|:---|
| **Port** | 8083 |
| **Database** | procurement_db |
| **Kafka Topics** | pr-events, rfq-events, po-events |

#### Endpoints - Purchase Requisition

| Method | Endpoint | Description |
|:---|:---|:---|
| GET | `/api/pr` | List PRs |
| GET | `/api/pr/{id}` | Get PR detail |
| POST | `/api/pr` | Create PR |
| PUT | `/api/pr/{id}` | Update draft PR |
| POST | `/api/pr/{id}/submit` | Submit for approval |
| POST | `/api/pr/{id}/approve` | Approve PR |
| POST | `/api/pr/{id}/reject` | Reject PR |
| POST | `/api/pr/{id}/cancel` | Cancel PR |

#### Endpoints - RFQ

| Method | Endpoint | Description |
|:---|:---|:---|
| GET | `/api/rfq` | List RFQs |
| POST | `/api/rfq` | Create from PR |
| POST | `/api/rfq/{id}/publish` | Publish to vendors |
| POST | `/api/rfq/{id}/extend` | Extend deadline |
| GET | `/api/rfq/{id}/bids` | View bids (sealed) |
| POST | `/api/rfq/{id}/unseal` | Unseal bids |
| POST | `/api/rfq/{id}/award` | Award to vendor |

#### Endpoints - Purchase Order

| Method | Endpoint | Description |
|:---|:---|:---|
| GET | `/api/po` | List POs |
| POST | `/api/po` | Create from RFQ |
| POST | `/api/po/{id}/approve` | Approve PO |
| POST | `/api/po/{id}/issue` | Issue to vendor |
| POST | `/api/po/{id}/revise` | Revise (change order) |
| POST | `/api/po/{id}/cancel` | Cancel PO |

#### Key Entities

```java
@Entity
@Table(name = "purchase_requisitions")
public class PurchaseRequisition {
    @Id
    private UUID id;
    private String prNumber;
    private String title;
    private UUID requesterId;
    private String department;
    private String costCenter;
    private BigDecimal totalAmount;
    private PRStatus status;
    private LocalDate requiredDate;
    
    @OneToMany(mappedBy = "pr")
    private List<PRLineItem> lineItems;
    
    @OneToMany(mappedBy = "pr")
    private List<Attachment> attachments;
}

public enum PRStatus {
    DRAFT, PENDING_APPROVAL, APPROVED, REJECTED, 
    CANCELLED, CONVERTED_TO_RFQ
}
```

---

### 2.4 Vendor Service

| Atribut | Detail |
|:---|:---|
| **Port** | 8084 |
| **Database** | vendor_db |
| **Kafka Topics** | vendor-events |

#### Endpoints

| Method | Endpoint | Description |
|:---|:---|:---|
| GET | `/api/vendors` | List vendors |
| GET | `/api/vendors/{id}` | Get vendor detail |
| POST | `/api/vendors/invite` | Send invitation |
| POST | `/api/vendors/register` | Complete registration |
| PUT | `/api/vendors/{id}/profile` | Update profile |
| POST | `/api/vendors/{id}/documents` | Upload documents |
| POST | `/api/vendors/{id}/verify` | Verify KYC |
| POST | `/api/vendors/{id}/blacklist` | Blacklist vendor |
| GET | `/api/vendors/{id}/performance` | Get scorecard |
| POST | `/api/vendors/{id}/rate` | Submit rating |

#### Entities

```java
@Entity
@Table(name = "vendors")
public class Vendor {
    @Id
    private UUID id;
    private String companyName;
    private String npwp;
    private String nib;
    private String address;
    private String city;
    private String phone;
    private String email;
    private VendorStatus status;
    private Double performanceScore;
    
    @OneToMany(mappedBy = "vendor")
    private List<VendorDocument> documents;
    
    @OneToMany(mappedBy = "vendor")
    private List<BankAccount> bankAccounts;
}

public enum VendorStatus {
    INVITED, REGISTERED, PENDING_VERIFICATION,
    VERIFIED, SUSPENDED, BLACKLISTED
}
```

---

### 2.5 Finance Service

| Atribut | Detail |
|:---|:---|
| **Port** | 8085 |
| **Database** | finance_db |
| **Kafka Topics** | invoice-events, payment-events |

#### Endpoints - Invoice

| Method | Endpoint | Description |
|:---|:---|:---|
| GET | `/api/invoices` | List invoices |
| POST | `/api/invoices` | Create/receive invoice |
| POST | `/api/invoices/{id}/match` | Run 3-way matching |
| POST | `/api/invoices/{id}/approve` | Approve for payment |
| POST | `/api/invoices/{id}/dispute` | Flag as disputed |

#### Endpoints - Payment

| Method | Endpoint | Description |
|:---|:---|:---|
| GET | `/api/payments` | List payments |
| POST | `/api/payments/voucher` | Create payment voucher |
| POST | `/api/payments/batch` | Execute batch payment |
| GET | `/api/payments/{id}/status` | Check payment status |

#### Endpoints - Budget

| Method | Endpoint | Description |
|:---|:---|:---|
| GET | `/api/budgets` | List budgets |
| POST | `/api/budgets/check` | Check availability |
| POST | `/api/budgets/reserve` | Soft lock |
| POST | `/api/budgets/commit` | Hard commit |
| POST | `/api/budgets/release` | Release hold |

---

### 2.6 Notification Service

| Atribut | Detail |
|:---|:---|
| **Port** | 8086 |
| **Database** | notification_db |
| **Consumes** | All event topics |

#### Channels

| Channel | Provider | Use Case |
|:---|:---|:---|
| Email | SendGrid / AWS SES | Approvals, alerts |
| SMS | Twilio | MFA, urgent alerts |
| Push | Firebase FCM | Mobile notifications |
| In-App | WebSocket | Real-time updates |

#### Event Consumer

```java
@Component
public class NotificationEventConsumer {

    @KafkaListener(topics = "pr-events")
    public void handlePREvent(PREvent event) {
        switch (event.getType()) {
            case "pr.created" -> sendApprovalRequest(event);
            case "pr.approved" -> notifyRequester(event);
            case "pr.rejected" -> notifyRejection(event);
        }
    }
}
```

---

### 2.7 Audit Service

| Atribut | Detail |
|:---|:---|
| **Port** | 8087 |
| **Database** | Elasticsearch |
| **Consumes** | All event topics |

#### Features

- Immutable audit log
- Full-text search
- Retention policies
- Compliance reporting

#### Audit Log Schema

```json
{
  "id": "audit-123",
  "timestamp": "2026-01-06T10:00:00Z",
  "actor": {
    "userId": "user-456",
    "email": "john@bank.com",
    "role": "OPERATOR",
    "ipAddress": "10.0.0.1"
  },
  "action": "PR_CREATED",
  "resource": {
    "type": "PurchaseRequisition",
    "id": "PR-2026-00001"
  },
  "details": {
    "totalAmount": 50000000,
    "department": "IT"
  },
  "status": "SUCCESS"
}
```

---

### 2.8 Document Service

| Atribut | Detail |
|:---|:---|
| **Port** | 8088 |
| **Storage** | MinIO (S3-compatible) |
| **Database** | document_db (metadata) |

#### Endpoints

| Method | Endpoint | Description |
|:---|:---|:---|
| POST | `/api/documents/upload` | Upload file |
| GET | `/api/documents/{id}` | Download file |
| GET | `/api/documents/{id}/preview` | Get preview URL |
| DELETE | `/api/documents/{id}` | Delete file |
| GET | `/api/documents/{id}/versions` | List versions |

#### Security Features

- Virus scanning (ClamAV)
- File type validation
- Size limits
- Presigned URLs (temporary access)

---

### 2.9 Inventory Service

| Atribut | Detail |
|:---|:---|
| **Port** | 8089 |
| **Database** | inventory_db |
| **Kafka Topics** | grn-events, rtv-events |

#### Endpoints

| Method | Endpoint | Description |
|:---|:---|:---|
| POST | `/api/grn` | Create Goods Receipt |
| POST | `/api/grn/{id}/inspect` | QC Inspection |
| POST | `/api/rtv` | Create Return to Vendor |
| GET | `/api/stock` | Get stock levels |
| GET | `/api/stock/movements` | Movement history |

---

## 3. Shared Libraries

### 3.1 Common Library (Maven)

```xml
<dependency>
    <groupId>com.eprocurement</groupId>
    <artifactId>common-lib</artifactId>
    <version>1.0.0</version>
</dependency>
```

**Contents:**
- Exception handlers
- DTO base classes
- Kafka event base
- Audit annotations
- Security utilities

### 3.2 Shared DTOs

```java
// Response wrapper
public record ApiResponse<T>(
    boolean success,
    String message,
    T data,
    Instant timestamp
) {}

// Pagination
public record PageRequest(
    int page,
    int size,
    String sortBy,
    String sortDir
) {}

public record PageResponse<T>(
    List<T> content,
    int page,
    int size,
    long totalElements,
    int totalPages
) {}

// Error response
public record ErrorResponse(
    Instant timestamp,
    int status,
    String errorCode,
    String message,
    String path
) {}
```

---

## 4. Inter-Service Communication

### 4.1 Synchronous (REST)

```java
@FeignClient(name = "user-service")
public interface UserServiceClient {

    @GetMapping("/api/users/{id}")
    UserDTO getUserById(@PathVariable UUID id);

    @GetMapping("/api/users/{id}/permissions")
    List<String> getUserPermissions(@PathVariable UUID id);
}
```

### 4.2 Asynchronous (Kafka)

```java
// Producer
@Service
public class PREventProducer {

    private final KafkaTemplate<String, PREvent> kafkaTemplate;

    public void publishPRCreated(PurchaseRequisition pr) {
        PREvent event = PREvent.builder()
            .type("pr.created")
            .prId(pr.getId())
            .requesterId(pr.getRequesterId())
            .amount(pr.getTotalAmount())
            .status(pr.getStatus())
            .timestamp(Instant.now())
            .build();

        kafkaTemplate.send("pr-events", pr.getId().toString(), event);
    }
}
```

---

## 5. Configuration Templates

### 5.1 application.yml Template

```yaml
spring:
  application:
    name: ${SERVICE_NAME}
  
  datasource:
    url: jdbc:postgresql://${DB_HOST}:5432/${DB_NAME}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
  
  jpa:
    hibernate:
      ddl-auto: validate
    properties:
      hibernate:
        dialect: org.hibernate.dialect.PostgreSQLDialect
  
  kafka:
    bootstrap-servers: ${KAFKA_SERVERS}
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
    consumer:
      group-id: ${spring.application.name}
      auto-offset-reset: earliest
  
  data:
    redis:
      host: ${REDIS_HOST}
      port: 6379

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      show-details: always

logging:
  level:
    root: INFO
    com.eprocurement: DEBUG
```

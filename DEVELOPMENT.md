# Development Guide

## Overview

This guide covers coding standards, development workflow, and best practices for contributing to the e-Procurement system.

---

## Project Conventions

### Package Structure

Each service follows a consistent package structure:

```
com.tugas_akhir.<service_name>/
├── config/                 # Configuration classes
├── controller/             # REST controllers
├── dto/                    # Data Transfer Objects
│   ├── request/           # Request DTOs
│   └── response/          # Response DTOs
├── domain/                 # Domain entities
│   └── <subdomain>/
│       ├── controller/
│       ├── service/
│       ├── repository/
│       └── model/
├── entity/                # JPA entities (if not in domain)
├── exception/              # Custom exceptions
├── mapper/                 # MapStruct mappers
├── repository/             # Spring Data repositories
├── security/               # Security configurations
├── service/                # Business logic services
└── common/                 # Shared utilities
    ├── base/              # Base classes
    └── util/              # Utility classes
```

### Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| Package | lowercase, underscores | `com.tugas_akhir.procurement_service` |
| Class | PascalCase | `ProcurementService` |
| Interface | PascalCase | `UserRepository` |
| Method | camelCase | `findByEmail()` |
| Variable | camelCase | `userName` |
| Constant | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| Database Table | snake_case | `procurement_requests` |
| API Endpoint | kebab-case | `/api/v1/procurement-requests` |

---

## Coding Standards

### Controller Layer

```java
@RestController
@RequestMapping("/api/v1/procurement-requests")
@RequiredArgsConstructor
@Tag(name = "Procurement Requests", description = "PR management endpoints")
public class ProcurementRequestController {

    private final ProcurementRequestService procurementService;

    @GetMapping
    @Operation(summary = "List all procurement requests")
    public ResponseEntity<Page<PRResponse>> list(
            @ParameterObject Pageable pageable,
            @RequestParam(required = false) PRStatus status) {
        return ResponseEntity.ok(procurementService.findAll(pageable, status));
    }

    @GetMapping("/{id}")
    @Operation(summary = "Get procurement request by ID")
    public ResponseEntity<PRResponse> getById(@PathVariable UUID id) {
        return ResponseEntity.ok(procurementService.findById(id));
    }

    @PostMapping
    @Operation(summary = "Create new procurement request")
    public ResponseEntity<PRResponse> create(
            @Valid @RequestBody CreatePRRequest request) {
        PRResponse response = procurementService.create(request);
        URI location = URI.create("/api/v1/procurement-requests/" + response.getId());
        return ResponseEntity.created(location).body(response);
    }

    @PutMapping("/{id}")
    @Operation(summary = "Update procurement request")
    public ResponseEntity<PRResponse> update(
            @PathVariable UUID id,
            @Valid @RequestBody UpdatePRRequest request) {
        return ResponseEntity.ok(procurementService.update(id, request));
    }

    @DeleteMapping("/{id}")
    @Operation(summary = "Delete procurement request")
    public ResponseEntity<Void> delete(@PathVariable UUID id) {
        procurementService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

### Service Layer

```java
@Service
@RequiredArgsConstructor
@Transactional(readOnly = true)
@Slf4j
public class ProcurementRequestService {

    private final ProcurementRequestRepository repository;
    private final PRMapper mapper;
    private final KafkaProducerService kafkaProducer;

    public Page<PRResponse> findAll(Pageable pageable, PRStatus status) {
        Page<ProcurementRequest> page = status != null
            ? repository.findByStatusAndIsDeletedFalse(status, pageable)
            : repository.findByIsDeletedFalse(pageable);
        
        return page.map(mapper::toResponse);
    }

    public PRResponse findById(UUID id) {
        ProcurementRequest pr = repository.findByIdAndIsDeletedFalse(id)
            .orElseThrow(() -> new ResourceNotFoundException("PR", id));
        
        return mapper.toResponse(pr);
    }

    @Transactional
    public PRResponse create(CreatePRRequest request) {
        log.info("Creating new PR for vendor: {}", request.getVendorId());
        
        ProcurementRequest pr = mapper.toEntity(request);
        pr = repository.save(pr);
        
        log.info("PR created with ID: {}", pr.getId());
        return mapper.toResponse(pr);
    }

    @Transactional
    public PRResponse update(UUID id, UpdatePRRequest request) {
        ProcurementRequest pr = repository.findByIdAndIsDeletedFalse(id)
            .orElseThrow(() -> new ResourceNotFoundException("PR", id));
        
        if (pr.getStatus() != PRStatus.DRAFT) {
            throw new BusinessException("Cannot update non-draft PR");
        }
        
        mapper.updateEntity(request, pr);
        pr = repository.save(pr);
        
        return mapper.toResponse(pr);
    }

    @Transactional
    public void delete(UUID id) {
        ProcurementRequest pr = repository.findByIdAndIsDeletedFalse(id)
            .orElseThrow(() -> new ResourceNotFoundException("PR", id));
        
        pr.setIsDeleted(true);
        pr.setDeletedAt(LocalDateTime.now());
        repository.save(pr);
        
        log.info("PR {} soft deleted", id);
    }

    @Transactional
    public void submit(UUID id) {
        ProcurementRequest pr = repository.findByIdAndIsDeletedFalse(id)
            .orElseThrow(() -> new ResourceNotFoundException("PR", id));
        
        if (pr.getStatus() != PRStatus.DRAFT) {
            throw new BusinessException("Only draft PRs can be submitted");
        }
        
        pr.setStatus(PRStatus.SUBMITTED);
        repository.save(pr);
        
        // Publish event
        kafkaProducer.publish("procurement.pr.submitted", new PRSubmittedEvent(pr));
        
        log.info("PR {} submitted", id);
    }
}
```

### Repository Layer

```java
public interface ProcurementRequestRepository 
        extends JpaRepository<ProcurementRequest, UUID> {

    // Standard queries with soft delete
    Optional<ProcurementRequest> findByIdAndIsDeletedFalse(UUID id);
    
    Page<ProcurementRequest> findByIsDeletedFalse(Pageable pageable);
    
    Page<ProcurementRequest> findByStatusAndIsDeletedFalse(
        PRStatus status, Pageable pageable);
    
    Page<ProcurementRequest> findByOperatorIdAndIsDeletedFalse(
        UUID operatorId, Pageable pageable);

    // Custom queries
    @Query("""
        SELECT pr FROM ProcurementRequest pr
        WHERE pr.isDeleted = false
        AND pr.status = :status
        AND pr.deadline < :date
        ORDER BY pr.priority DESC, pr.createdAt ASC
        """)
    List<ProcurementRequest> findOverduePRs(
        @Param("status") PRStatus status,
        @Param("date") LocalDateTime date);

    // Native query for complex reports
    @Query(nativeQuery = true, value = """
        SELECT DATE(created_at) as date, COUNT(*) as count
        FROM procurement_requests
        WHERE is_deleted = false
        AND created_at >= :startDate
        GROUP BY DATE(created_at)
        ORDER BY date
        """)
    List<Object[]> getPRCountByDate(@Param("startDate") LocalDateTime startDate);
}
```

### Entity Layer

```java
@Entity
@Table(name = "procurement_requests")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class ProcurementRequest extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.UUID)
    private UUID id;

    @Column(name = "operator_id", nullable = false)
    private UUID operatorId;

    @Column(name = "vendor_id", nullable = false)
    private UUID vendorId;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    private PRType type;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    @Builder.Default
    private PRPriority priority = PRPriority.NORMAL;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    @Builder.Default
    private PRStatus status = PRStatus.DRAFT;

    @Column(columnDefinition = "TEXT")
    private String description;

    private LocalDateTime deadline;

    @OneToMany(mappedBy = "procurementRequest", cascade = CascadeType.ALL, 
               orphanRemoval = true)
    @Builder.Default
    private List<ProcurementItem> items = new ArrayList<>();

    // Soft delete fields from BaseEntity
    // - isDeleted
    // - deletedAt
    // - createdAt
    // - updatedAt
    // - createdBy
    // - updatedBy
}
```

### DTO Layer

```java
// Request DTO with validation
@Data
@Builder
public class CreatePRRequest {

    @NotNull(message = "Vendor ID is required")
    private UUID vendorId;

    @NotNull(message = "Type is required")
    private PRType type;

    private PRPriority priority;

    @Size(max = 1000, message = "Description must be less than 1000 characters")
    private String description;

    @Future(message = "Deadline must be in the future")
    private LocalDateTime deadline;

    @NotEmpty(message = "At least one item is required")
    @Valid
    private List<CreatePRItemRequest> items;
}

// Response DTO
@Data
@Builder
public class PRResponse {
    private UUID id;
    private UUID operatorId;
    private UUID vendorId;
    private PRType type;
    private PRPriority priority;
    private PRStatus status;
    private String description;
    private LocalDateTime deadline;
    private List<PRItemResponse> items;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
}
```

### Mapper with MapStruct

```java
@Mapper(componentModel = "spring", 
        nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE)
public interface PRMapper {

    @Mapping(target = "id", ignore = true)
    @Mapping(target = "status", constant = "DRAFT")
    @Mapping(target = "createdAt", ignore = true)
    @Mapping(target = "updatedAt", ignore = true)
    ProcurementRequest toEntity(CreatePRRequest request);

    PRResponse toResponse(ProcurementRequest entity);

    List<PRResponse> toResponseList(List<ProcurementRequest> entities);

    @Mapping(target = "id", ignore = true)
    @Mapping(target = "status", ignore = true)
    void updateEntity(UpdatePRRequest request, @MappingTarget ProcurementRequest entity);
}
```

---

## Exception Handling

### Global Exception Handler

```java
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        log.warn("Resource not found: {}", ex.getMessage());
        
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .status(HttpStatus.NOT_FOUND.value())
            .errorCode("RESOURCE_NOT_FOUND")
            .message(ex.getMessage())
            .build();
        
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ErrorResponse> handleBusiness(BusinessException ex) {
        log.warn("Business rule violation: {}", ex.getMessage());
        
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .status(HttpStatus.UNPROCESSABLE_ENTITY.value())
            .errorCode(ex.getErrorCode())
            .message(ex.getMessage())
            .build();
        
        return ResponseEntity.status(HttpStatus.UNPROCESSABLE_ENTITY).body(error);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(
            MethodArgumentNotValidException ex) {
        
        List<String> errors = ex.getBindingResult()
            .getFieldErrors()
            .stream()
            .map(f -> f.getField() + ": " + f.getDefaultMessage())
            .collect(Collectors.toList());
        
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .status(HttpStatus.BAD_REQUEST.value())
            .errorCode("VALIDATION_ERROR")
            .message("Validation failed")
            .details(errors)
            .build();
        
        return ResponseEntity.badRequest().body(error);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneral(Exception ex) {
        log.error("Unexpected error occurred", ex);
        
        ErrorResponse error = ErrorResponse.builder()
            .timestamp(Instant.now())
            .status(HttpStatus.INTERNAL_SERVER_ERROR.value())
            .errorCode("INTERNAL_ERROR")
            .message("An unexpected error occurred")
            .build();
        
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(error);
    }
}
```

### Custom Exceptions

```java
public class ResourceNotFoundException extends RuntimeException {
    
    public ResourceNotFoundException(String resource, Object id) {
        super(String.format("%s not found with id: %s", resource, id));
    }
}

@Getter
public class BusinessException extends RuntimeException {
    
    private final String errorCode;
    
    public BusinessException(String message) {
        super(message);
        this.errorCode = "BUSINESS_ERROR";
    }
    
    public BusinessException(String errorCode, String message) {
        super(message);
        this.errorCode = errorCode;
    }
}
```

---

## Kafka Integration

### Producer

```java
@Service
@RequiredArgsConstructor
@Slf4j
public class KafkaProducerService {

    private final KafkaTemplate<String, Object> kafkaTemplate;

    public void publish(String topic, Object event) {
        log.info("Publishing event to topic {}: {}", topic, event);
        
        kafkaTemplate.send(topic, event)
            .whenComplete((result, ex) -> {
                if (ex != null) {
                    log.error("Failed to publish to {}", topic, ex);
                } else {
                    log.debug("Event published to {} at offset {}", 
                        topic, result.getRecordMetadata().offset());
                }
            });
    }
}
```

### Consumer

```java
@Service
@Slf4j
public class PREventConsumer {

    @KafkaListener(topics = "procurement.pr.submitted", 
                   groupId = "notification-group")
    public void handlePRSubmitted(PRSubmittedEvent event) {
        log.info("Received PR submitted event: {}", event.getPrId());
        
        // Process event
        sendNotification(event);
    }
    
    @KafkaListener(topics = "procurement.pr.approved",
                   groupId = "audit-group")
    public void handlePRApproved(PRApprovedEvent event) {
        log.info("Received PR approved event: {}", event.getPrId());
        
        // Log audit trail
        logAudit(event);
    }
}
```

---

## Testing Standards

### Unit Test

```java
@ExtendWith(MockitoExtension.class)
class ProcurementRequestServiceTest {

    @Mock
    private ProcurementRequestRepository repository;

    @Mock
    private PRMapper mapper;

    @InjectMocks
    private ProcurementRequestService service;

    @Test
    void findById_WhenExists_ShouldReturnPR() {
        // Arrange
        UUID id = UUID.randomUUID();
        ProcurementRequest pr = createTestPR(id);
        PRResponse expected = createTestResponse(id);
        
        when(repository.findByIdAndIsDeletedFalse(id))
            .thenReturn(Optional.of(pr));
        when(mapper.toResponse(pr))
            .thenReturn(expected);
        
        // Act
        PRResponse result = service.findById(id);
        
        // Assert
        assertThat(result).isNotNull();
        assertThat(result.getId()).isEqualTo(id);
        
        verify(repository).findByIdAndIsDeletedFalse(id);
    }

    @Test
    void findById_WhenNotExists_ShouldThrowException() {
        // Arrange
        UUID id = UUID.randomUUID();
        when(repository.findByIdAndIsDeletedFalse(id))
            .thenReturn(Optional.empty());
        
        // Act & Assert
        assertThatThrownBy(() -> service.findById(id))
            .isInstanceOf(ResourceNotFoundException.class)
            .hasMessageContaining(id.toString());
    }
}
```

### Integration Test

```java
@SpringBootTest
@AutoConfigureMockMvc
@Testcontainers
class ProcurementControllerIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine");

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }

    @Test
    @WithMockUser(roles = "OPERATOR")
    void createPR_WithValidRequest_ShouldReturnCreated() throws Exception {
        CreatePRRequest request = CreatePRRequest.builder()
            .vendorId(UUID.randomUUID())
            .type(PRType.GOODS)
            .description("Test PR")
            .build();

        mockMvc.perform(post("/api/v1/procurement-requests")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.id").exists())
            .andExpect(jsonPath("$.status").value("DRAFT"));
    }
}
```

---

## Git Workflow

### Branch Naming

```
feature/<ticket-id>-<short-description>
bugfix/<ticket-id>-<short-description>
hotfix/<ticket-id>-<short-description>
release/<version>
```

**Examples:**
```
feature/TA-123-add-vendor-approval
bugfix/TA-456-fix-pr-submission
hotfix/TA-789-security-patch
release/1.0.0
```

### Commit Messages

Follow Conventional Commits:

```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Code style (formatting)
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Maintenance tasks

**Examples:**
```
feat(procurement): add approval workflow
fix(auth): resolve token refresh issue
docs(readme): update API documentation
refactor(user): extract validation logic
test(vendor): add integration tests
```

---

## Development Checklist

Before committing, ensure:

- [ ] Code compiles without errors
- [ ] All tests pass (`./mvnw test`)
- [ ] No TODO comments in production code
- [ ] API endpoints documented with OpenAPI annotations
- [ ] Proper logging added
- [ ] Exception handling implemented
- [ ] Validation annotations on DTOs
- [ ] Database migrations created if needed
- [ ] Soft delete pattern followed

---

## Next Steps

- [Testing Guide](./TESTING.md) - Detailed testing strategies
- [API Reference](./API_REFERENCE.md) - API documentation
- [Security Guide](./SECURITY.md) - Security best practices

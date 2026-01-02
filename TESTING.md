# Testing Guide

## Overview

This guide covers testing strategies, tools, and best practices for the e-Procurement microservices system.

---

## Testing Pyramid

```
                    ┌─────────────┐
                    │  E2E Tests  │  ← Few, slow, high confidence
                    │  (Browser)  │
                    └─────────────┘
               ┌─────────────────────┐
               │  Integration Tests  │  ← Medium amount
               │  (API, Database)    │
               └─────────────────────┘
          ┌───────────────────────────────┐
          │        Unit Tests            │  ← Many, fast, focused
          │  (Services, Utilities)       │
          └───────────────────────────────┘
```

---

## Test Stack

| Type | Tool | Purpose |
|------|------|---------|
| Unit Tests | JUnit 5 + Mockito | Test isolated components |
| Integration Tests | Spring Boot Test | Test with real dependencies |
| Container Tests | Testcontainers | Test with real databases |
| API Tests | MockMvc | Test REST endpoints |
| Performance Tests | JMeter | Load and stress testing |

---

## Unit Testing

### Service Layer Tests

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @Mock
    private UserMapper userMapper;

    @InjectMocks
    private UserService userService;

    private User testUser;
    private UserResponse testResponse;

    @BeforeEach
    void setUp() {
        testUser = User.builder()
            .id(UUID.randomUUID())
            .email("test@example.com")
            .fullName("Test User")
            .status(UserStatus.ACTIVE)
            .isDeleted(false)
            .build();

        testResponse = UserResponse.builder()
            .id(testUser.getId())
            .email(testUser.getEmail())
            .fullName(testUser.getFullName())
            .status(testUser.getStatus())
            .build();
    }

    @Test
    @DisplayName("Should return user when found by ID")
    void findById_WhenUserExists_ReturnsUser() {
        // Arrange
        when(userRepository.findByIdAndIsDeletedFalse(testUser.getId()))
            .thenReturn(Optional.of(testUser));
        when(userMapper.toResponse(testUser))
            .thenReturn(testResponse);

        // Act
        UserResponse result = userService.findById(testUser.getId());

        // Assert
        assertThat(result).isNotNull();
        assertThat(result.getId()).isEqualTo(testUser.getId());
        assertThat(result.getEmail()).isEqualTo("test@example.com");

        verify(userRepository).findByIdAndIsDeletedFalse(testUser.getId());
    }

    @Test
    @DisplayName("Should throw exception when user not found")
    void findById_WhenUserNotExists_ThrowsException() {
        // Arrange
        UUID nonExistentId = UUID.randomUUID();
        when(userRepository.findByIdAndIsDeletedFalse(nonExistentId))
            .thenReturn(Optional.empty());

        // Act & Assert
        assertThatThrownBy(() -> userService.findById(nonExistentId))
            .isInstanceOf(ResourceNotFoundException.class)
            .hasMessageContaining("User")
            .hasMessageContaining(nonExistentId.toString());
    }

    @Test
    @DisplayName("Should create user with valid request")
    void create_WithValidRequest_CreatesUser() {
        // Arrange
        CreateUserRequest request = CreateUserRequest.builder()
            .email("new@example.com")
            .fullName("New User")
            .build();

        User newUser = User.builder()
            .id(UUID.randomUUID())
            .email(request.getEmail())
            .fullName(request.getFullName())
            .status(UserStatus.ACTIVE)
            .build();

        when(userMapper.toEntity(request)).thenReturn(newUser);
        when(userRepository.save(newUser)).thenReturn(newUser);
        when(userMapper.toResponse(newUser)).thenReturn(
            UserResponse.builder()
                .id(newUser.getId())
                .email(newUser.getEmail())
                .build()
        );

        // Act
        UserResponse result = userService.create(request);

        // Assert
        assertThat(result).isNotNull();
        assertThat(result.getEmail()).isEqualTo("new@example.com");

        verify(userRepository).save(any(User.class));
    }

    @Test
    @DisplayName("Should soft delete user")
    void delete_SoftDeletesUser() {
        // Arrange
        when(userRepository.findByIdAndIsDeletedFalse(testUser.getId()))
            .thenReturn(Optional.of(testUser));
        when(userRepository.save(any(User.class)))
            .thenAnswer(invocation -> invocation.getArgument(0));

        // Act
        userService.delete(testUser.getId());

        // Assert
        ArgumentCaptor<User> userCaptor = ArgumentCaptor.forClass(User.class);
        verify(userRepository).save(userCaptor.capture());

        User savedUser = userCaptor.getValue();
        assertThat(savedUser.getIsDeleted()).isTrue();
        assertThat(savedUser.getDeletedAt()).isNotNull();
    }
}
```

### Controller Layer Tests

```java
@WebMvcTest(UserController.class)
@Import(SecurityTestConfig.class)
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @MockBean
    private UserService userService;

    @Test
    @WithMockUser(roles = "ADMIN")
    @DisplayName("GET /users/{id} - Should return user")
    void getById_ReturnsUser() throws Exception {
        UUID userId = UUID.randomUUID();
        UserResponse response = UserResponse.builder()
            .id(userId)
            .email("test@example.com")
            .fullName("Test User")
            .build();

        when(userService.findById(userId)).thenReturn(response);

        mockMvc.perform(get("/api/v1/users/{id}", userId))
            .andExpect(status().isOk())
            .andExpect(content().contentType(MediaType.APPLICATION_JSON))
            .andExpect(jsonPath("$.id").value(userId.toString()))
            .andExpect(jsonPath("$.email").value("test@example.com"));
    }

    @Test
    @WithMockUser(roles = "ADMIN")
    @DisplayName("POST /users - Should create user")
    void create_WithValidRequest_ReturnsCreated() throws Exception {
        CreateUserRequest request = CreateUserRequest.builder()
            .email("new@example.com")
            .fullName("New User")
            .build();

        UUID newId = UUID.randomUUID();
        UserResponse response = UserResponse.builder()
            .id(newId)
            .email(request.getEmail())
            .fullName(request.getFullName())
            .build();

        when(userService.create(any(CreateUserRequest.class))).thenReturn(response);

        mockMvc.perform(post("/api/v1/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isCreated())
            .andExpect(header().exists("Location"))
            .andExpect(jsonPath("$.id").value(newId.toString()));
    }

    @Test
    @WithMockUser(roles = "ADMIN")
    @DisplayName("POST /users - Should return 400 for invalid request")
    void create_WithInvalidRequest_ReturnsBadRequest() throws Exception {
        CreateUserRequest request = CreateUserRequest.builder()
            .email("invalid-email")  // Invalid email format
            .fullName("")  // Empty name
            .build();

        mockMvc.perform(post("/api/v1/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isBadRequest())
            .andExpect(jsonPath("$.errorCode").value("VALIDATION_ERROR"));
    }

    @Test
    @WithMockUser(roles = "USER")
    @DisplayName("DELETE /users/{id} - Should return 403 for non-admin")
    void delete_AsNonAdmin_ReturnsForbidden() throws Exception {
        UUID userId = UUID.randomUUID();

        mockMvc.perform(delete("/api/v1/users/{id}", userId))
            .andExpect(status().isForbidden());
    }
}
```

---

## Integration Testing

### Repository Tests

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Testcontainers
class UserRepositoryIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine")
        .withDatabaseName("testdb")
        .withUsername("test")
        .withPassword("test");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private TestEntityManager entityManager;

    @Test
    @DisplayName("Should find user by email")
    void findByEmail_WhenExists_ReturnsUser() {
        // Arrange
        User user = User.builder()
            .email("test@example.com")
            .fullName("Test User")
            .status(UserStatus.ACTIVE)
            .isDeleted(false)
            .build();
        entityManager.persistAndFlush(user);

        // Act
        Optional<User> result = userRepository.findByEmailAndIsDeletedFalse("test@example.com");

        // Assert
        assertThat(result).isPresent();
        assertThat(result.get().getEmail()).isEqualTo("test@example.com");
    }

    @Test
    @DisplayName("Should not find soft-deleted users")
    void findByEmail_WhenDeleted_ReturnsEmpty() {
        // Arrange
        User user = User.builder()
            .email("deleted@example.com")
            .fullName("Deleted User")
            .status(UserStatus.ACTIVE)
            .isDeleted(true)
            .deletedAt(LocalDateTime.now())
            .build();
        entityManager.persistAndFlush(user);

        // Act
        Optional<User> result = userRepository.findByEmailAndIsDeletedFalse("deleted@example.com");

        // Assert
        assertThat(result).isEmpty();
    }

    @Test
    @DisplayName("Should count users by status")
    void countByStatus_ReturnsCorrectCount() {
        // Arrange
        entityManager.persist(User.builder()
            .email("active1@example.com").fullName("Active 1")
            .status(UserStatus.ACTIVE).isDeleted(false).build());
        entityManager.persist(User.builder()
            .email("active2@example.com").fullName("Active 2")
            .status(UserStatus.ACTIVE).isDeleted(false).build());
        entityManager.persist(User.builder()
            .email("inactive@example.com").fullName("Inactive")
            .status(UserStatus.INACTIVE).isDeleted(false).build());
        entityManager.flush();

        // Act
        long activeCount = userRepository.countByStatusAndIsDeletedFalse(UserStatus.ACTIVE);

        // Assert
        assertThat(activeCount).isEqualTo(2);
    }
}
```

### Full Integration Tests

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@Testcontainers
@ActiveProfiles("test")
class ProcurementIntegrationTest {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine");

    @Container
    static KafkaContainer kafka = new KafkaContainer(
        DockerImageName.parse("confluentinc/cp-kafka:7.7.1"));

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
        registry.add("spring.kafka.bootstrap-servers", kafka::getBootstrapServers);
    }

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private ProcurementRequestRepository repository;

    @Test
    @DisplayName("Full PR workflow - Create, Submit, Approve")
    void fullPRWorkflow() {
        // 1. Create PR
        CreatePRRequest createRequest = CreatePRRequest.builder()
            .vendorId(UUID.randomUUID())
            .type(PRType.GOODS)
            .description("Test procurement")
            .items(List.of(
                CreatePRItemRequest.builder()
                    .itemName("Test Item")
                    .quantity(10)
                    .unitPrice(BigDecimal.valueOf(1000))
                    .build()
            ))
            .build();

        ResponseEntity<PRResponse> createResponse = restTemplate
            .withBasicAuth("operator", "password")
            .postForEntity("/api/v1/procurement-requests", createRequest, PRResponse.class);

        assertThat(createResponse.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        UUID prId = createResponse.getBody().getId();

        // 2. Submit PR
        ResponseEntity<Void> submitResponse = restTemplate
            .withBasicAuth("operator", "password")
            .postForEntity("/api/v1/procurement-requests/{id}/submit", null, Void.class, prId);

        assertThat(submitResponse.getStatusCode()).isEqualTo(HttpStatus.OK);

        // 3. Approve PR
        ApprovalRequest approvalRequest = ApprovalRequest.builder()
            .notes("Approved for procurement")
            .build();

        ResponseEntity<Void> approveResponse = restTemplate
            .withBasicAuth("supervisor", "password")
            .postForEntity("/api/v1/procurement-requests/{id}/approve", 
                          approvalRequest, Void.class, prId);

        assertThat(approveResponse.getStatusCode()).isEqualTo(HttpStatus.OK);

        // 4. Verify final state
        ProcurementRequest pr = repository.findById(prId).orElseThrow();
        assertThat(pr.getStatus()).isEqualTo(PRStatus.APPROVED);
    }
}
```

---

## Test Configuration

### Test Properties

```properties
# src/test/resources/application-test.properties

# Use H2 for simple tests
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.jpa.hibernate.ddl-auto=create-drop

# Disable Flyway for H2
spring.flyway.enabled=false

# Disable Kafka for unit tests
spring.kafka.bootstrap-servers=

# Faster startup
spring.main.lazy-initialization=true
logging.level.root=WARN
```

### Security Test Configuration

```java
@TestConfiguration
public class SecurityTestConfig {

    @Bean
    public SecurityFilterChain testSecurityFilterChain(HttpSecurity http) throws Exception {
        return http
            .authorizeHttpRequests(auth -> auth
                .anyRequest().authenticated())
            .httpBasic(Customizer.withDefaults())
            .csrf(csrf -> csrf.disable())
            .build();
    }
}
```

---

## Running Tests

### Command Line

```bash
# Run all tests
./mvnw test

# Run specific test class
./mvnw test -Dtest=UserServiceTest

# Run specific test method
./mvnw test -Dtest=UserServiceTest#findById_WhenUserExists_ReturnsUser

# Run integration tests only
./mvnw verify -Dskip.unit.tests=true

# Run with coverage
./mvnw test jacoco:report
```

### Test Output

```bash
# View test results
target/surefire-reports/

# View coverage report
target/site/jacoco/index.html
```

---

## Test Data Builders

```java
public class TestDataBuilder {

    public static User.UserBuilder aUser() {
        return User.builder()
            .id(UUID.randomUUID())
            .email("test@example.com")
            .fullName("Test User")
            .status(UserStatus.ACTIVE)
            .isDeleted(false)
            .createdAt(LocalDateTime.now());
    }

    public static ProcurementRequest.ProcurementRequestBuilder aPR() {
        return ProcurementRequest.builder()
            .id(UUID.randomUUID())
            .operatorId(UUID.randomUUID())
            .vendorId(UUID.randomUUID())
            .type(PRType.GOODS)
            .priority(PRPriority.NORMAL)
            .status(PRStatus.DRAFT)
            .description("Test PR")
            .isDeleted(false)
            .createdAt(LocalDateTime.now());
    }

    public static CreatePRRequest.CreatePRRequestBuilder aCreatePRRequest() {
        return CreatePRRequest.builder()
            .vendorId(UUID.randomUUID())
            .type(PRType.GOODS)
            .priority(PRPriority.NORMAL)
            .description("Test procurement request")
            .items(List.of(aCreateItemRequest().build()));
    }

    public static CreatePRItemRequest.CreatePRItemRequestBuilder aCreateItemRequest() {
        return CreatePRItemRequest.builder()
            .itemName("Test Item")
            .quantity(10)
            .unitPrice(BigDecimal.valueOf(1000))
            .specification("Standard quality");
    }
}
```

**Usage:**
```java
@Test
void example() {
    User user = aUser()
        .email("custom@example.com")
        .status(UserStatus.INACTIVE)
        .build();

    ProcurementRequest pr = aPR()
        .operatorId(user.getId())
        .priority(PRPriority.URGENT)
        .build();
}
```

---

## Test Coverage Requirements

| Layer | Minimum Coverage |
|-------|-----------------|
| Service | 80% |
| Controller | 70% |
| Repository | 60% |
| Utility | 90% |
| Overall | 75% |

### Jacoco Configuration

```xml
<!-- pom.xml -->
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.11</version>
    <executions>
        <execution>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
        </execution>
        <execution>
            <id>report</id>
            <phase>test</phase>
            <goals>
                <goal>report</goal>
            </goals>
        </execution>
        <execution>
            <id>check</id>
            <goals>
                <goal>check</goal>
            </goals>
            <configuration>
                <rules>
                    <rule>
                        <element>BUNDLE</element>
                        <limits>
                            <limit>
                                <counter>INSTRUCTION</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.75</minimum>
                            </limit>
                        </limits>
                    </rule>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>
```

---

## Best Practices

### 1. Test Naming Convention

```java
// Pattern: methodName_scenario_expectedResult
@Test
void findById_WhenUserExists_ReturnsUser() { }

@Test
void create_WithDuplicateEmail_ThrowsException() { }

@Test
void submit_WhenPRNotInDraftStatus_ThrowsBusinessException() { }
```

### 2. Arrange-Act-Assert Pattern

```java
@Test
void exampleTest() {
    // Arrange - Set up test data and mocks
    UUID userId = UUID.randomUUID();
    when(repository.findById(userId)).thenReturn(Optional.of(testUser));

    // Act - Execute the method under test
    UserResponse result = service.findById(userId);

    // Assert - Verify the results
    assertThat(result).isNotNull();
    assertThat(result.getId()).isEqualTo(userId);
}
```

### 3. Test Independence

Each test should be independent and not rely on the state from other tests.

### 4. Use Descriptive Assertions

```java
// ❌ Less informative
assertTrue(result.size() == 3);

// ✅ More informative
assertThat(result)
    .hasSize(3)
    .extracting(User::getStatus)
    .containsOnly(UserStatus.ACTIVE);
```

---

## Next Steps

- [Development Guide](./DEVELOPMENT.md) - Coding standards
- [API Reference](./API_REFERENCE.md) - API documentation
- [Security Guide](./SECURITY.md) - Security testing

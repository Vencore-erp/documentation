# Getting Started

## Overview

This guide will help you set up the e-Procurement development environment and run the microservices locally.

## Prerequisites

Ensure you have the following installed:

| Software | Minimum Version | Download |
|----------|----------------|----------|
| Java JDK | 21 | [Adoptium](https://adoptium.net/) |
| Maven | 3.9+ | [Apache Maven](https://maven.apache.org/) |
| Docker | 24.0+ | [Docker Desktop](https://www.docker.com/products/docker-desktop/) |
| Docker Compose | 2.20+ | Included with Docker Desktop |
| Git | 2.40+ | [Git](https://git-scm.com/) |
| IDE | - | IntelliJ IDEA / VS Code |

### Verify Installation

```powershell
# Check Java version
java --version
# Expected: openjdk 21.x.x

# Check Maven version
mvn --version
# Expected: Apache Maven 3.9.x

# Check Docker version
docker --version
# Expected: Docker version 24.x.x

# Check Docker Compose version
docker compose version
# Expected: Docker Compose version v2.x.x
```

---

## Project Structure

```
tugas-akhir/
├── backend/
│   ├── infra/                    # Docker infrastructure
│   │   ├── docker-compose.yml    # Infrastructure services
│   │   └── data/                 # Persistent data volumes
│   └── services/                 # Microservices
│       ├── admin-service/
│       ├── audit-service/
│       ├── auth-service/
│       ├── document-service/
│       ├── gateway-service/
│       ├── inventory-service/
│       ├── masterdata-service/
│       ├── notification-service/
│       ├── payment-service/
│       ├── procurement-service/
│       ├── reporting-service/
│       ├── user-service/
│       ├── vendor-service/
│       └── workflow-service/
└── documentation/                # Project documentation
```

---

## Quick Start

### Step 1: Clone the Repository

```powershell
git clone <repository-url>
cd tugas-akhir
```

### Step 2: Start Infrastructure Services

Start PostgreSQL, Kafka, and Redis using Docker Compose:

```powershell
cd backend/infra
docker compose up -d
```

**Verify services are running:**
```powershell
docker compose ps
```

Expected output:
```
NAME             SERVICE        STATUS
kafka_main       kafka          Up
pgadmin_ui       pgadmin        Up
postgres_main    postgres       Up (healthy)
redis_insight_ui redis_insight  Up
redis_main       redis          Up (healthy)
```

### Step 3: Access Admin UIs

| Service | URL | Credentials |
|---------|-----|-------------|
| PgAdmin | http://localhost:5050 | admin@local.com / admin |
| Redis Insight | http://localhost:8001 | - |

### Step 4: Run Auth Service

The Auth Service should be started first as other services depend on it for JWT validation.

```powershell
cd ../services/auth-service/auth-service
./mvnw spring-boot:run
```

**Verify:**
- Health: http://localhost:8081/actuator/health
- Swagger UI: http://localhost:8081/swagger-ui.html

### Step 5: Run Other Services

Open new terminal windows for each service:

```powershell
# User Service
cd backend/services/user-service/user-service
./mvnw spring-boot:run

# Procurement Service
cd backend/services/procurement-service/procurement-service
./mvnw spring-boot:run

# Vendor Service
cd backend/services/vendor-service/vendor-service
./mvnw spring-boot:run

# Gateway Service
cd backend/services/gateway-service/gateway-service
./mvnw spring-boot:run
```

---

## Service Startup Order

For optimal startup, follow this order:

```
1. Infrastructure (Docker Compose)
   └── PostgreSQL, Kafka, Redis
   
2. Auth Service (Port 8081)
   └── JWT issuer for all services
   
3. Master Data Service (Port 8083)
   └── Reference data
   
4. User Service (Port 8082)
   └── User management

5. Vendor Service (Port 8085)
   └── Vendor management
   
6. Procurement Service (Port 8084)
   └── Core business logic
   
7. Supporting Services
   ├── Notification Service (Port 8088)
   ├── Audit Service (Port 8090)
   ├── Inventory Service (Port 8093)
   └── Admin Service (Port 8087)

8. Gateway Service (Port 8080)
   └── API entry point
```

---

## Database Setup

### Connect to PostgreSQL

Using PgAdmin (http://localhost:5050):

1. Login with `admin@local.com` / `admin`
2. Add new server:
   - Host: `postgres_main` (or `localhost` from host machine)
   - Port: `5432`
   - Database: `masterdb`
   - Username: `postgres`
   - Password: `postgres`

### Manual Database Connection

```powershell
# Connect via Docker
docker exec -it postgres_main psql -U postgres -d masterdb

# Or using psql directly (if installed)
psql -h localhost -p 5432 -U postgres -d masterdb
```

### View Tables

```sql
-- List all tables
\dt

-- Describe a table
\d users

-- View data
SELECT * FROM users LIMIT 10;
```

---

## Running Tests

### Unit Tests

```powershell
cd backend/services/<service-name>/<service-folder>
./mvnw test
```

### Integration Tests

Integration tests use Testcontainers for PostgreSQL and Kafka:

```powershell
./mvnw verify
```

### Test with Coverage

```powershell
./mvnw test jacoco:report
```

Reports generated at: `target/site/jacoco/index.html`

---

## IDE Setup

### IntelliJ IDEA

1. **Import Project**
   - File → Open → Select `tugas-akhir/backend/services/<service-name>/<service-folder>`
   
2. **Enable Annotation Processing** (for Lombok/MapStruct)
   - Settings → Build → Compiler → Annotation Processors
   - Check "Enable annotation processing"

3. **Configure JDK**
   - Project Structure → Project → SDK → Java 21

4. **Run Configuration**
   - Add new Spring Boot configuration
   - Main class: `*Application.java`
   - Working directory: Service root

### VS Code

1. **Install Extensions**
   - Extension Pack for Java
   - Spring Boot Extension Pack
   - Lombok Annotations Support

2. **Configure Java**
   - `Ctrl+Shift+P` → Java: Configure Java Runtime
   - Select JDK 21

---

## Environment Variables

Default development configuration is in `application.properties`. For custom settings:

### Option 1: Environment Variables

```powershell
$env:SPRING_DATASOURCE_URL = "jdbc:postgresql://localhost:5432/custom_db"
$env:SPRING_DATASOURCE_PASSWORD = "custom_password"
./mvnw spring-boot:run
```

### Option 2: Profile-Specific Properties

Create `application-dev.properties`:
```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/custom_db
```

Run with profile:
```powershell
./mvnw spring-boot:run -Dspring-boot.run.profiles=dev
```

---

## API Testing

### Using Swagger UI

Each service provides Swagger UI at `/swagger-ui.html`:
- http://localhost:8081/swagger-ui.html (Auth)
- http://localhost:8084/swagger-ui.html (Procurement)

### Using cURL

```bash
# 1. Login to get token
curl -X POST http://localhost:8081/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"admin123"}'

# 2. Use token for authenticated requests
curl -X GET http://localhost:8082/api/v1/users/internal \
  -H "Authorization: Bearer <access_token>"
```

### Using Postman

1. Import the API collection (if available)
2. Set environment variable `base_url` = `http://localhost:8080`
3. Use Pre-request scripts to handle JWT tokens

---

## Troubleshooting

### Port Already in Use

```powershell
# Find process using port
netstat -ano | findstr :8081

# Kill process
taskkill /PID <process_id> /F
```

### Docker Containers Not Starting

```powershell
# View logs
docker compose logs -f <service_name>

# Restart services
docker compose down
docker compose up -d
```

### Database Connection Issues

1. Check PostgreSQL is running:
   ```powershell
   docker compose ps postgres
   ```

2. Verify connection settings in `application.properties`

3. Check if database exists:
   ```powershell
   docker exec postgres_main psql -U postgres -c "\l"
   ```

### Maven Build Failures

```powershell
# Clean and rebuild
./mvnw clean install -DskipTests

# Update dependencies
./mvnw dependency:resolve
```

### Flyway Migration Errors

```powershell
# Reset Flyway history (development only!)
./mvnw flyway:clean
./mvnw flyway:migrate
```

---

## Useful Commands

### Docker Compose

```powershell
# Start all infrastructure
docker compose up -d

# Stop all services
docker compose down

# View logs
docker compose logs -f

# Restart specific service
docker compose restart postgres
```

### Maven

```powershell
# Clean build
./mvnw clean install

# Run without tests
./mvnw spring-boot:run -DskipTests

# Check dependency tree
./mvnw dependency:tree

# Run specific test
./mvnw test -Dtest=UserServiceTest
```

---

## Next Steps

- [Development Guide](./DEVELOPMENT.md) - Coding standards
- [API Reference](./API_REFERENCE.md) - API documentation
- [Configuration Guide](./CONFIGURATION.md) - Environment settings
- [Testing Guide](./TESTING.md) - Testing strategies

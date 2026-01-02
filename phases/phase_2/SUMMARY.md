# Phase 2: Infrastructure & Configuration (COMPLETED)

## Objective
Audit service configurations (`application.properties`), standardize security, and prepare the local development environment.

## Key Achievements

### 1. Security Standardization
*   **Authentication:** Unified **HS256** (Symmetric Key) across all services.
*   **Token Validation:**
    *   Configured **Local Validation** using `jwt.secret` for high-traffic services (Inventory, Procurement, etc.).
    *   Configured **Remote Validation** (Auth Service call) for others.
*   **SecurityConfig:** Standardized whitelist for `/actuator/**` and `/swagger-ui/**`.

### 2. Infrastructure Fixes
*   **Port Conflicts:** Changed **Audit Service** from `8086` to `8090` (Configured 8086 was reserved for Payment).
*   **Service Discovery:** Updated configurations to point to `localhost` ports for Hybrid Development.

### 3. Development Workflow Strategy
*   **Adopted Strategy:** Hybrid Workflow.
    *   **Docker:** Runs Core Infra (PostgreSQL, Kafka, Redis, Zipkin).
    *   **Localhost:** Runs Java Microservices via IDE (IntelliJ/VSCode).
*   **Benefit:** Enables Hot-Reload and faster debugging without rebuilding Docker images constantly.

### 4. Docker Compose
*   Verified `backend/infra/docker-compose.yml` is ready for infrastructure bootstrapping.

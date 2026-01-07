# Sprint Plan 2: Backend Core & Infrastructure
**Goal:** Membangun fondasi arsitektur Microservices yang aman dan scalable.
**Durasi:** 1-2 Minggu.

---

## 🎯 Objectives
1.  Setup Mono-repo/Poly-repo untuk Microservices Spring Boot.
2.  Implementasi Database & Infrastructure (Docker Compose).
3.  Implementasi **Authentication Service** (JWT + Role Based).
4.  Implementasi **API Gateway** untuk routing.

---

## 📋 Task List

### 1. Infrastructure Setup
- [ ] **Docker:** Buat `docker-compose.yml` utama.
  - [ ] Service: PostgreSQL (Database).
  - [ ] Service: Redis (Caching/Session).
  - [ ] Service: Kafka + Zookeeper (Event Bus).
  - [ ] Service: MinIO (File Storage Local).
- [ ] **Database:** Init script untuk create user/schema (`auth_db`, `procurement_db`, `finance_db`).

### 2. Service: API Gateway (Spring Cloud Gateway)
- [ ] Init Spring Boot Project.
- [ ] Config: Route definition untuk `/api/auth/**`, `/api/procurement/**`.
- [ ] Filter: Global Error Handling & Logging.
- [ ] Filter: CORS Configuration (Allow localhost frontend).

### 3. Service: Auth Service (Spring Security)
- [ ] **Data Model:** Entity `User`, `Role`, `Permission`.
- [ ] **Security:** Implementasi JWT Token Generation (RS256).
- [ ] **API:** `POST /auth/login` (Return Access & Refresh Token).
- [ ] **API:** `POST /auth/refresh`.
- [ ] **Seed:** Create default users (Admin, Operator, Spv, Finance, Vendor).

### 4. Service: User Service
- [ ] **CRUD:** Manage User Internal & Vendor Profile.
- [ ] **Integration:** Listen event `USER_CREATED`.

---

## 🧪 Definition of Done
- [ ] Semua service bisa jalan via Docker Compose `docker-compose up`.
- [ ] Bisa login via Postman ke `http://localhost:8080/api/auth/login` dan dapat Token.
- [ ] Token bisa didecode dan berisi Role/Claims yang benar.
- [ ] Unit Test Coverage min 40% untuk Utility Class.

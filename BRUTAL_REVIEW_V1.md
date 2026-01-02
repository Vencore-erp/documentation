# 🔥 Brutal Code Review - Version 1

**Review Date:** January 2, 2026  
**Reviewer:** AI Code Reviewer  
**Project:** e-Procurement Microservices System  
**Overall Score:** **6.5/10**

---

## Executive Summary

Project ini **cukup bagus untuk skala Tugas Akhir**, tapi kalau ini mau jadi production-ready enterprise system, masih **banyak yang harus diperbaiki**.

| Aspect | Score | Notes |
|--------|-------|-------|
| **Architecture** | 7/10 | Microservices baik, tapi over-engineered untuk TA |
| **Code Quality** | 5/10 | Inconsistent, banyak RuntimeException |
| **Testing** | 3/10 | Sangat minimal |
| **Security** | 6/10 | JWT bagus, tapi ada bugs |
| **Documentation** | 8/10 | Lengkap setelah update |
| **Deployment Readiness** | 4/10 | Docker not tested, version issues |
| **Database Design** | 7/10 | Migrations bagus, soft delete ✓ |

---

## 🚨 CRITICAL ISSUES (Harus Diperbaiki Segera)

### 1. Exception Handling yang MALAS

**Files Affected:** `auth-service/../AuthService.java`, `AdminService.java`, `VendorService.java`

```java
// SEMUA ERROR PAKAI RuntimeException generik!
throw new RuntimeException("User not found");
throw new RuntimeException("User account is not active");
throw new RuntimeException("Invalid or expired refresh token");
throw new RuntimeException("Passwords do not match");
throw new RuntimeException("Token expired or already used");
throw new RuntimeException("Email already registered");
throw new RuntimeException("Username or Email already exists");
```

**Masalah:**
- Sudah punya `GlobalExceptionHandler` di banyak service, TAPI di Auth Service masih pakai `RuntimeException` generik
- Error codes tidak konsisten - padahal sudah define standardized `ErrorResponse`
- Tidak ada custom exceptions seperti `AuthenticationException`, `TokenExpiredException`, `UserNotFoundException`

**Seharusnya:**
```java
throw new AuthenticationException("AUTH_001", "User account is not active");
throw new TokenException("TOKEN_001", "Invalid or expired refresh token");
```

**Priority:** 🔴 URGENT

---

### 2. Inconsistent Package Naming

| Service | Package | Status |
|---------|---------|--------|
| auth-service | `com.tugas_akhir.auth_service` | ✅ OK |
| user-service | `com.tugas_akhir.user_service` | ✅ OK |
| procurement-service | `com.tugas_akhir.procurement_service` | ✅ OK |
| vendor-service | `com.tugasakhir.vendor_service` | ❌ BEDA (tanpa underscore) |
| master-data-service | `com.tugas_akhir.master_data` | ❌ BEDA (bukan master_data_service) |

**Impact:**
- Inter-service communication complexity
- Shared libraries akan sulit
- Code generation inconsistent

**Priority:** 🔴 URGENT

---

### 3. Spring Boot Version TIDAK EXIST

**Files Affected:** All `pom.xml` files

```xml
<parent>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>4.0.0</version>  <!-- INI TIDAK EXIST! -->
</parent>
```

**Problem:** Spring Boot 4.0.0 belum release! Versi terbaru adalah 3.4.x (per January 2026).

**Kemungkinan:**
- Project tidak pernah di-build properly
- Placeholder yang lupa diganti
- Copy-paste error

**Fix:** Ganti ke `3.4.1` atau versi stable terbaru

**Priority:** 🔴 URGENT

---

### 4. Duplicate Migration Files

**Location:** `src/main/resources/db/migration/`

```
admin-service:
  V1__Init_Schema.sql
  V1__init_admin_schema.sql     ← CONFLICT!

procurement-service:
  V1__create_procurement_requests_table.sql
  V1__init_procurement_schema.sql  ← CONFLICT!
  V1__init_schema.sql              ← CONFLICT!

notification-service:
  V1__init_notification_schema.sql
  V1__init_schema.sql              ← CONFLICT!
```

**Problem:** Flyway akan FAIL jika ada 2 migration dengan version yang sama.

**Fix:** Rename migrations dengan version yang sequential

**Priority:** 🔴 URGENT

---

## ⚠️ MAJOR ISSUES

### 5. Test Coverage Sangat Minimal

**Analysis:**

| Service | Test Files | Status |
|---------|------------|--------|
| auth-service | 4 files | ✅ Acceptable |
| vendor-service | 7 files | ✅ Good |
| procurement-service | 3 files | ⚠️ Minimal |
| admin-service | 2 files | ⚠️ Kafka only |
| user-service | **0 files** | ❌ NONE |
| masterdata-service | **0 files** | ❌ NONE |
| inventory-service | **0 files** | ❌ NONE |
| notification-service | **0 files** | ❌ NONE |
| audit-service | **0 files** | ❌ NONE |
| gateway-service | **0 files** | ❌ NONE |

**Total:** 22 test files untuk 14 services = **Sangat kurang!**

**Recommended:** Minimum 80% coverage untuk service layer

**Priority:** 🟠 HIGH

---

### 6. Missing @Transactional Annotations

**File:** `auth-service/../AuthService.java`

```java
// login() tidak ada @Transactional padahal ada WRITE operation!
public TokenResponse login(LoginRequest request) {
    User user = userRepository.findByUsername(request.getUsername())...
    user.setLastLoginAt(LocalDateTime.now()); 
    userRepository.save(user);  // WRITE tanpa @Transactional!
}
```

**Impact:** Potential data consistency issues

**Priority:** 🟠 HIGH

---

### 7. Security Bug: Logout Audit Username

**File:** `auth-service/../AuthService.java:97`

```java
public void logout(LogoutRequest request) {
    // ...
    auditPublisher.publishEvent("LOGOUT", "unknown", "User logged out");
    //                                   ^^^^^^^^^ SELALU "unknown"!
}
```

**Problem:** Username should be extracted from token before blacklisting

**Priority:** 🟠 HIGH

---

### 8. Contradictory Feign Configuration

**File:** `procurement-service/../application.properties`

```java
// Application class
@EnableFeignClients  // Enabled!

// Properties
spring.cloud.openfeign.autoconfiguration.enabled=false  // Disabled?!
```

**Problem:** Contradictory configuration

**Priority:** 🟠 HIGH

---

## 🟡 MODERATE ISSUES

### 9. Inconsistent API Versioning

```
/api/v1/auth        ← With version
/api/auth/**        ← Gateway route WITHOUT v1
/api/v1/users       ← With version
/api/v1/procurements ← With version
```

**Fix:** Standardize all endpoints with `/api/v1/` prefix

**Priority:** 🟡 MEDIUM

---

### 10. Docker Compose Not Tested

**File:** `backend/infra/docker-compose.yml`

```yaml
# ALL APPLICATION SERVICES ARE COMMENTED OUT!
#   auth-service:
#     build: ...
#   user-service:
#     build: ...
#   procurement-service:
#     build: ...
```

**Problem:** Docker deployment never tested

**Priority:** 🟡 MEDIUM

---

### 11. No Circuit Breaker for Feign Clients

Gateway has Resilience4j config, but inter-service Feign calls have no fallback mechanism.

**Priority:** 🟡 MEDIUM

---

### 12. Hardcoded Credentials

**Files:** Various `application.properties`

```properties
# Auth Service
jwt.secret-key=your-256-bit-secret-key-here  # PLACEHOLDER!

# All services
spring.datasource.password=postgres  # DEFAULT PASSWORD!
```

**Priority:** 🟡 MEDIUM

---

### 13. Missing Pagination

Not all repository queries use `Pageable`. Will cause issues with large datasets.

**Priority:** 🟡 MEDIUM

---

## 🟢 GOOD THINGS (Yang Sudah Bagus)

| Feature | Status | Notes |
|---------|--------|-------|
| Soft Delete Pattern | ✅ | Konsisten di semua entities |
| Audit Trail | ✅ | Kafka event publishing |
| Token Rotation | ✅ | Refresh token di-revoke saat refresh |
| Password Reset Flow | ✅ | Lengkap dengan expiry |
| Swagger/OpenAPI | ✅ | Di semua services |
| Event-Driven Architecture | ✅ | Kafka topics terstruktur |
| Partial Indexes | ✅ | Bagus untuk query performance |
| Separation of Concerns | ✅ | User vs Auth service terpisah |
| GlobalExceptionHandler | ✅ | Ada di mayoritas services |
| MapStruct Integration | ✅ | DTO mapping proper |
| Flyway Migrations | ✅ | Version-controlled schema |

---

## 🎯 PRIORITAS PERBAIKAN

### Immediate (Week 1)
1. ❗ Fix Spring Boot version ke 3.4.x
2. ❗ Fix duplicate Flyway migrations
3. ❗ Test build semua services

### Short-term (Week 2-3)
4. Ganti semua `RuntimeException` dengan custom exceptions
5. Fix package naming inconsistency
6. Add `@Transactional` yang missing
7. Fix logout audit username bug

### Medium-term (Week 4-6)
8. Add unit tests untuk services tanpa tests
9. Enable dan test Docker deployment
10. Standarisasi API versioning

### Long-term
11. Add circuit breakers ke Feign clients
12. Implement proper secrets management
13. Add integration tests dengan Testcontainers
14. Performance testing

---

## Conclusion

**Untuk Tugas Akhir:** Project ini sudah **cukup impressive** menunjukkan pemahaman microservices architecture yang baik.

**Untuk Production:** Masih perlu **banyak polish** terutama di area testing, error handling, dan deployment readiness.

**Key Takeaway:** Foundation bagus, tapi eksekusi masih "setengah-setengah" - ada fitur tapi tidak complete, ada pattern tapi tidak konsisten.

---

## Version History

| Version | Date | Reviewer | Focus |
|---------|------|----------|-------|
| v1 | 2026-01-02 | AI Reviewer | Initial comprehensive review |

---

*This is an automated code review. Human review is recommended for critical business logic.*

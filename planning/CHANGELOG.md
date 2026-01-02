# Changelog

All notable changes to the e-Procurement system will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [Unreleased]

### Planned
- Payment service integration
- Reporting dashboard
- Document service file storage
- Workflow engine enhancements
- Mobile client API support

---

## [0.1.0] - 2026-01-02

### Added

#### Core Infrastructure
- Docker Compose setup for development environment
- PostgreSQL 16 database configuration
- Apache Kafka 7.7.1 for event streaming
- Redis 7.4 for caching and session management
- PgAdmin and Redis Insight admin UIs

#### Microservices
- **Auth Service** (Port 8081)
  - JWT token generation and validation
  - Login, logout, and token refresh endpoints
  - Password reset functionality
  - Role management
  - Redis-based session and token blacklist

- **User Service** (Port 8082)
  - Internal user (employee) management
  - External user (client) management
  - Vendor user management
  - Soft delete pattern implementation
  - MapStruct DTO mapping

- **Vendor Service** (Port 8085)
  - Vendor registration and onboarding
  - Vendor verification workflow
  - Vendor scheduling
  - Kafka event publishing

- **Procurement Service** (Port 8084)
  - Purchase Requisition (PR) lifecycle
  - PR approval workflow (submit, approve, reject, return)
  - Purchase Order (PO) creation
  - Delivery tracking
  - Dashboard APIs for Operator and Supervisor
  - Kafka event publishing for all PR state changes

- **Master Data Service** (Port 8083)
  - Catalog management
  - Location management
  - Cost center management
  - General configuration

- **Inventory Service** (Port 8093)
  - Warehouse management
  - Stock level tracking
  - Stock transactions (in/out)

- **Admin Service** (Port 8087)
  - Cross-service administration
  - Employee management
  - Calendar management
  - Centralized audit viewing

- **Gateway Service** (Port 8080)
  - API routing to all services
  - Rate limiting with Redis
  - CORS configuration
  - Circuit breaker with Resilience4j

- **Notification Service** (Port 8088)
  - Kafka event consumption
  - Notification storage and retrieval

- **Audit Service** (Port 8090)
  - Audit log capture
  - Kafka event consumption

#### Security
- OAuth2 Resource Server configuration
- JWT-based authentication
- Role-based access control (ADMIN, SUPERVISOR, OPERATOR, FINANCE, VENDOR)
- Global exception handler with standardized error responses

#### Database
- Flyway migrations for all services
- Soft delete pattern across all entities
- Audit columns (created_at, updated_at, created_by, updated_by)
- Partial indexes for soft-deleted records

#### Documentation
- OpenAPI/Swagger documentation for all services
- Comprehensive project documentation

---

## Version History Format

### Types of Changes

- **Added** - New features
- **Changed** - Changes to existing functionality
- **Deprecated** - Features to be removed in future
- **Removed** - Removed features
- **Fixed** - Bug fixes
- **Security** - Security-related changes

### Versioning

This project uses [Semantic Versioning](https://semver.org/):

- **MAJOR** version for incompatible API changes
- **MINOR** version for backwards-compatible functionality additions
- **PATCH** version for backwards-compatible bug fixes

---

## Migration Guide

### From 0.0.x to 0.1.0

This is the initial release. No migration required.

### Future Migrations

Migration guides will be provided here for breaking changes between versions.

---

## Release Checklist

Before releasing a new version:

- [ ] All tests passing
- [ ] Documentation updated
- [ ] CHANGELOG.md updated
- [ ] Version numbers updated in pom.xml files
- [ ] Docker images built and tagged
- [ ] Database migrations tested
- [ ] API compatibility verified
- [ ] Security scan completed

---

## Contributing to Changelog

When making changes:

1. Add entries under `[Unreleased]` section
2. Use the appropriate category (Added, Changed, Fixed, etc.)
3. Include brief description and PR/issue reference if applicable
4. Move entries to release version when releasing

Example:
```markdown
### Added
- User export to CSV feature (#123)

### Fixed
- PR approval notification not sent (#456)
```

---

## Links

- [Documentation Index](./README.md)
- [Architecture](./ARCHITECTURE.md)
- [API Reference](./API_REFERENCE.md)
- [Getting Started](./GETTING_STARTED.md)

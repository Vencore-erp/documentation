# Glossary

## Overview

This glossary provides definitions for terms, acronyms, and concepts used throughout the e-Procurement system documentation.

---

## Business Terms

### Procurement Terms

| Term | Definition |
|------|------------|
| **PR** | Purchase Requisition - An internal document requesting the purchase of goods or services |
| **PO** | Purchase Order - An official document sent to a vendor to order goods or services |
| **RFQ** | Request for Quotation - A document requesting vendors to submit price quotes |
| **RFP** | Request for Proposal - A document requesting detailed proposals from vendors |
| **GRN** | Goods Receipt Note - Document confirming receipt of goods from a vendor |
| **SLA** | Service Level Agreement - Contract defining expected service levels |

### Procurement Workflow

| Term | Definition |
|------|------------|
| **Draft** | Initial state of a PR before submission |
| **Submitted** | PR has been submitted for approval |
| **Approved** | PR has been approved by a supervisor |
| **Rejected** | PR has been rejected and requires revision |
| **Returned** | PR has been returned to operator for changes |
| **Escalated** | PR has been escalated due to delayed approval |
| **Cancelled** | PR has been cancelled and is no longer active |

### Roles

| Role | Definition |
|------|------------|
| **Operator** | User who creates and manages Purchase Requisitions |
| **Supervisor** | User who approves or rejects Purchase Requisitions |
| **Finance** | User who handles payment processing and financial operations |
| **Admin** | System administrator with full access to all functions |
| **Vendor** | External supplier providing goods or services |

### Priority Levels

| Priority | Definition |
|----------|------------|
| **Normal** | Standard processing timeline |
| **Important** | Elevated priority, faster processing expected |
| **Urgent** | Immediate attention required, expedited processing |

---

## Technical Terms

### Architecture

| Term | Definition |
|------|------------|
| **Microservices** | Architectural style where an application is composed of small, independent services |
| **API Gateway** | Entry point that routes client requests to appropriate microservices |
| **Event-Driven Architecture** | Design pattern where services communicate through events |
| **Message Queue** | System for asynchronous message passing between services |
| **Service Discovery** | Mechanism for services to find and communicate with each other |
| **Circuit Breaker** | Pattern that prevents cascading failures in distributed systems |

### Security

| Term | Definition |
|------|------------|
| **JWT** | JSON Web Token - A compact, URL-safe means of representing claims between parties |
| **OAuth2** | Authorization framework enabling third-party access to resources |
| **RBAC** | Role-Based Access Control - Permissions based on user roles |
| **Access Token** | Short-lived token for accessing protected resources |
| **Refresh Token** | Long-lived token for obtaining new access tokens |
| **Token Blacklist** | List of revoked tokens stored in Redis |

### Database

| Term | Definition |
|------|------------|
| **Soft Delete** | Marking records as deleted without physically removing them |
| **Migration** | Version-controlled database schema changes |
| **UUID** | Universally Unique Identifier - 128-bit identifier |
| **JSONB** | Binary JSON format for efficient storage in PostgreSQL |
| **Index** | Database structure that improves query performance |
| **Partial Index** | Index with a filter condition (e.g., `WHERE is_deleted = false`) |

### Messaging

| Term | Definition |
|------|------------|
| **Kafka** | Distributed event streaming platform |
| **Topic** | Named channel for publishing and subscribing to messages |
| **Producer** | Service that publishes messages to a topic |
| **Consumer** | Service that subscribes to and processes messages from a topic |
| **Consumer Group** | Group of consumers that share message processing |
| **Offset** | Position of a message within a topic partition |
| **Partition** | Subdivision of a topic for parallel processing |

### Caching

| Term | Definition |
|------|------------|
| **Redis** | In-memory data structure store used for caching |
| **TTL** | Time To Live - Duration before cached data expires |
| **Cache Invalidation** | Process of removing stale data from cache |
| **Session Store** | Database for user session data |

---

## Framework & Tools

### Spring Ecosystem

| Term | Definition |
|------|------------|
| **Spring Boot** | Framework for building production-ready Spring applications |
| **Spring Data JPA** | Abstraction layer for database access using JPA |
| **Spring Security** | Security framework for authentication and authorization |
| **Spring Cloud Gateway** | API Gateway built on Spring WebFlux |
| **Spring Kafka** | Spring integration for Apache Kafka |

### Libraries

| Term | Definition |
|------|------------|
| **Lombok** | Library that reduces boilerplate code with annotations |
| **MapStruct** | Code generator for bean mapping (DTO ↔ Entity) |
| **Flyway** | Database migration tool |
| **Testcontainers** | Library for running Docker containers in tests |
| **Springdoc** | Library for generating OpenAPI documentation |

### DevOps

| Term | Definition |
|------|------------|
| **Docker** | Platform for containerizing applications |
| **Docker Compose** | Tool for defining multi-container applications |
| **Kubernetes (K8s)** | Container orchestration platform |
| **CI/CD** | Continuous Integration / Continuous Deployment |
| **Actuator** | Spring Boot module for monitoring and management |

---

## API Concepts

### REST

| Term | Definition |
|------|------------|
| **REST** | Representational State Transfer - Architectural style for APIs |
| **Endpoint** | URL path for accessing a resource |
| **Resource** | Entity exposed through the API (e.g., User, PR) |
| **HTTP Method** | Action to perform (GET, POST, PUT, DELETE, etc.) |
| **Status Code** | Numeric code indicating request outcome (200, 404, etc.) |

### Request/Response

| Term | Definition |
|------|------------|
| **DTO** | Data Transfer Object - Object for transferring data between layers |
| **Payload** | Data sent in request body or received in response |
| **Pagination** | Dividing large result sets into pages |
| **Query Parameter** | Parameter passed in URL (`?status=ACTIVE`) |
| **Path Variable** | Variable embedded in URL path (`/users/{id}`) |

### Documentation

| Term | Definition |
|------|------------|
| **OpenAPI** | Specification for describing REST APIs (formerly Swagger) |
| **Swagger UI** | Interactive API documentation and testing interface |
| **Schema** | Definition of data structure |

---

## Testing

| Term | Definition |
|------|------------|
| **Unit Test** | Test for individual components in isolation |
| **Integration Test** | Test for component interactions |
| **E2E Test** | End-to-end test simulating user behavior |
| **Mock** | Simulated object replacing real dependencies |
| **Test Fixture** | Setup data for tests |
| **Code Coverage** | Percentage of code executed during tests |
| **TDD** | Test-Driven Development - Writing tests before code |

---

## Kafka Topics

| Topic | Description |
|-------|-------------|
| `procurement.pr.submitted` | Published when a PR is submitted |
| `procurement.pr.approved` | Published when a PR is approved |
| `procurement.pr.rejected` | Published when a PR is rejected |
| `procurement.pr.returned` | Published when a PR is returned |
| `procurement.pr.reminder` | Published for approval reminders |
| `procurement.pr.escalated` | Published when a PR is escalated |
| `procurement.po.created` | Published when a PO is created |
| `procurement.inventory.stock-alert` | Published for low stock alerts |

---

## HTTP Status Codes

| Code | Name | Usage |
|------|------|-------|
| 200 | OK | Successful GET, PUT |
| 201 | Created | Successful POST creating resource |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Invalid request syntax or validation error |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Valid auth but insufficient permissions |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Resource state conflict (duplicate) |
| 422 | Unprocessable Entity | Business rule violation |
| 500 | Internal Server Error | Unexpected server error |

---

## Acronyms

| Acronym | Full Form |
|---------|-----------|
| API | Application Programming Interface |
| CORS | Cross-Origin Resource Sharing |
| CRUD | Create, Read, Update, Delete |
| DTO | Data Transfer Object |
| FQDN | Fully Qualified Domain Name |
| HTTP | Hypertext Transfer Protocol |
| HTTPS | HTTP Secure |
| JPA | Java Persistence API |
| JSON | JavaScript Object Notation |
| JVM | Java Virtual Machine |
| ORM | Object-Relational Mapping |
| REST | Representational State Transfer |
| SQL | Structured Query Language |
| TLS | Transport Layer Security |
| URI | Uniform Resource Identifier |
| URL | Uniform Resource Locator |

---

## Next Steps

- [Architecture](./ARCHITECTURE.md) - System architecture overview
- [Services](./SERVICES.md) - Service descriptions
- [API Reference](./API_REFERENCE.md) - API endpoints

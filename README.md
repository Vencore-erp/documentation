# e-Procurement System Documentation

<p align="center">
  <strong>Enterprise e-Procurement Microservices Platform</strong><br>
  <em>PT XYZ - Tugas Akhir Project</em>
</p>

---

## 📋 Overview

This project is a comprehensive **e-Procurement System** built using a **microservices architecture**. The system is designed to manage the entire procurement lifecycle, from purchase requisition to payment processing, with support for vendor management, inventory tracking, and workflow automation.

## 🏗️ Architecture Summary

| Component | Technology |
|-----------|------------|
| **Backend Framework** | Spring Boot 4.0.x |
| **Language** | Java 21 |
| **Database** | PostgreSQL 16 |
| **Message Queue** | Apache Kafka 7.7 |
| **Cache** | Redis 7.4 |
| **API Gateway** | Spring Cloud Gateway |
| **Security** | OAuth2 + JWT |
| **Documentation** | OpenAPI 3 (Springdoc) |
| **Container** | Docker + Docker Compose |
| **Migration** | Flyway |

## 📚 Documentation Index

### Core Documentation
| Document | Description |
|----------|-------------|
| [Architecture Overview](./ARCHITECTURE.md) | System architecture, components, and design patterns |
| [API Reference](./API_REFERENCE.md) | Complete API endpoints documentation |
| [Database Schema](./DATABASE_SCHEMA.md) | Entity relationships and table structures |
| [Services Overview](./SERVICES.md) | Detailed description of each microservice |

### Operational Guides
| Document | Description |
|----------|-------------|
| [Getting Started](./GETTING_STARTED.md) | Quick start guide for developers |
| [Deployment Guide](./DEPLOYMENT.md) | Production deployment instructions |
| [Configuration Guide](./CONFIGURATION.md) | Environment variables and settings |

### Development Guides
| Document | Description |
|----------|-------------|
| [Development Guide](./DEVELOPMENT.md) | Development workflow and coding standards |
| [Testing Guide](./TESTING.md) | Unit and integration testing strategies |
| [Security Guide](./SECURITY.md) | Authentication, authorization, and security practices |

### Reference
| Document | Description |
|----------|-------------|
| [Troubleshooting](./TROUBLESHOOTING.md) | Common issues and solutions |
| [Glossary](./GLOSSARY.md) | Terms and definitions |
| [Changelog](./CHANGELOG.md) | Version history and changes |
| [Brutal Review v1](./BRUTAL_REVIEW_V1.md) | 🔥 Honest code review with findings |
| [A+ Roadmap](./A_PLUS_ROADMAP.md) | 🏆 10-week plan to achieve A+ grade |

## 🏢 System Components

### Core Business Services
```
┌─────────────────────────────────────────────────────────────────────────┐
│                         e-Procurement System                            │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │    Auth     │  │    User     │  │   Vendor    │  │   Master    │    │
│  │   Service   │  │   Service   │  │   Service   │  │    Data     │    │
│  │   :8081     │  │   :8082     │  │   :8085     │  │   :8083     │    │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘    │
│                                                                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐    │
│  │ Procurement │  │  Inventory  │  │   Admin     │  │ Notification│    │
│  │   Service   │  │   Service   │  │   Service   │  │   Service   │    │
│  │   :8084     │  │   :8093     │  │   :8087     │  │   :8088     │    │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘    │
│                                                                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐                     │
│  │   Audit     │  │  Workflow   │  │  Reporting  │                     │
│  │   Service   │  │   Service   │  │   Service   │                     │
│  │   :8090     │  │   :8091     │  │   :8092     │                     │
│  └─────────────┘  └─────────────┘  └─────────────┘                     │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Infrastructure Components
- **API Gateway** (Port 8080) - Entry point for all client requests
- **PostgreSQL** (Port 5432) - Primary relational database
- **Kafka** (Port 9092) - Event streaming platform
- **Redis** (Port 6379) - Caching and session management
- **PgAdmin** (Port 5050) - Database administration UI
- **Redis Insight** (Port 8001) - Redis administration UI

## 🚀 Quick Start

```bash
# 1. Start infrastructure services
cd backend/infra
docker-compose up -d

# 2. Run individual services
cd ../services/auth-service/auth-service
./mvnw spring-boot:run

# 3. Access Swagger UI
# http://localhost:8081/swagger-ui.html
```

## 📊 Service Port Mapping

| Service | Port | Description |
|---------|------|-------------|
| Gateway Service | 8080 | API Gateway |
| Auth Service | 8081 | Authentication & Authorization |
| User Service | 8082 | User Management |
| Master Data Service | 8083 | Reference Data |
| Procurement Service | 8084 | Procurement Operations |
| Vendor Service | 8085 | Vendor Management |
| Payment Service | 8086 | Payment Processing |
| Admin Service | 8087 | Administration |
| Notification Service | 8088 | Notifications & Alerts |
| Document Service | 8089 | Document Management |
| Audit Service | 8090 | Audit Logging |
| Workflow Service | 8091 | Workflow Engine |
| Reporting Service | 8092 | Reports & Analytics |
| Inventory Service | 8093 | Inventory Management |

## 📝 Key Features

- ✅ **Multi-Role Support**: OPERATOR, SUPERVISOR, FINANCE, ADMIN
- ✅ **Workflow Automation**: Approval workflows with escalation
- ✅ **Event-Driven Architecture**: Kafka-based async communication
- ✅ **Soft Delete Pattern**: Data preservation with `is_deleted` flags
- ✅ **JWT Authentication**: Secure token-based authentication
- ✅ **API Documentation**: Swagger/OpenAPI 3 for all services
- ✅ **Audit Logging**: Comprehensive audit trail

## 📧 Contact

For questions or support regarding this documentation, please contact the development team.

---

<p align="center">
  <em>Last Updated: January 2026</em>
</p>

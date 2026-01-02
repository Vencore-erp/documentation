# Configuration Guide

## Overview

This guide covers all configuration options for the e-Procurement microservices. Configuration is managed via `application.properties` files within each service.

> **Note:** This project does not use Spring Cloud Config Server. All configurations are managed locally in each service's `application.properties` file.

---

## Configuration Hierarchy

Spring Boot loads configurations in the following order (later sources override earlier ones):

1. Default values in code
2. `application.properties` in classpath
3. Profile-specific properties (`application-{profile}.properties`)
4. Environment variables
5. Command-line arguments

---

## Common Configuration

These settings are common across all services:

### JPA & Database

```properties
# Database Connection
spring.datasource.url=jdbc:postgresql://localhost:5432/masterdb
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA Configuration
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.open-in-view=false
```

| Property | Description | Default |
|----------|-------------|---------|
| `spring.jpa.hibernate.ddl-auto` | Schema generation | `validate` |
| `spring.jpa.show-sql` | Log SQL queries | `true` |
| `spring.jpa.open-in-view` | OSIV pattern | `false` |

### Flyway Migration

```properties
spring.flyway.enabled=true
spring.flyway.locations=classpath:db/migration
spring.flyway.baseline-on-migrate=true
```

### Kafka

```properties
spring.kafka.bootstrap-servers=localhost:9092
spring.kafka.consumer.group-id=<service-name>-group
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.springframework.kafka.support.serializer.JsonDeserializer
spring.kafka.consumer.properties.spring.json.trusted.packages=*

spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.springframework.kafka.support.serializer.JsonSerializer
```

### Security (OAuth2 Resource Server)

```properties
spring.security.oauth2.resourceserver.jwt.issuer-uri=http://localhost:8081
```

### Swagger/OpenAPI

```properties
springdoc.api-docs.path=/v3/api-docs
springdoc.swagger-ui.path=/swagger-ui.html
```

### Actuator

```properties
management.endpoints.web.exposure.include=health,info,metrics
management.endpoint.health.show-details=always
```

### Logging

```properties
logging.level.root=INFO
logging.level.com.tugas_akhir.<service>=DEBUG
```

---

## Service-Specific Configurations

### Auth Service (Port 8081)

```properties
spring.application.name=auth-service
server.port=8081

# Redis (session management)
spring.data.redis.host=localhost
spring.data.redis.port=6379

# JWT Configuration
jwt.secret-key=your-256-bit-secret-key-here
jwt.access-token-expiration=3600000
jwt.refresh-token-expiration=604800000

# Kafka Topics
spring.kafka.consumer.group-id=auth-group
```

### User Service (Port 8082)

```properties
spring.application.name=user-service
server.port=8082

# Kafka
spring.kafka.consumer.group-id=user-group

# Security
spring.security.oauth2.resourceserver.jwt.issuer-uri=http://localhost:8081
```

### Gateway Service (Port 8080)

```properties
spring.application.name=gateway-service
server.port=8080

# Redis (Rate Limiting)
spring.data.redis.host=localhost
spring.data.redis.port=6379
spring.data.redis.timeout=2000ms

# CORS
spring.cloud.gateway.globalcors.cors-configurations.[/**].allowed-origins=*
spring.cloud.gateway.globalcors.cors-configurations.[/**].allowed-methods=GET,POST,PUT,DELETE,OPTIONS
spring.cloud.gateway.globalcors.cors-configurations.[/**].allowed-headers=*
spring.cloud.gateway.globalcors.cors-configurations.[/**].allow-credentials=false

# Rate Limiting
spring.cloud.gateway.filter.request-rate-limiter.redis-rate-limiter.replenish-rate=100
spring.cloud.gateway.filter.request-rate-limiter.redis-rate-limiter.burst-capacity=200

# Circuit Breaker
resilience4j.circuitbreaker.configs.default.sliding-window-size=10
resilience4j.circuitbreaker.configs.default.minimum-number-of-calls=5
resilience4j.circuitbreaker.configs.default.failure-rate-threshold=50
resilience4j.circuitbreaker.configs.default.wait-duration-in-open-state=10000

# Service Routes
spring.cloud.gateway.routes[0].id=auth-service
spring.cloud.gateway.routes[0].uri=http://localhost:8081
spring.cloud.gateway.routes[0].predicates[0]=Path=/api/auth/**
spring.cloud.gateway.routes[0].filters[0]=StripPrefix=1

spring.cloud.gateway.routes[1].id=user-service
spring.cloud.gateway.routes[1].uri=http://localhost:8082
spring.cloud.gateway.routes[1].predicates[0]=Path=/api/v1/users/**
spring.cloud.gateway.routes[1].filters[0]=StripPrefix=1

spring.cloud.gateway.routes[2].id=vendor-service
spring.cloud.gateway.routes[2].uri=http://localhost:8085
spring.cloud.gateway.routes[2].predicates[0]=Path=/api/v1/vendors/**
spring.cloud.gateway.routes[2].filters[0]=StripPrefix=1

spring.cloud.gateway.routes[3].id=procurement-service
spring.cloud.gateway.routes[3].uri=http://localhost:8084
spring.cloud.gateway.routes[3].predicates[0]=Path=/api/v1/procurements/**
spring.cloud.gateway.routes[3].filters[0]=StripPrefix=1

spring.cloud.gateway.routes[4].id=admin-service
spring.cloud.gateway.routes[4].uri=http://localhost:8087
spring.cloud.gateway.routes[4].predicates[0]=Path=/api/v1/admin/**
spring.cloud.gateway.routes[4].filters[0]=StripPrefix=1
```

### Procurement Service (Port 8084)

```properties
spring.application.name=procurement-service
server.port=8084

# Redis
spring.data.redis.host=localhost
spring.data.redis.port=6379

# Kafka Topics
spring.kafka.topic.pr-submitted=procurement.pr.submitted
spring.kafka.topic.pr-approved=procurement.pr.approved
spring.kafka.topic.pr-rejected=procurement.pr.rejected
spring.kafka.topic.pr-returned=procurement.pr.returned
spring.kafka.topic.pr-reminder=procurement.pr.reminder
spring.kafka.topic.pr-escalated=procurement.pr.escalated
spring.kafka.topic.po-created=procurement.po.created
spring.kafka.topic.stock-alert=procurement.inventory.stock-alert

# Feign Clients
integration.vendor-service.url=http://vendor_service:8080
integration.notification-service.url=http://notification_service:8080
integration.audit-service.url=http://audit_service:8080
feign.client.config.default.connectTimeout=5000
feign.client.config.default.readTimeout=5000

# Scheduled Tasks
scheduling.auto-reminder.cron=0 0 * * * *
scheduling.auto-escalation.cron=0 0 * * * *
```

### Admin Service (Port 8087)

```properties
spring.application.name=admin-service
server.port=8087

spring.kafka.consumer.group-id=admin-group
```

### Inventory Service (Port 8093)

```properties
spring.application.name=inventory-service
server.port=8093

spring.kafka.consumer.group-id=inventory-group
```

---

## Environment-Specific Configuration

### Development Profile

Create `application-dev.properties`:

```properties
# Development database
spring.datasource.url=jdbc:postgresql://localhost:5432/masterdb_dev

# Show SQL in development
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# Debug logging
logging.level.com.tugas_akhir=DEBUG
logging.level.org.springframework.web=DEBUG

# Disable Kafka in development (optional)
# spring.kafka.bootstrap-servers=

# Disable Flyway for testing
# spring.flyway.enabled=false
```

### Production Profile

Create `application-prod.properties`:

```properties
# Production database
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

# Disable SQL logging
spring.jpa.show-sql=false

# Production logging
logging.level.root=WARN
logging.level.com.tugas_akhir=INFO

# Enable compression
server.compression.enabled=true
server.compression.mime-types=application/json,application/xml,text/html,text/xml,text/plain

# Connection pooling (HikariCP)
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
```

### Activate Profile

```bash
# Via environment variable
export SPRING_PROFILES_ACTIVE=prod

# Via command line
./mvnw spring-boot:run -Dspring-boot.run.profiles=prod

# Via Docker
docker run -e SPRING_PROFILES_ACTIVE=prod myimage
```

---

## Environment Variables

All configuration properties can be overridden using environment variables:

### Naming Convention

Spring Boot converts property names to environment variables:
- Dots (`.`) → Underscores (`_`)
- Dashes (`-`) → Underscores (`_`)
- Uppercase letters

**Examples:**
| Property | Environment Variable |
|----------|---------------------|
| `spring.datasource.url` | `SPRING_DATASOURCE_URL` |
| `spring.kafka.bootstrap-servers` | `SPRING_KAFKA_BOOTSTRAP_SERVERS` |
| `jwt.secret-key` | `JWT_SECRET_KEY` |

### Docker Environment

```yaml
# docker-compose.yml
services:
  auth-service:
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/masterdb
      SPRING_DATASOURCE_USERNAME: postgres
      SPRING_DATASOURCE_PASSWORD: ${DB_PASSWORD}
      SPRING_REDIS_HOST: redis
      JWT_SECRET_KEY: ${JWT_SECRET}
```

---

## Configuration Best Practices

### 1. Use Environment Variables for Secrets

Never commit secrets to version control:

```properties
# ❌ Bad
spring.datasource.password=mypassword

# ✅ Good
spring.datasource.password=${DB_PASSWORD}
```

### 2. Externalize Configuration

For production, externalize all environment-specific configs:

```bash
# External config file
java -jar app.jar --spring.config.location=/etc/app/application.properties
```

### 3. Use Profiles Appropriately

```properties
# application.properties (defaults)
logging.level.root=INFO

# application-dev.properties
logging.level.root=DEBUG

# application-prod.properties
logging.level.root=WARN
```

### 4. Secure Actuator Endpoints

```properties
# Only expose necessary endpoints in production
management.endpoints.web.exposure.include=health,info
management.endpoint.health.show-details=never
```

### 5. Configure Connection Pools

```properties
# Prevent connection exhaustion
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.connection-timeout=30000
```

---

## Troubleshooting Configuration

### Check Active Configuration

```bash
# View all configuration properties
GET http://localhost:8081/actuator/configprops

# View environment
GET http://localhost:8081/actuator/env
```

### Common Issues

**Database connection fails:**
- Check `spring.datasource.url` format
- Verify PostgreSQL is running
- Check network connectivity

**Kafka connection fails:**
- Verify `spring.kafka.bootstrap-servers`
- Check Kafka container is healthy
- Review consumer group settings

**JWT validation fails:**
- Match `jwt.issuer-uri` across services
- Verify Auth Service is accessible
- Check JWT secret consistency

---

## Next Steps

- [Security Guide](./SECURITY.md) - Security configuration
- [Deployment Guide](./DEPLOYMENT.md) - Production deployment
- [Getting Started](./GETTING_STARTED.md) - Development setup

# Deployment Guide

## Overview

This guide covers deploying the e-Procurement system to various environments.

## Deployment Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          Production Environment                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌───────────────┐    ┌───────────────────────────────────────────┐   │
│  │  Load Balancer│    │           Docker Host / Kubernetes        │   │
│  │   (Nginx)     │───▶│                                           │   │
│  └───────────────┘    │  ┌─────────┐  ┌─────────┐  ┌─────────┐   │   │
│                       │  │ Gateway │  │  Auth   │  │  User   │   │   │
│                       │  │ :8080   │  │ :8081   │  │ :8082   │   │   │
│                       │  └─────────┘  └─────────┘  └─────────┘   │   │
│                       │                                           │   │
│                       │  ┌─────────┐  ┌─────────┐  ┌─────────┐   │   │
│                       │  │Procure  │  │ Vendor  │  │ Master  │   │   │
│                       │  │ :8084   │  │ :8085   │  │ :8083   │   │   │
│                       │  └─────────┘  └─────────┘  └─────────┘   │   │
│                       └───────────────────────────────────────────┘   │
│                                         │                              │
│  ┌─────────────────────────────────────┼───────────────────────────┐  │
│  │                    Data Layer        ▼                           │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │  │
│  │  │ PostgreSQL  │  │    Redis    │  │    Kafka    │              │  │
│  │  │   Cluster   │  │   Cluster   │  │   Cluster   │              │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘              │  │
│  └──────────────────────────────────────────────────────────────────┘  │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Docker Deployment

### Building Docker Images

Each service has a Dockerfile in its root directory.

**Example Service Dockerfile:**
```dockerfile
FROM eclipse-temurin:21-jdk-alpine
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Build and Push Images

```bash
# Build the service JAR
cd backend/services/auth-service/auth-service
./mvnw clean package -DskipTests

# Build Docker image
docker build -t eprocurement/auth-service:latest .

# Push to registry
docker push eprocurement/auth-service:latest
```

### Build Script for All Services

```bash
#!/bin/bash
# build-all.sh

SERVICES=(
  "auth-service"
  "user-service"
  "vendor-service"
  "procurement-service"
  "masterdata-service"
  "inventory-service"
  "admin-service"
  "gateway-service"
  "notification-service"
  "audit-service"
)

for SERVICE in "${SERVICES[@]}"; do
  echo "Building $SERVICE..."
  cd backend/services/$SERVICE/*-service
  ./mvnw clean package -DskipTests
  docker build -t eprocurement/$SERVICE:latest .
  cd -
done

echo "All services built successfully!"
```

---

## Docker Compose Production

### docker-compose.prod.yml

```yaml
version: "3.9"

services:
  # ======================
  # INFRASTRUCTURE
  # ======================
  postgres:
    image: postgres:16-alpine
    container_name: postgres_main
    restart: always
    environment:
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: ${DB_NAME}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - eprocurement_net
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7.4-alpine
    container_name: redis_main
    restart: always
    command: ["redis-server", "--requirepass", "${REDIS_PASSWORD}"]
    volumes:
      - redis_data:/data
    networks:
      - eprocurement_net

  kafka:
    image: confluentinc/cp-kafka:7.7.1
    container_name: kafka_main
    restart: always
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_PROCESS_ROLES: "broker,controller"
      KAFKA_LISTENERS: PLAINTEXT://kafka:29092,CONTROLLER://kafka:29093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092
      KAFKA_CONTROLLER_QUORUM_VOTERS: "1@kafka:29093"
      CLUSTER_ID: "MkU3OEVBNTcwNTJENDM2Qk"
    volumes:
      - kafka_data:/var/lib/kafka/data
    networks:
      - eprocurement_net

  # ======================
  # APPLICATION SERVICES
  # ======================
  auth-service:
    image: eprocurement/auth-service:latest
    container_name: auth_service
    restart: always
    ports:
      - "8081:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/${DB_NAME}
      SPRING_DATASOURCE_USERNAME: ${DB_USER}
      SPRING_DATASOURCE_PASSWORD: ${DB_PASSWORD}
      SPRING_REDIS_HOST: redis
      SPRING_REDIS_PASSWORD: ${REDIS_PASSWORD}
      SPRING_KAFKA_BOOTSTRAP_SERVERS: kafka:29092
      JWT_SECRET: ${JWT_SECRET}
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_started
      kafka:
        condition: service_started
    networks:
      - eprocurement_net

  user-service:
    image: eprocurement/user-service:latest
    container_name: user_service
    restart: always
    ports:
      - "8082:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/${DB_NAME}
      SPRING_DATASOURCE_USERNAME: ${DB_USER}
      SPRING_DATASOURCE_PASSWORD: ${DB_PASSWORD}
      SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI: http://auth-service:8080
    depends_on:
      - postgres
      - auth-service
    networks:
      - eprocurement_net

  procurement-service:
    image: eprocurement/procurement-service:latest
    container_name: procurement_service
    restart: always
    ports:
      - "8084:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/${DB_NAME}
      SPRING_DATASOURCE_USERNAME: ${DB_USER}
      SPRING_DATASOURCE_PASSWORD: ${DB_PASSWORD}
      SPRING_REDIS_HOST: redis
      SPRING_REDIS_PASSWORD: ${REDIS_PASSWORD}
      SPRING_KAFKA_BOOTSTRAP_SERVERS: kafka:29092
      SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI: http://auth-service:8080
    depends_on:
      - postgres
      - redis
      - kafka
      - auth-service
    networks:
      - eprocurement_net

  gateway-service:
    image: eprocurement/gateway-service:latest
    container_name: gateway_service
    restart: always
    ports:
      - "8080:8080"
    environment:
      SPRING_REDIS_HOST: redis
      SPRING_REDIS_PASSWORD: ${REDIS_PASSWORD}
      SPRING_SECURITY_OAUTH2_RESOURCESERVER_JWT_ISSUER_URI: http://auth-service:8080
      AUTH_SERVICE_URL: http://auth-service:8080
      USER_SERVICE_URL: http://user-service:8080
      PROCUREMENT_SERVICE_URL: http://procurement-service:8080
    depends_on:
      - auth-service
      - user-service
      - procurement-service
    networks:
      - eprocurement_net

networks:
  eprocurement_net:
    driver: bridge

volumes:
  postgres_data:
  redis_data:
  kafka_data:
```

### Environment File (.env)

```bash
# Database
DB_USER=eprocurement_user
DB_PASSWORD=secure_password_here
DB_NAME=eprocurement_db

# Redis
REDIS_PASSWORD=redis_secure_password

# JWT
JWT_SECRET=your_jwt_secret_key_here_minimum_32_characters

# Application
SPRING_PROFILES_ACTIVE=prod
```

### Deploy with Docker Compose

```bash
# Production deployment
docker compose -f docker-compose.prod.yml up -d

# View logs
docker compose -f docker-compose.prod.yml logs -f

# Scale a service
docker compose -f docker-compose.prod.yml up -d --scale procurement-service=3
```

---

## Kubernetes Deployment

### Namespace

```yaml
# namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: eprocurement
```

### ConfigMap

```yaml
# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: eprocurement-config
  namespace: eprocurement
data:
  SPRING_PROFILES_ACTIVE: "prod"
  SPRING_DATASOURCE_URL: "jdbc:postgresql://postgres-service:5432/eprocurement_db"
  SPRING_KAFKA_BOOTSTRAP_SERVERS: "kafka-service:9092"
  SPRING_REDIS_HOST: "redis-service"
```

### Secrets

```yaml
# secrets.yaml
apiVersion: v1
kind: Secret
metadata:
  name: eprocurement-secrets
  namespace: eprocurement
type: Opaque
stringData:
  DB_PASSWORD: secure_password_here
  REDIS_PASSWORD: redis_secure_password
  JWT_SECRET: your_jwt_secret_key_here
```

### Service Deployment

```yaml
# auth-service-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth-service
  namespace: eprocurement
spec:
  replicas: 2
  selector:
    matchLabels:
      app: auth-service
  template:
    metadata:
      labels:
        app: auth-service
    spec:
      containers:
      - name: auth-service
        image: eprocurement/auth-service:latest
        ports:
        - containerPort: 8080
        envFrom:
        - configMapRef:
            name: eprocurement-config
        env:
        - name: SPRING_DATASOURCE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: eprocurement-secrets
              key: DB_PASSWORD
        - name: JWT_SECRET
          valueFrom:
            secretKeyRef:
              name: eprocurement-secrets
              key: JWT_SECRET
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "1Gi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /actuator/health
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: auth-service
  namespace: eprocurement
spec:
  selector:
    app: auth-service
  ports:
  - port: 8080
    targetPort: 8080
  type: ClusterIP
```

### Ingress

```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: eprocurement-ingress
  namespace: eprocurement
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: api.eprocurement.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: gateway-service
            port:
              number: 8080
```

### Apply Kubernetes Manifests

```bash
# Apply all manifests
kubectl apply -f k8s/

# Check deployments
kubectl get pods -n eprocurement

# View logs
kubectl logs -f deployment/auth-service -n eprocurement
```

---

## Production Checklist

### Security
- [ ] Change all default passwords
- [ ] Use strong JWT secret (min 256 bits)
- [ ] Enable TLS/HTTPS
- [ ] Configure CORS properly
- [ ] Enable rate limiting
- [ ] Review security headers

### Database
- [ ] Use connection pooling
- [ ] Enable SSL for database connections
- [ ] Set up regular backups
- [ ] Configure database replication

### Monitoring
- [ ] Set up Prometheus/Grafana
- [ ] Configure alerting
- [ ] Enable distributed tracing
- [ ] Centralize logging (ELK Stack)

### Performance
- [ ] Configure JVM memory settings
- [ ] Enable response compression
- [ ] Set up CDN for static assets
- [ ] Configure connection pools

### High Availability
- [ ] Deploy multiple instances per service
- [ ] Set up load balancing
- [ ] Configure health checks
- [ ] Implement circuit breakers

---

## Health Monitoring

### Actuator Endpoints

Each service exposes health endpoints:
- `/actuator/health` - Overall health
- `/actuator/health/liveness` - Liveness probe
- `/actuator/health/readiness` - Readiness probe
- `/actuator/metrics` - Metrics data
- `/actuator/info` - Build info

### Prometheus Configuration

```yaml
# prometheus.yml
scrape_configs:
  - job_name: 'eprocurement'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets:
        - 'auth-service:8080'
        - 'user-service:8080'
        - 'procurement-service:8080'
```

---

## Rollback Procedures

### Docker Compose

```bash
# Rollback to previous version
docker compose -f docker-compose.prod.yml stop procurement-service
docker tag eprocurement/procurement-service:latest eprocurement/procurement-service:rollback
docker pull eprocurement/procurement-service:previous
docker tag eprocurement/procurement-service:previous eprocurement/procurement-service:latest
docker compose -f docker-compose.prod.yml up -d procurement-service
```

### Kubernetes

```bash
# View rollout history
kubectl rollout history deployment/procurement-service -n eprocurement

# Rollback to previous version
kubectl rollout undo deployment/procurement-service -n eprocurement

# Rollback to specific revision
kubectl rollout undo deployment/procurement-service -n eprocurement --to-revision=2
```

---

## Next Steps

- [Configuration Guide](./CONFIGURATION.md) - Environment settings
- [Security Guide](./SECURITY.md) - Security configuration
- [Testing Guide](./TESTING.md) - Testing strategies

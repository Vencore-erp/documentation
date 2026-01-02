# Troubleshooting Guide

## Overview

This guide provides solutions to common issues encountered when developing, deploying, or operating the e-Procurement system.

---

## Quick Diagnostic Commands

```powershell
# Check running Docker containers
docker ps

# Check service health
curl http://localhost:8081/actuator/health

# View service logs
docker logs -f auth_service

# Check database connection
docker exec postgres_main psql -U postgres -c "SELECT 1"

# Check Kafka topics
docker exec kafka_main kafka-topics --list --bootstrap-server localhost:9092
```

---

## Startup Issues

### Services Won't Start

#### Problem: Port Already in Use
```
Error: Web server failed to start. Port 8081 was already in use.
```

**Solution:**
```powershell
# Find process using the port
netstat -ano | findstr :8081

# Kill the process
taskkill /PID <process_id> /F

# Or change the port in application.properties
server.port=8091
```

---

#### Problem: Database Connection Failed
```
Error: Unable to acquire JDBC Connection
org.postgresql.util.PSQLException: Connection refused
```

**Solutions:**

1. **Verify PostgreSQL is running:**
   ```powershell
   docker ps | findstr postgres
   ```

2. **Check connection string:**
   ```properties
   # Correct format
   spring.datasource.url=jdbc:postgresql://localhost:5432/masterdb
   ```

3. **Test database connectivity:**
   ```powershell
   docker exec postgres_main psql -U postgres -c "\l"
   ```

4. **Restart PostgreSQL:**
   ```powershell
   docker compose restart postgres
   ```

---

#### Problem: Kafka Connection Failed
```
Error: Connection to node -1 could not be established
org.apache.kafka.common.errors.TimeoutException
```

**Solutions:**

1. **Verify Kafka is running:**
   ```powershell
   docker ps | findstr kafka
   ```

2. **Check bootstrap servers:**
   ```properties
   spring.kafka.bootstrap-servers=localhost:9092
   ```

3. **Restart Kafka:**
   ```powershell
   docker compose restart kafka
   ```

4. **If running in Docker, use internal hostname:**
   ```properties
   # Inside Docker network
   spring.kafka.bootstrap-servers=kafka:29092
   
   # From host machine
   spring.kafka.bootstrap-servers=localhost:9092
   ```

---

#### Problem: Redis Connection Failed
```
Error: Unable to connect to Redis
io.lettuce.core.RedisConnectionException
```

**Solutions:**

1. **Verify Redis is running:**
   ```powershell
   docker ps | findstr redis
   ```

2. **Test Redis connectivity:**
   ```powershell
   docker exec redis_main redis-cli ping
   # Should return: PONG
   ```

3. **Check Redis configuration:**
   ```properties
   spring.data.redis.host=localhost
   spring.data.redis.port=6379
   ```

---

### Flyway Migration Errors

#### Problem: Migration Checksum Mismatch
```
Error: Migration checksum mismatch for migration version 1
```

**Solutions:**

1. **Development only - Reset Flyway:**
   ```powershell
   ./mvnw flyway:clean flyway:migrate
   ```

2. **Repair Flyway history:**
   ```powershell
   ./mvnw flyway:repair
   ```

3. **Never modify applied migrations in production - create a new version instead**

---

#### Problem: Duplicate Migration Version
```
Error: Found more than one migration with version 1
```

**Solution:**
Check `src/main/resources/db/migration/` for duplicate files:
```
V1__init_schema.sql      ← Remove one
V1__init_admin_schema.sql ← Keep one, rename other to V2
```

---

## Authentication Issues

### Problem: JWT Token Invalid
```
Error: 401 Unauthorized
{
  "errorCode": "INVALID_TOKEN",
  "message": "JWT signature does not match"
}
```

**Solutions:**

1. **Verify JWT secret is consistent across services:**
   ```properties
   # All services must use the same issuer-uri
   spring.security.oauth2.resourceserver.jwt.issuer-uri=http://localhost:8081
   ```

2. **Check token expiration:**
   ```powershell
   # Decode token at jwt.io to check 'exp' claim
   ```

3. **Refresh the token:**
   ```http
   POST /api/v1/auth/refresh
   { "refreshToken": "..." }
   ```

---

### Problem: Access Denied (403 Forbidden)
```
Error: 403 Forbidden
{
  "errorCode": "ACCESS_DENIED",
  "message": "You don't have permission to perform this action"
}
```

**Solutions:**

1. **Verify user has required role:**
   ```sql
   SELECT u.username, r.name 
   FROM users u
   JOIN user_roles ur ON u.id = ur.user_id
   JOIN roles r ON ur.role_id = r.id
   WHERE u.username = 'your_username';
   ```

2. **Check endpoint security configuration**

3. **Assign required role:**
   ```sql
   INSERT INTO user_roles (user_id, role_id) 
   VALUES ('user-uuid', (SELECT id FROM roles WHERE name = 'SUPERVISOR'));
   ```

---

### Problem: Token Blacklisted After Logout
```
Error: 401 Unauthorized - Token has been revoked
```

**Solution:**
This is expected behavior after logout. Generate a new token by logging in again.

---

## API Errors

### Problem: 400 Bad Request - Validation Error
```json
{
  "errorCode": "VALIDATION_ERROR",
  "message": "Validation failed",
  "details": ["email: must be a valid email address"]
}
```

**Solution:**
Check your request body against the DTO validation rules. Refer to [API Reference](./API_REFERENCE.md).

---

### Problem: 404 Not Found
```json
{
  "errorCode": "RESOURCE_NOT_FOUND",
  "message": "PR not found with id: ..."
}
```

**Solutions:**

1. **Verify resource exists:**
   ```sql
   SELECT * FROM procurement_requests WHERE id = 'uuid';
   ```

2. **Check if soft-deleted:**
   ```sql
   SELECT * FROM procurement_requests 
   WHERE id = 'uuid' AND is_deleted = false;
   ```

---

### Problem: 409 Conflict
```json
{
  "errorCode": "DUPLICATE_RESOURCE",
  "message": "Email already exists"
}
```

**Solution:**
The resource you're trying to create already exists. Use a different unique identifier.

---

### Problem: 422 Unprocessable Entity
```json
{
  "errorCode": "BUSINESS_ERROR",
  "message": "Cannot approve PR that is not in SUBMITTED status"
}
```

**Solution:**
This indicates a business rule violation. Check the current state of the resource and the allowed operations.

---

## Database Issues

### Problem: Connection Pool Exhausted
```
Error: HikariPool-1 - Connection is not available, request timed out
```

**Solutions:**

1. **Increase pool size:**
   ```properties
   spring.datasource.hikari.maximum-pool-size=20
   spring.datasource.hikari.minimum-idle=5
   ```

2. **Check for connection leaks:**
   - Ensure all database operations use `@Transactional`
   - Avoid long-running transactions

3. **Monitor active connections:**
   ```sql
   SELECT count(*) FROM pg_stat_activity WHERE datname = 'masterdb';
   ```

---

### Problem: Deadlock Detected
```
Error: deadlock detected
```

**Solutions:**

1. **Review transaction isolation levels**
2. **Ensure consistent lock ordering**
3. **Keep transactions short**
4. **Implement retry logic:**
   ```java
   @Retryable(value = PessimisticLockingFailureException.class, maxAttempts = 3)
   public void updateWithRetry() { ... }
   ```

---

### Problem: Slow Queries
**Diagnosis:**
```sql
-- Find slow queries
SELECT pid, now() - pg_stat_activity.query_start AS duration, query
FROM pg_stat_activity
WHERE state != 'idle' AND now() - pg_stat_activity.query_start > interval '5 seconds';
```

**Solutions:**

1. **Add missing indexes:**
   ```sql
   CREATE INDEX idx_pr_status ON procurement_requests(status) 
   WHERE is_deleted = false;
   ```

2. **Analyze query plan:**
   ```sql
   EXPLAIN ANALYZE SELECT * FROM procurement_requests WHERE status = 'DRAFT';
   ```

3. **Update table statistics:**
   ```sql
   ANALYZE procurement_requests;
   ```

---

## Kafka Issues

### Problem: Consumer Lag
**Diagnosis:**
```powershell
docker exec kafka_main kafka-consumer-groups \
  --bootstrap-server localhost:9092 \
  --describe --group procurement-group
```

**Solutions:**

1. **Scale consumers:**
   - Increase consumer instances
   - Match partition count with consumers

2. **Optimize consumer processing:**
   - Batch processing
   - Async handling

---

### Problem: Messages Not Being Consumed
**Solutions:**

1. **Check consumer group:**
   ```properties
   spring.kafka.consumer.group-id=unique-group-id
   ```

2. **Reset consumer offset:**
   ```powershell
   docker exec kafka_main kafka-consumer-groups \
     --bootstrap-server localhost:9092 \
     --group procurement-group \
     --reset-offsets --to-earliest \
     --topic procurement.pr.submitted \
     --execute
   ```

3. **Verify topic exists:**
   ```powershell
   docker exec kafka_main kafka-topics \
     --list --bootstrap-server localhost:9092
   ```

---

## Docker Issues

### Problem: Container Keeps Restarting
**Diagnosis:**
```powershell
docker logs <container_name>
docker inspect <container_name> --format='{{.State.ExitCode}}'
```

**Solutions:**

1. **Check memory limits:**
   ```yaml
   deploy:
     resources:
       limits:
         memory: 512M
   ```

2. **View exit reason:**
   ```powershell
   docker inspect <container_name> --format='{{.State.Error}}'
   ```

---

### Problem: Volume Permission Issues
```
Error: Permission denied
```

**Solution (Linux/Mac):**
```bash
sudo chown -R 1000:1000 ./data
```

**Solution (Windows):**
Ensure Docker Desktop has access to the folder in Settings > Resources > File sharing.

---

### Problem: Network Issues Between Containers
**Solutions:**

1. **Verify network exists:**
   ```powershell
   docker network ls
   ```

2. **Use service names, not localhost:**
   ```properties
   # Inside Docker network
   spring.datasource.url=jdbc:postgresql://postgres:5432/masterdb
   ```

3. **Check container is on same network:**
   ```powershell
   docker network inspect micro_net
   ```

---

## Performance Issues

### Problem: High Memory Usage
**Solutions:**

1. **Configure JVM heap:**
   ```dockerfile
   ENTRYPOINT ["java", "-Xmx512m", "-Xms256m", "-jar", "app.jar"]
   ```

2. **Enable GC logging:**
   ```properties
   -XX:+PrintGCDetails -XX:+PrintGCDateStamps
   ```

3. **Analyze heap dump:**
   ```powershell
   jmap -dump:format=b,file=heap.bin <pid>
   ```

---

### Problem: Slow API Responses
**Solutions:**

1. **Enable response compression:**
   ```properties
   server.compression.enabled=true
   server.compression.mime-types=application/json
   ```

2. **Add caching:**
   ```java
   @Cacheable("users")
   public UserResponse findById(UUID id) { ... }
   ```

3. **Optimize database queries:**
   - Use pagination
   - Add appropriate indexes
   - Avoid N+1 queries

---

## Getting Help

If you've tried the solutions above and still have issues:

1. **Check service logs:**
   ```powershell
   docker logs -f <service_name> --tail 100
   ```

2. **Check actuator endpoints:**
   ```
   GET /actuator/health
   GET /actuator/info
   GET /actuator/metrics
   ```

3. **Enable debug logging:**
   ```properties
   logging.level.com.tugas_akhir=DEBUG
   logging.level.org.springframework=DEBUG
   ```

4. **Consult documentation:**
   - [Getting Started](./GETTING_STARTED.md)
   - [Configuration Guide](./CONFIGURATION.md)
   - [API Reference](./API_REFERENCE.md)

---

## Common Error Codes

| Error Code | Description | Solution |
|------------|-------------|----------|
| `VALIDATION_ERROR` | Request validation failed | Check request body format |
| `RESOURCE_NOT_FOUND` | Resource doesn't exist | Verify ID and soft-delete status |
| `ACCESS_DENIED` | Insufficient permissions | Check user roles |
| `INVALID_TOKEN` | JWT validation failed | Re-authenticate |
| `BUSINESS_ERROR` | Business rule violation | Check resource state |
| `DUPLICATE_RESOURCE` | Unique constraint violated | Use different identifier |
| `INTERNAL_ERROR` | Unexpected server error | Check logs for details |

---

## Next Steps

- [Getting Started](./GETTING_STARTED.md) - Setup guide
- [Configuration Guide](./CONFIGURATION.md) - Configuration options
- [Development Guide](./DEVELOPMENT.md) - Development practices

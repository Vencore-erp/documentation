# Configuration: API Gateway Service

## `application.properties` Reference

```properties
server.port=8081

spring.application.name=api-gateway

# Discovery
spring.cloud.gateway.discovery.locator.enabled=true
spring.cloud.gateway.discovery.locator.lower-case-service-id=true

# GLOBAL CORS
spring.cloud.gateway.globalcors.cors-configurations.[/**].allowedOrigins=http://localhost:3000
spring.cloud.gateway.globalcors.cors-configurations.[/**].allowedMethods=*
spring.cloud.gateway.globalcors.cors-configurations.[/**].allowedHeaders=*
spring.cloud.gateway.globalcors.cors-configurations.[/**].allowCredentials=true

# ROUTES

# 1. Auth Service
spring.cloud.gateway.routes[0].id=auth-service
spring.cloud.gateway.routes[0].uri=lb://AUTH-SERVICE
spring.cloud.gateway.routes[0].predicates[0]=Path=/api/auth/**
spring.cloud.gateway.routes[0].filters[0]=StripPrefix=2
spring.cloud.gateway.routes[0].filters[1].name=RequestRateLimiter
spring.cloud.gateway.routes[0].filters[1].args.redis-rate-limiter.replenishRate=20
spring.cloud.gateway.routes[0].filters[1].args.redis-rate-limiter.burstCapacity=40

# 2. Procurement Service
spring.cloud.gateway.routes[1].id=procurement-service
spring.cloud.gateway.routes[1].uri=lb://PROCUREMENT-SERVICE
spring.cloud.gateway.routes[1].predicates[0]=Path=/api/procurement/**
spring.cloud.gateway.routes[1].filters[0]=StripPrefix=2
spring.cloud.gateway.routes[1].filters[1]=AuthenticationFilter
spring.cloud.gateway.routes[1].filters[2].name=CircuitBreaker
spring.cloud.gateway.routes[1].filters[2].args.name=procurementCircuitBreaker
spring.cloud.gateway.routes[1].filters[2].args.fallbackUri=forward:/fallback/procurement

# 3. Vendor Service
spring.cloud.gateway.routes[2].id=vendor-service
spring.cloud.gateway.routes[2].uri=lb://VENDOR-SERVICE
spring.cloud.gateway.routes[2].predicates[0]=Path=/api/vendors/**
spring.cloud.gateway.routes[2].filters[0]=StripPrefix=2
spring.cloud.gateway.routes[2].filters[1]=AuthenticationFilter
```

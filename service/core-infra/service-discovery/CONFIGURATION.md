# Configuration: Service Discovery

## `application.properties` Reference

```properties
server.port=8761

spring.application.name=service-discovery

# Eureka Server Configuration
# Don't register with self (Standalone Mode)
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka/

# Dashboard
eureka.dashboard.enabled=true
eureka.dashboard.path=/

# Logging
logging.level.com.netflix.eureka=OFF
logging.level.com.netflix.discovery=OFF
```

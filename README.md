# RevTalent Eureka Service Discovery Server

The **Eureka Server** is the central nervous system of the **RevTalent** microservices infrastructure. It functions as a dynamic service registry, enabling microservice instances to locate and communicate with each other seamlessly without hardcoding service hostnames or ports.

---

##  What is Service Discovery? (Detailed Explanation)

In a traditional monolithic architecture, components communicate via direct method calls. In a microservices architecture, services reside on different hosts, ports, or containers, which dynamically scale up/down or restart due to updates or load changes. Hardcoding these endpoints is impossible.

**Spring Cloud Netflix Eureka** solves this with the following lifecycle:

1. **Service Registration**:
   - When any microservice (e.g., `auth-service`, `employee-service`) boots up, it automatically registers its name, IP address, and port with the Eureka registry.
   
2. **Heartbeats & Health Monitoring**:
   - Each registered microservice instance sends a periodic heartbeat (default: every 30 seconds) to the Eureka server.
   - If Eureka fails to receive heartbeats from an instance for a specific duration, it assumes the service is unhealthy and automatically evicts (removes) it from the active registry.

3. **Client-Side Service Discovery**:
   - Downstream services (like the **API Gateway**) query Eureka to retrieve the latest registry cache.
   - Using this information, client-side load balancers (such as Spring Cloud LoadBalancer) route requests (e.g. `lb://employee-service`) dynamically to a healthy instance.

---

## Dependencies Added

The following key dependencies are configured in the `pom.xml`:

- **Spring Cloud Netflix Eureka Server** (`spring-cloud-starter-netflix-eureka-server`): Integrates Netflix Eureka server engine into the Spring Boot bootstrapper.
- **Spring Web MVC** (`spring-boot-starter-webmvc`): Serves the web-based Eureka HTML Dashboard.
- **Spring Web MVC Test** (`spring-boot-starter-webmvc-test`): Framework testing dependencies.

---

##  Configuration Details

Since this is the registry itself, it does not need to register with another Eureka instance or fetch a registry copy. This is disabled in `application.properties`:

```properties
server.port=8761
spring.application.name=eureka-server

# Disable self-registration and registry fetching
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false

# Default service zone
eureka.client.service-url.defaultZone=${EUREKA_URL:http://localhost:8761/eureka/}

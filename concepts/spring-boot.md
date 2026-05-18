# Spring Boot — Notes (2026-05-18)

Overview
- Spring Boot is a framework built on top of Spring that removes the need for manual configuration. It provides sensible defaults, embedded servers, and starter dependencies so you can run apps quickly without XML or extensive setup.

Key Concepts
- Spring Initializr: start.spring.io to generate a new project with chosen dependencies.
- REST Controller: `@RestController` marks classes as HTTP API handlers; `@GetMapping("/route")` maps GET routes to methods.
- Starters: pre-packaged dependency bundles (e.g., `spring-boot-starter-web`) to avoid adding many libraries individually.
- Parent POM: Spring Boot parent manages dependency versions and compatibility.
- DevTools: `spring-boot-devtools` enables auto-restart on code changes during development.
- Actuator: provides endpoints like `/actuator/health` and `/actuator/info` for monitoring and health checks.
- Security: secure Actuator endpoints using Spring Security in production.
- Running: `mvn spring-boot:run` for development or `mvn package` + `java -jar yourapp.jar` for production.

Project Structure (Maven)
- `src/main/java` → application code
- `src/main/resources` → `application.properties` (configs)
- `src/test/java` → tests
- `pom.xml` → dependency declarations

Configuration
- Use `application.properties` (or `application.yml`) to store config like `server.port` or MQ connection strings.
- Inject with `@Value("${property.name}")` into fields.

Common Annotations
- `@SpringBootApplication` — marks the main application class and enables component scanning.
- `@RestController` — HTTP controllers
- `@GetMapping`, `@PostMapping`, etc. — route mappings
- `@Value` — inject property values

Notes
- Dev tools and Actuator are especially useful during local development and for monitoring deployed services; secure actuator endpoints before exposing them.


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

# Spring Boot — Notes (2026-05-19)

IoC & Dependency Injection — Notes (2026-05-19)

The problem first
- Normal Java code creates its own objects; IoC inverts that control so Spring creates and manages objects (beans) instead.

Inversion of Control (IoC)
- Spring's IoC Container creates objects, holds them, and provides them to classes that need them. You ask for a `Coach` and Spring decides which implementation to create and inject.

Dependency Injection (DI)
- DI is how Spring provides dependencies to classes. Example:

Without DI — class creates its own dependency:
- `private FileReader reader = new FileReader();`

With DI — Spring injects the dependency (recommended: constructor injection):
- `public PaymentService(FileReader reader) { this.reader = reader; }`

Three injection styles
1. Constructor injection (recommended) — use `@Autowired` on the constructor or rely on single-constructor autowiring.
2. Setter injection — Spring calls a setter method annotated with `@Autowired`.
3. Field injection — `@Autowired` on a field (works but less testable).

Key annotations
- `@Component` / `@Service` — register a class as a Spring bean.
- `@Autowired` — request an injected dependency.
- `@Qualifier("beanName")` / `@Primary` — disambiguate when multiple beans match.
- `@SpringBootApplication` — triggers component scanning that finds `@Component` beans.

How it works (flow)
1. App starts → `@SpringBootApplication` triggers component scan
2. Spring finds `@Component` classes and creates bean instances
3. Beans are stored in the IoC container
4. When a class has dependencies (`@Autowired`), Spring pulls matching beans from the container and injects them

Connection to MQ repos
- Look for `@Component`/`@Service` classes, constructors requesting injected beans, `application.properties` values injected with `@Value`, and no manual MQ connection creation — Spring wires them for production use.



# Java and Spring Boot Standards

Applies to Java, Spring Boot, Maven, and Gradle. Follow the project's configured Java and Spring versions.

# Code Formatting

- Use `Spotless` for all Java files.
- Formatting is enforced by `./gradlew spotlessCheck`.
- The project also enables `toggleOffOn()`, so Spotless suppression comments are respected where needed.
- Follow `.editorconfig` for general whitespace rules:

# Gradle Dependencies

- Do not add/update any dependency without permission

## Java-specific style

- Use Google Java Style conventions.
- Keep imports ordered according to `.editorconfig` / Spotless.
- Prefer 2-space indentation.
- Keep lines within 100 characters where practical.
- Use descriptive names for classes, methods and variables but keep the names smaller and human readable as much as possible
- Avoid `var` keyword for src classes, prefer explicit types. `var` can be used in tests
- Preference for immutability
- Avoid magic number and string, use constants instead
- Check emptiness and nullness before operation on collection and string as well as other objects if needed
- Prefer early returns
- Write full `if-else` block for completeness
- Name backend services with suffix `Service`.
- Always use `ZonnedDateTime` with `UTC` timezone unless otherwise specified. Truncate to millis
- Use `DTO` suffix to rest model objects
- For Rest Controller, swagger documentation must be written matching the input/output object
- Use [cloudevents](https://github.com/cloudevents/spec/blob/main/cloudevents/spec.md) structure for kafka events
- Parse oscar bags using `JsonValueParser` class available in platform library


## Lombok Annotations

- Use `@RequiredArgsConstructor` from Lombok for dependency injection via constructor.
- Use `@Slf4j` from Lombok for logging.
- Use `@Builder` for complex object creation.
- Create immutable DTO's(true immutability for collection in dtos may not be possible) using `@Value`

## Annotations

- **`@Service`**: For business logic classes.
- **`@Repository`**: For data access classes that extend mongo/JPA repositories or interact with the database.
- **`@RestController`**: For web controllers.
- **`@Component`**: For generic Spring components.
- **`@Configuration`**: For Spring configuration classes.
- **`@Autowired`**: Prefer constructor injection for production code and field injection only for tests.
- **`@ConfigurationProperties`**: For binding related properties avoid multiple `@Value` annotations. From more than 2 properties, consider using this annotation.
- **`@Transactional`**: Only Service classes should be annotated with @Transactional at class level to avoid transaction management in each method.
- **`@Validated`**: To enable Bean Validation in method parameters or classes.
- **`@PreAuthorize`**: at the controller layer when using Spring Security to enforce method-level security.
- Circular dependencies should be avoided.

## Mappers
**Use MapStruct**

- MapFor mapping between DTOs and entities.
- Define mapper interfaces with `@Mapper` annotation.
- Use `@Mapping` annotation for custom field mappings.
- Prefer non-spring component model. Use `componentModel = "spring"` to allow Spring to manage mapper instances only if needed.
- Mapper should have as suffix `Mapper` (e.g., `UserMapper`).
- Name mapper methods clearly (e.g., `toDto`, `toEntity`).

## Testing

- Use JUnit 5 for unit and integration testing.
- Use Mockito for mocking dependencies in unit tests.
- Use `@WebMvcTest(ControllerClass.class)` for testing Spring MVC controllers.
- Use `@SpringBootTest` for integration tests that require the Spring context.
- Use `given/when/then` structure in test methods for clarity.
- Use `wiremock` for mocking external rest dependency
- Method naming could follow snake_case or camelCase convention for test methods with format `methodUnderTest_should_{expectedBehaviour}_when_{conditionUnderTest}`.
- Write Integration wherever needed.(e.g. kafka producers/consumers, databases repository etc).
- use test containers from AbstractContainerBaseIT from onpier-platform project for integration tests
- Avoid reflection in tests.
- Avoid business logic in tests; focus on behavior verification.
- Do not mock ObjectMapper instance, instead inject `new ObjectMapperConfiguration().objectMapper()`
- Use bags/baglist from test resources to create bags/baglist

## Logging

- Use `@Slf4j` annotation from Lombok for logging to avoid boilerplate code with Logger instances.
- Log at appropriate levels: `DEBUG`, `INFO`, `WARN`, `ERROR`.
- Include contextual information in logs (e.g. useCaseSubject, brandKey, internal id's).
- Avoid logging sensitive information/private data.
- Use structured logging for better log management.
- Format log messages with placeholders (e.g., `{}`) instead of string concatenation.
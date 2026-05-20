# Upgrade Plan: asset-manager (20260520090913)

- **Generated**: 2026-05-20 09:20:00
- **HEAD Branch**: copilot/execute-upgrade-plan
- **HEAD Commit ID**: e85951b

## Available Tools

**JDKs**
- JDK 11: /usr/lib/jvm/temurin-11-jdk-amd64 (current project JDK, used in baseline)
- JDK 17: /usr/lib/jvm/temurin-17-jdk-amd64 (used in Step 3 intermediate)
- JDK 25: /usr/lib/jvm/temurin-25-jdk-amd64 (target, used in Step 4 and Final Validation)

**Build Tools**
- Maven Wrapper: 3.9.9 (via `.mvn/wrapper/maven-wrapper.properties`) — adequate for Java 25 (Maven 3.9.9 released Jan 2025 supports Java 25)

## Guidelines

> Note: Running in Automatic flow mode — no user confirmation pauses.

- Upgrade JDK to 25, Spring Boot to 4.x, Spring Framework to 7.x
- Migrate javax.* to jakarta.* namespaces where applicable
- Ensure all dependencies are compatible with target versions

## Options

- Working branch: appmod/java-upgrade-20260520090913
- Run tests before and after the upgrade: true

## Upgrade Goals

- Java: 11 → **25**
- Spring Boot: 2.7.14 → **4.0.0**
- Spring Framework: 5.3.x → **7.0.x** (derived from Spring Boot 4.0.0)

## Technology Stack

| Technology/Dependency               | Current  | Min Compatible | Why Incompatible                                              |
| ----------------------------------- | -------- | -------------- | ------------------------------------------------------------- |
| Java                                | 11       | 17             | User requested Java 25; Spring Boot 4.0 requires Java 17+    |
| Spring Boot                         | 2.7.14   | 4.0.0          | User requested                                                |
| Spring Framework                    | 5.3.x    | 7.0.x          | Derived from Spring Boot 4.0.0                                |
| Maven Wrapper                       | 3.9.9    | 3.9.9          | Compatible (3.9.9 released Jan 2025 supports Java 25)        |
| maven-compiler-plugin               | (managed)| 3.13+          | Managed by Spring Boot parent                                 |
| spring-boot-starter-amqp            | 2.7.14   | 4.0.0          | Managed by Spring Boot parent                                 |
| spring-boot-starter-data-jpa        | 2.7.14   | 4.0.0          | Managed by Spring Boot parent                                 |
| spring-boot-starter-web             | 2.7.14   | 4.0.0          | Managed by Spring Boot parent                                 |
| spring-boot-starter-thymeleaf       | 2.7.14   | 4.0.0          | Managed by Spring Boot parent                                 |
| Hibernate ORM                       | 5.6.x    | 7.1.x          | Managed by Spring Boot 4.0.0 (Hibernate 7.1.8.Final)         |
| lombok                              | 1.18.28  | 1.18.42        | Managed by Spring Boot 4.0.0                                  |
| software.amazon.awssdk:s3           | 2.25.13  | -              | No incompatibility; stays at 2.25.13                          |
| javax.persistence ⚠️ EOL            | JPA 2.x  | N/A            | Replaced by jakarta.persistence in Spring Boot 3.x+          |
| javax.annotation ⚠️ EOL             | 1.x      | N/A            | Replaced by jakarta.annotation in Spring Boot 3.x+           |
| H2                                  | 2.1.x    | 2.4.x          | Managed by Spring Boot 4.0.0 (H2 2.4.240)                    |
| postgresql                          | 42.x     | 42.7.x         | Managed by Spring Boot 4.0.0                                  |

## Derived Upgrades

- **Spring Boot 3.x → 4.0.0**: Requires Jakarta EE 10+, Spring Framework 7.x, Hibernate 7.x, Spring AMQP 4.0.0
- **Spring Boot 3.x migration**: Requires javax.* → jakarta.* namespace migration
- **Spring Boot 4.0.0 minimum Java**: 17 (user target is 25)
- **Hibernate 7.x**: Uses `jakarta.persistence.*` exclusively; `javax.persistence.*` entirely removed
- **jakarta.annotation 3.0.0**: `@PostConstruct` moved to `jakarta.annotation.PostConstruct`

## Upgrade Steps

- Step 1: Setup Environment
  - **Rationale**: Verify all required JDKs are available (11, 17, 25 all present). Maven wrapper 3.9.9 is sufficient.
  - **Changes to Make**: No installs needed — all JDKs and Maven already available.
  - **Verification**: JDK list confirmed via `/usr/lib/jvm/` inspection. Expected: All JDKs available.

- Step 2: Setup Baseline
  - **Rationale**: Document pre-upgrade state. Worker module already uses jakarta.* (pre-existing partial migration), so full project compilation fails. Web module compiles and its test passes.
  - **Changes to Make**: Run web module compile and tests only for baseline.
  - **Verification**: `./mvnw clean test -pl web -q`, JDK 11. Expected: 1/1 tests pass.

- Step 3: Spring Boot 2.7.14 → 3.3.13 with Java 17 (Intermediate)
  - **Rationale**: Incremental upgrade to handle javax.* → jakarta.* namespace migration. Spring Boot 3.x supports both Java 17 and jakarta.*. This bridges from javax to jakarta before moving to Spring Boot 4.0.0.
  - **Changes to Make**:
    - Update root pom.xml: Spring Boot parent 2.7.14 → 3.3.13, java.version 11 → 17
    - `web/model/ImageMetadata.java`: `javax.persistence.*` → `jakarta.persistence.*`
    - `web/service/LocalFileStorageService.java`: `javax.annotation.PostConstruct` → `jakarta.annotation.PostConstruct`
    - `web/test/resources/application.properties`: update deprecated H2 dialect property
  - **Verification**: `JAVA_HOME=/usr/lib/jvm/temurin-17-jdk-amd64 ./mvnw clean test-compile -q`. Expected: BUILD SUCCESS.

- Step 4: Spring Boot 3.3.13 → 4.0.0 with Java 25 (Final Upgrade)
  - **Rationale**: Upgrade to target Spring Boot 4.0.0 (Spring Framework 7.x) and Java 25.
  - **Changes to Make**:
    - Update root pom.xml: Spring Boot parent 3.3.13 → 4.0.0, java.version 17 → 25
    - Fix any Spring Boot 4.0.0 breaking API changes (e.g., SimpleRabbitListenerContainerFactoryConfigurer deprecation)
    - Verify test application.properties for compatibility with Hibernate 7.x
  - **Verification**: `JAVA_HOME=/usr/lib/jvm/temurin-25-jdk-amd64 ./mvnw clean test-compile -q`. Expected: BUILD SUCCESS.

- Step 5: Final Validation
  - **Rationale**: Verify all upgrade goals are met and all tests pass at 100%.
  - **Changes to Make**: Fix any remaining test failures. Clean rebuild with JDK 25.
  - **Verification**: `JAVA_HOME=/usr/lib/jvm/temurin-25-jdk-amd64 ./mvnw clean test -q`. Expected: BUILD SUCCESS, all tests pass.

## Key Challenges

- **javax.* → jakarta.* Migration**
  - **Challenge**: Web module uses `javax.persistence.*` and `javax.annotation.PostConstruct`; must migrate to jakarta.* for Spring Boot 3.x+.
  - **Strategy**: Replace imports directly in Step 3 during the intermediate Spring Boot 3.x upgrade.

- **Worker Module Pre-existing Partial Migration**
  - **Challenge**: Worker module already uses `jakarta.*` imports while Spring Boot parent was still 2.7.14 (causing baseline compile failure).
  - **Strategy**: Upgrading Spring Boot parent to 3.x+ provides the jakarta.* JARs, resolving the pre-existing compile failure.

- **Hibernate 7.x API Changes**
  - **Challenge**: Hibernate upgraded from 5.6.x to 7.1.x. Dialect configuration syntax changed.
  - **Strategy**: Remove deprecated `spring.jpa.database-platform` from test config; Hibernate 7.x auto-detects H2 dialect.

- **Spring Boot 4.0.0 API Changes**
  - **Challenge**: Potential breaking changes in Spring AMQP 4.0.0 and Spring Boot 4.0.0 auto-configuration.
  - **Strategy**: Compile with JDK 25 after upgrade; fix any API incompatibilities found.

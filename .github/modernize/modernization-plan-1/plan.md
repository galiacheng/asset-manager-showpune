# Modernization Plan: Upgrade and Migrate to Azure

**Project**: assets-manager-parent

---

## Technical Framework

- **Language**: Java 11
- **Framework**: Spring Boot 2.7.14
- **Build Tool**: Maven
- **Database**: PostgreSQL (localhost)
- **Key Dependencies**: Spring Data JPA, Spring AMQP
  (RabbitMQ), AWS SDK v2 (S3), Thymeleaf, Lombok

---

## Overview

This migration upgrades the assets-manager application
from an end-of-support Spring Boot 2.7.14 / Java 11 stack
and migrates all AWS-based services to Azure equivalents.
The application currently uses AWS S3 for object storage,
RabbitMQ for asynchronous messaging between the web and
worker modules, and a local PostgreSQL database for
metadata persistence. The new architecture will:

- Upgrade to a supported Spring Boot 3.x / Java 21
  runtime to restore long-term support and security
  patch availability
- Replace AWS S3 with Azure Blob Storage for
  cloud-native object storage on Azure
- Replace RabbitMQ with Azure Service Bus for fully
  managed, scalable messaging
- Migrate PostgreSQL to Azure Database for PostgreSQL
  with managed identity for secure, credential-free
  authentication

The migration follows a phased approach: framework
upgrade first, then service-by-service Azure migration.

---

## Migration Impact Summary

| Application       | Original Service | New Azure Service              | Authentication   | Comments                       |
|-------------------|------------------|--------------------------------|------------------|--------------------------------|
| assets-manager    | Java 11 /        | Java 21 /                      | N/A              | Upgrade runtime and            |
|                   | Spring Boot 2.7  | Spring Boot 3.x                |                  | framework                      |
| assets-manager    | AWS S3           | Azure Blob Storage             | Managed Identity | Migrate object storage         |
| assets-manager    | RabbitMQ (AMQP)  | Azure Service Bus              | Managed Identity | Migrate async messaging        |
| assets-manager    | PostgreSQL       | Azure Database for PostgreSQL  | Managed Identity | Credential-free auth           |

---

## Tasks

1. **Upgrade Spring Boot to 3.x** — Upgrade from
   Spring Boot 2.7.14 / Java 11 to Spring Boot 3.x /
   Java 21, including Spring Framework 6.x and
   Jakarta EE namespace migration.

2. **Migrate AWS S3 to Azure Blob Storage** — Replace
   AWS S3 SDK usage in both web and worker modules
   with Azure Blob Storage.

3. **Migrate RabbitMQ to Azure Service Bus** — Replace
   Spring AMQP RabbitMQ messaging in both web and
   worker modules with Azure Service Bus.

4. **Migrate PostgreSQL to Azure Database for PostgreSQL
   with Managed Identity** — Migrate database
   connectivity from password-based local PostgreSQL
   to Azure Database for PostgreSQL using managed
   identity for credential-free authentication.

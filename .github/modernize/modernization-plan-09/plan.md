# Modernization Plan: Migrate Asset Manager to Azure

**Project**: assets-manager-parent

---

## Technical Framework

- **Language**: Java 11
- **Framework**: Spring Boot 2.7.14
- **Build Tool**: Maven
- **Database**: PostgreSQL (password-based authentication)
- **Key Dependencies**: Spring Data JPA, Spring AMQP (RabbitMQ), AWS SDK for Java v2 (S3)

---

## Overview

This migration moves the Asset Manager application from AWS services to Azure-native
equivalents. The application currently uses AWS S3 for file storage, RabbitMQ for
asynchronous image-processing messaging, and PostgreSQL with password-based
authentication. The new architecture will:

- Replace AWS S3 with Azure Blob Storage for scalable, managed object storage on
  Azure, removing AWS dependency.
- Replace RabbitMQ with Azure Service Bus for reliable, cloud-native messaging
  between the web and worker modules.
- Replace password-based PostgreSQL authentication with Azure Managed Identity for
  secure, credential-free access to Azure Database for PostgreSQL.

The migration is executed in focused, independently testable phases: storage first,
then messaging, then database authentication.

---

## Migration Impact Summary

| Application              | Original Service | New Azure Service                  | Authentication   | Comments |
|--------------------------|------------------|------------------------------------|------------------|----------|
| assets-manager-web       | AWS S3           | Azure Blob Storage                 | Managed Identity |          |
| assets-manager-worker    | AWS S3           | Azure Blob Storage                 | Managed Identity |          |
| assets-manager-web       | RabbitMQ (AMQP)  | Azure Service Bus                  | Managed Identity |          |
| assets-manager-worker    | RabbitMQ (AMQP)  | Azure Service Bus                  | Managed Identity |          |
| assets-manager-web       | PostgreSQL       | Azure Database for PostgreSQL      | Managed Identity |          |
| assets-manager-worker    | PostgreSQL       | Azure Database for PostgreSQL      | Managed Identity |          |

---

## Migration Tasks

### Task 001 — Migrate AWS S3 to Azure Blob Storage

Migrate file storage operations in both `web` and `worker` modules from AWS S3 to
Azure Blob Storage using Managed Identity authentication.

### Task 002 — Migrate RabbitMQ to Azure Service Bus

Migrate asynchronous messaging in both `web` and `worker` modules from RabbitMQ
(AMQP) to Azure Service Bus using Managed Identity authentication.

### Task 003 — Migrate PostgreSQL to Azure Database for PostgreSQL with Managed Identity

Migrate database connectivity in both `web` and `worker` modules from
password-based PostgreSQL authentication to Azure Managed Identity for Azure
Database for PostgreSQL.

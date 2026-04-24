# Modernization Plan: Migrate Asset Manager to Azure

**Project**: assets-manager-parent

---

## Technical Framework

- **Language**: Java 11
- **Framework**: Spring Boot 2.7.14
- **Build Tool**: Maven
- **Database**: PostgreSQL (localhost, password-based auth)
- **Key Dependencies**: Spring Data JPA, Spring AMQP (RabbitMQ), AWS SDK v2 (S3)

---

## Overview

This migration moves the Asset Manager application from AWS and local infrastructure
to Azure managed services. The application currently uses AWS S3 for file/image
storage, RabbitMQ for asynchronous messaging between the web and worker modules,
and PostgreSQL with plaintext credentials. The new architecture will:

- Replace AWS S3 with Azure Blob Storage for secure, scalable file storage with
  Managed Identity authentication
- Replace RabbitMQ (AMQP) with Azure Service Bus for reliable, managed messaging
  between the web and worker modules
- Migrate PostgreSQL connectivity to use Azure Managed Identity for secure,
  credential-free database access

The migration follows a task-by-task approach where each Azure service replacement
is independently addressable and testable.

---

## Migration Impact Summary

| Application             | Original Service | New Azure Service          | Authentication     | Comments                        |
|-------------------------|------------------|----------------------------|--------------------|---------------------------------|
| assets-manager-web      | AWS S3           | Azure Blob Storage         | Managed Identity   | File upload and listing         |
| assets-manager-worker   | AWS S3           | Azure Blob Storage         | Managed Identity   | Thumbnail download/upload       |
| assets-manager-web      | RabbitMQ (AMQP)  | Azure Service Bus          | Managed Identity   | Publish image-processing msgs   |
| assets-manager-worker   | RabbitMQ (AMQP)  | Azure Service Bus          | Managed Identity   | Consume image-processing msgs   |
| assets-manager-web      | PostgreSQL       | Azure Database for PostgreSQL | Managed Identity | Image metadata persistence      |
| assets-manager-worker   | PostgreSQL       | Azure Database for PostgreSQL | Managed Identity | Image metadata persistence      |

---

## Migration Tasks

### Task 1 — Migrate AWS S3 to Azure Blob Storage

Migrate both `web` and `worker` modules from AWS S3 SDK to Azure Blob Storage,
replacing all upload, download, list, and delete operations. Managed Identity
will be used for authentication.

### Task 2 — Migrate RabbitMQ to Azure Service Bus

Migrate both `web` and `worker` modules from Spring AMQP with RabbitMQ to
Azure Service Bus. The `web` module publishes image-processing messages and
the `worker` module consumes them.

### Task 3 — Migrate PostgreSQL to Azure Managed Identity

Migrate both `web` and `worker` modules from password-based PostgreSQL
connectivity to Azure Managed Identity authentication for Azure Database
for PostgreSQL.

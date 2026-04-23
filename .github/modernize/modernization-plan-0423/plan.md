# Modernization Plan: Migrate Asset Manager to Azure

**Project**: asset-manager

---

## Technical Framework

- **Language**: Java 11
- **Framework**: Spring Boot 2.7.14
- **Build Tool**: Maven
- **Database**: PostgreSQL (password-based authentication)
- **Key Dependencies**: Spring Data JPA, Spring AMQP (RabbitMQ), AWS SDK for S3, Lombok, Jackson

---

## Overview

This migration moves the asset-manager application (web + worker modules) from AWS and on-premises services to Azure-managed services. The application currently stores files in AWS S3, processes messages via RabbitMQ, persists metadata in PostgreSQL using password-based credentials, and stores plaintext credentials in configuration files. The new architecture will:

- Replace AWS S3 with Azure Blob Storage for secure, scalable file storage on Azure
- Replace RabbitMQ with Azure Service Bus for managed, reliable messaging
- Migrate PostgreSQL connectivity to use Azure Managed Identity, eliminating password-based authentication
- Move plaintext credentials to Azure Key Vault for secure secrets management

The migration proceeds service by service, with each task independently executable and verifiable.

---

## Migration Impact Summary

| Application         | Original Service | New Azure Service          | Authentication     | Comments |
|---------------------|------------------|----------------------------|--------------------|----------|
| web, worker         | AWS S3           | Azure Blob Storage         | Managed Identity   |          |
| web, worker         | RabbitMQ (AMQP)  | Azure Service Bus          | Managed Identity   |          |
| web, worker         | PostgreSQL       | Azure Database for PostgreSQL | Managed Identity |          |
| web, worker         | Plaintext config | Azure Key Vault            | Managed Identity   |          |

---

## Migration Tasks

### Task 1: Migrate AWS S3 to Azure Blob Storage

Migrate file upload, download, and listing operations in both `web` and `worker` modules from AWS S3 to Azure Blob Storage with Managed Identity authentication.

### Task 2: Migrate RabbitMQ to Azure Service Bus

Migrate the AMQP-based messaging in both `web` and `worker` modules from RabbitMQ to Azure Service Bus with Managed Identity authentication.

### Task 3: Migrate PostgreSQL to Azure Database for PostgreSQL with Managed Identity

Migrate the database connectivity in both `web` and `worker` modules from password-based PostgreSQL authentication to Azure Managed Identity.

### Task 4: Migrate Plaintext Credentials to Azure Key Vault

Remove plaintext AWS, RabbitMQ, and PostgreSQL credentials from configuration files and store them securely in Azure Key Vault.

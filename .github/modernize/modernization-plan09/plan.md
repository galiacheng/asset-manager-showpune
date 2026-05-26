# Modernization Plan: Asset Manager Azure Migration

**Project**: Asset Manager

---

## Technical Framework

- **Language**: Java 25
- **Framework**: Spring Boot 4.0.0-RC2
- **Build Tool**: Maven Wrapper / Maven multi-module build
- **Database**: PostgreSQL
- **Key Dependencies**: Spring Web, Thymeleaf, Spring AMQP, Spring Data JPA, AWS SDK for S3

---

## Overview

> This migration moves the Asset Manager application from its current AWS-
> aligned integrations to Azure services. The application currently stores
> images in Amazon S3, exchanges processing messages through RabbitMQ, and
> persists metadata in PostgreSQL using password-based authentication.
>
> The new architecture will:
>
> - Move image storage and retrieval to Azure Blob Storage
> - Replace RabbitMQ-based processing with Azure Service Bus messaging
> - Run the application on Azure with Azure Database for PostgreSQL and
>   managed identity aligned connectivity
>
> The migration follows a phased approach that updates storage, messaging,
> data access, security remediation, and deployment in sequence so existing
> application behavior can be preserved while the hosting platform changes.

---

## Migration Impact Summary

| Application | Original Service | New Azure Service | Authentication | Comments |
|-------------|------------------|-------------------|----------------|----------|
| Web module | Amazon S3 | Azure Blob Storage | Managed identity | Preserve upload, list, view, and delete flows |
| Worker module | Amazon S3 | Azure Blob Storage | Managed identity | Preserve original download and thumbnail upload |
| Web + Worker | RabbitMQ | Azure Service Bus | Managed identity | Preserve async image processing flow |
| Web + Worker | PostgreSQL | Azure Database for PostgreSQL | Managed identity | Preserve metadata persistence and reads |
| Web + Worker | Current local hosting | Azure Container Apps | Managed identity | Default Azure deployment target |

---

## Planned Modernization Scope

### Storage Migration

Migrate the application's storage integration from Amazon S3 to Azure Blob
Storage across the web and worker modules while keeping the existing user
workflows and local development profile intact.

### Messaging Migration

Replace RabbitMQ-backed message publishing and consumption with Azure Service
Bus so thumbnail processing continues asynchronously after the Azure cutover.

### Database Connectivity Modernization

Update the application's PostgreSQL connectivity so production Azure runs use
Azure Database for PostgreSQL with managed identity aligned authentication
instead of password-based runtime credentials.

### Security Compliance

Scan the solution for dependency CVEs and remediate confirmed vulnerabilities
before deployment so the migrated application is built on supported packages.

### Deployment

Deploy the web and worker services to Azure Container Apps as the default Azure
hosting target for this modernization plan.

---

## Open Questions & Questionnaire

- [x] Q: Should the plan include environment/infrastructure provisioning? → A: No — focus on application migration and deployment rather than a separate IaC generation task.
- [x] Q: Should the plan include integration testing to verify migrated services? → A: No — a standalone integration-testing task was not added because it was not explicitly requested.
- [x] Q: Should the plan include a security scan and CVE remediation task? → A: Yes — include a security remediation task before deployment.
- [x] Q: Which Azure deployment target should the plan use? → A: Azure Container Apps (default).
- [x] Q: Should the plan include containerization? → A: Containerization is covered by the Azure Container Apps deployment task.

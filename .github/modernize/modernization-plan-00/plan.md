# Modernization Plan: Asset Manager - Azure Migration

**Project**: assets-manager-parent

---

## Technical Framework

- **Language**: Java 11
- **Framework**: Spring Boot 2.7.14, Spring Framework 5.x
- **Build Tool**: Maven
- **Database**: PostgreSQL (local JDBC, password-based authentication)
- **Key Dependencies**: Spring Data JPA, Spring AMQP (RabbitMQ), AWS SDK S3, Hibernate

---

## Overview

This migration modernizes the Asset Manager application — a multi-module Spring Boot project (web + worker) — by upgrading its runtime and migrating all infrastructure dependencies to Azure managed services.

The application currently runs on Java 11 with Spring Boot 2.7.14 (end of OSS support), stores files in AWS S3 and local disk, uses a local PostgreSQL database with password-based authentication, and exchanges messages via RabbitMQ with hardcoded credentials.

The new architecture will:

- Upgrade to Java 21 and Spring Boot 3.x for long-term support and Jakarta EE compatibility
- Replace AWS S3 with Azure Blob Storage for scalable, managed object storage
- Migrate local file system usage to Azure-hosted storage
- Connect to Azure Database for PostgreSQL using Managed Identity for credential-free authentication
- Replace RabbitMQ with Azure Service Bus for fully managed messaging with Managed Identity authentication
- Package the application as a container (Dockerfile) ready for Azure deployment

The migration follows a phased approach: runtime upgrade first, then service migrations in parallel, and finally containerization.

---

## Migration Impact Summary

| Application | Original Service | New Azure Service | Authentication | Comments |
|---|---|---|---|---|
| assets-manager (web + worker) | AWS S3 | Azure Blob Storage | Managed Identity | S3 dependency in both modules |
| assets-manager (web + worker) | Local file system (Java NIO) | Azure Blob Storage (mounted) | Managed Identity | LocalFileStorageService & LocalFileProcessingService |
| assets-manager (web + worker) | PostgreSQL (local JDBC) | Azure Database for PostgreSQL | Managed Identity | Password-based auth replaced |
| assets-manager (web + worker) | RabbitMQ (Spring AMQP) | Azure Service Bus | Managed Identity | spring-boot-starter-amqp in both modules |

---

## Migration Tasks

### Phase 1 — Runtime Upgrade

| # | Task | Type |
|---|---|---|
| 001 | Upgrade Spring Boot to 3.x (includes Java 21 and Jakarta EE migration) | upgrade |

### Phase 2 — Service Migrations

| # | Task | Type |
|---|---|---|
| 002 | Migrate AWS S3 to Azure Blob Storage | transform |
| 003 | Migrate local file system storage to Azure Blob Storage (mounted) | transform |
| 004 | Migrate PostgreSQL to Azure Database for PostgreSQL with Managed Identity | transform |
| 005 | Migrate RabbitMQ (Spring AMQP) to Azure Service Bus with Managed Identity | transform |

### Phase 3 — Containerization

| # | Task | Type |
|---|---|---|
| 006 | Create Dockerfile for containerization | containerization |

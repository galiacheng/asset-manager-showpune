# Modernization Plan: modernization-plan-0420

**Project**: assets-manager-parent

---

## Technical Framework

- **Language**: Java 11
- **Framework**: Spring Boot 2.7.14
- **Build Tool**: Maven
- **Database**: PostgreSQL
- **Key Dependencies**: Spring Data JPA, AWS SDK S3,
  Spring AMQP (RabbitMQ), Lombok, Thymeleaf

---

## Overview

This migration upgrades the assets-manager application
from legacy Java 11 / Spring Boot 2.7.14 to the latest
LTS versions and migrates all cloud services from AWS
to Azure. The application currently uses AWS S3 for
file storage, RabbitMQ for messaging between the web
and worker modules, and PostgreSQL for data persistence.
The new architecture will:

- Upgrade to Java 21 and Spring Boot 3.x for long-term
  support, security patches, and Jakarta EE compatibility
- Replace AWS S3 with Azure Blob Storage for cloud-native
  file management on Azure
- Replace RabbitMQ with Azure Service Bus for fully
  managed messaging
- Migrate PostgreSQL to Azure Database for PostgreSQL
  with managed identity for secure, credential-free
  authentication

The migration follows a phased approach starting with
the platform upgrade, followed by individual service
migrations.

---

## Migration Impact Summary

| Application | Original Service   | New Azure Service              | Authentication | Comments               |
|-------------|--------------------|--------------------------------|----------------|------------------------|
| web, worker | Java 11 / SB 2.7   | Java 21 / SB 3.x              | N/A            | Platform upgrade       |
| web, worker | AWS S3             | Azure Blob Storage             | Managed Identity | File storage         |
| web, worker | RabbitMQ (AMQP)    | Azure Service Bus              | Managed Identity | Messaging            |
| web, worker | PostgreSQL (local) | Azure Database for PostgreSQL  | Managed Identity | Database             |

---

## Tasks

### Task 1: Upgrade Spring Boot to 3.x

- **Type**: upgrade
- **ID**: 001-upgrade-spring-boot-3x
- **Description**: Upgrade Spring Boot from 2.7.14 to 3.x,
  including Java 11 to 21, Spring Framework 5 to 6,
  and javax to jakarta namespace migration

### Task 2: Migrate AWS S3 to Azure Blob Storage

- **Type**: transform
- **ID**: 002-transform-migration-s3-to-azure-blob-storage
- **Skill**: migration-s3-to-azure-blob-storage
- **Description**: Migrate file storage from AWS S3 to
  Azure Blob Storage in both web and worker modules

### Task 3: Migrate RabbitMQ to Azure Service Bus

- **Type**: transform
- **ID**: 003-transform-migration-rabbitmq-to-servicebus
- **Skill**: migration-amqp-rabbitmq-servicebus
- **Description**: Migrate messaging from RabbitMQ with
  Spring AMQP to Azure Service Bus in both web and
  worker modules

### Task 4: Migrate PostgreSQL to Azure Database for PostgreSQL

- **Type**: transform
- **ID**: 004-transform-migration-postgresql-to-azure-postgresql
- **Skill**: migration-mi-postgresql-azure-sdk-public-cloud
- **Description**: Migrate database connectivity from local
  PostgreSQL with password-based auth to Azure Database
  for PostgreSQL with managed identity

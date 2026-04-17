# Modernization Plan: Asset Manager Azure Migration

**Project**: assets-manager-parent

---

## Technical Framework

- **Language**: Java 11
- **Framework**: Spring Boot 2.7.14
- **Build Tool**: Maven
- **Database**: PostgreSQL (local, password-based authentication)
- **Key Dependencies**: Spring Data JPA, Spring AMQP (RabbitMQ),
  AWS SDK v2 (S3), Thymeleaf, Lombok

---

## Overview

This migration upgrades and modernizes the Asset Manager
application for Azure. The application currently runs on
Spring Boot 2.7.14 with Java 11, uses AWS S3 for file storage,
RabbitMQ for message queuing, and PostgreSQL with hardcoded
credentials for data persistence. The new architecture will:

- Upgrade to Spring Boot 3.x with Java 21 for long-term support
  and modern language features
- Replace AWS S3 with Azure Blob Storage for cloud-native file
  management
- Migrate from RabbitMQ to Azure Service Bus for managed messaging
- Move to Azure Database for PostgreSQL with Managed Identity for
  secure, credential-free database access
- Containerize both web and worker modules for cloud deployment

The migration follows a phased approach starting with the framework
upgrade, followed by Azure service migrations, and concluding with
containerization.

---

## Migration Impact Summary

| Application    | Original Service        | New Azure Service                  | Authentication  | Comments                                        |
|----------------|-------------------------|------------------------------------|-----------------|--------------------------------------------------|
| assets-manager | Spring Boot 2.7 / Java 11 | Spring Boot 3.x / Java 21      | N/A             | Framework and runtime upgrade                    |
| assets-manager | AWS S3                  | Azure Blob Storage                 | Managed Identity| Migrate file storage in web and worker modules   |
| assets-manager | RabbitMQ (Spring AMQP)  | Azure Service Bus                  | Managed Identity| Migrate messaging with manual ACK and retry      |
| assets-manager | PostgreSQL (local)      | Azure Database for PostgreSQL      | Managed Identity| Replace hardcoded credentials with credential-free auth |
| assets-manager | No containerization     | Docker containers                  | N/A             | Create Dockerfiles for web and worker modules    |

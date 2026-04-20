# Modernization Plan: assets-manager Azure Migration

**Project**: assets-manager-parent

---

## Technical Framework

- **Language**: Java 11
- **Framework**: Spring Boot 2.7.14
- **Build Tool**: Maven
- **Database**: PostgreSQL (local, password-based authentication)
- **Key Dependencies**: Spring Data JPA, AWS S3 SDK 2.25.13,
  Spring AMQP (RabbitMQ), Thymeleaf, Lombok

---

## Overview

This migration upgrades and modernizes the assets-manager
multi-module application for Azure. The application currently
runs on Spring Boot 2.7.14 with Java 11, uses AWS S3 for
file storage, RabbitMQ for asynchronous messaging between
web and worker modules, and PostgreSQL with password-based
authentication. The new architecture will:

- Upgrade to Spring Boot 3.x with Java 21 for long-term
  support and modern Jakarta EE namespace
- Replace AWS S3 with Azure Blob Storage for file management
  across both web and worker modules
- Replace RabbitMQ with Azure Service Bus for messaging
  between the web and worker modules
- Migrate to Azure Database for PostgreSQL with Managed
  Identity for secure, credential-free authentication
- Containerize both modules for Azure deployment readiness

The migration follows a phased approach: upgrade the
runtime first, then migrate cloud services to Azure
equivalents, and finally containerize the application.

---

## Migration Impact Summary

| Application | Original Service   | New Azure Service              | Authentication | Comments                        |
|-------------|--------------------|--------------------------------|----------------|---------------------------------|
| web, worker | AWS S3             | Azure Blob Storage             | Managed Identity | File uploads and thumbnails   |
| web, worker | RabbitMQ (AMQP)    | Azure Service Bus              | Managed Identity | Async messaging               |
| web, worker | PostgreSQL (local) | Azure Database for PostgreSQL  | Managed Identity | Credential-free auth          |

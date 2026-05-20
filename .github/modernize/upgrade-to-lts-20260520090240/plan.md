# Upgrade Plan

## Overview

Upgrade the Java application to the latest LTS versions: **Java 25**, **Spring Boot 4.x**, and **Spring Framework 7.x**, including the Jakarta EE namespace migration (`javax.*` -> `jakarta.*`).

## Tasks

See `.metadata/tasks.json` for the detailed task breakdown.

| # | Task | Type | Status |
|---|------|------|--------|
| 001 | Upgrade to Java 25 and Spring Boot 4.x with Spring Framework 7.x and Jakarta EE migration | upgrade | pending |

## Upgrade Scope

- **Java**: Upgrade to JDK 25 (latest LTS)
- **Spring Boot**: Upgrade to 4.x (latest, requires Java 25)
- **Spring Framework**: Upgrade to 7.x (included with Spring Boot 4.x)
- **Jakarta EE**: Migrate `javax.*` imports to `jakarta.*` namespace

## Notes

- Spring Boot 4.x upgrade includes Spring Framework 7.x and Jakarta EE migration - no separate sub-tasks needed.
- All existing unit tests must pass after the upgrade.
- Build must succeed with the new dependency versions.

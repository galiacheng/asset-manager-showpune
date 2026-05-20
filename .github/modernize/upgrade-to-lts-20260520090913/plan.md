# Upgrade Plan

## Overview

Upgrade to the latest LTS versions: Java 25, Spring Boot 4.x, and Spring Framework 7.x. This includes migration from `javax.*` to `jakarta.*` namespaces as required by the Jakarta EE standard adopted in Spring Boot 3+.

## Tasks

See `.metadata/tasks.json` for the detailed task breakdown.

| # | Task | Description |
|---|------|-------------|
| 1 | `001-upgrade-java-spring-boot` | Upgrade to Java 25 and Spring Boot 4.x (includes Spring Framework 7.x and javax → jakarta migration) |

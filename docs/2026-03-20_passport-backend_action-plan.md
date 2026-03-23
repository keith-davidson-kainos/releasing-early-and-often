# Passport Backend Dependency Action Plan (One-Pager)

Date: 2026-03-20
Scope: direct dependencies declared in `passport-backend/pom.xml`

## RAG Legend

- Red: urgent upgrade required (security, EOL, or clear incompatibility risk)
- Amber: upgrade recommended soon (compatibility, supportability, or governance risk)
- Green: no immediate upgrade pressure identified

## Dependency Upgrade Plan

| Dependency | Resolved Version | Scope | RAG | Why | Recommended Action |
|---|---:|---|---|---|---|
| org.springframework.boot:spring-boot-starter-webmvc | 4.0.0 | compile | Green | Current major line for this project baseline and centrally BOM-managed. | Keep on Boot BOM and review on each 4.x patch release cycle. |
| org.springframework.boot:spring-boot-starter-validation | 4.0.0 | compile | Green | BOM-managed and aligned with the active platform set. | Keep aligned to Spring Boot patch cadence. |
| org.springframework.boot:spring-boot-starter-data-jpa | 4.0.0 | compile | Green | BOM-managed and aligned with Hibernate/JPA stack resolved by Boot. | Keep aligned to Spring Boot patch cadence. |
| com.h2database:h2 | 2.4.240 | runtime | Amber | Dual-license profile (MPL 2.0 / EPL 1.0) plus runtime footprint; no immediate EOL flag but governance and upgrade hygiene needed. | Keep current for training use; add legal-policy check and review latest patch monthly. |
| org.springframework.boot:spring-boot-h2console | 4.0.0 | compile | Green | Framework-aligned; mostly development/runtime tooling support. | Retain with Boot BOM alignment; verify not exposed in production-like deployments. |
| org.springframework.boot:spring-boot-starter-flyway | 4.0.0 | compile | Green | BOM-managed and currently resolves cleanly to Flyway core without conflict markers. | Continue standard Boot patch upgrades and migration regression tests. |
| org.springdoc:springdoc-openapi-starter-webmvc-ui | 2.8.0 | compile | Amber | Compatibility watchpoint against Boot 4 managed graph and mixed Jackson family presence. | Prioritize validation against latest springdoc compatible release for Boot 4; upgrade in next maintenance sprint. |
| org.projectlombok:lombok | 1.18.42 | compile | Green | Current stable line; low runtime risk because compile-time oriented. | Keep current; review only on major JDK/toolchain changes. |
| org.springframework.boot:spring-boot-starter-test | 4.0.0 | test | Green | Test-scope only and fully BOM-managed with resolved graph. | Maintain via Boot patch updates with test suite execution. |

## Priority Actions (Next 30 Days)

1. Validate and, if available, upgrade `org.springdoc:springdoc-openapi-starter-webmvc-ui` to the latest release proven with Boot 4.
2. Add a lightweight dependency governance check in CI for license policy and patch freshness.
3. Re-run dependency tree and risk assessment after any Boot or springdoc version changes.

# Passport Backend Risk Assessment

Date: 2026-03-20
Scope: `passport-backend` only

## Inputs And Evidence

- Build and resolution executed in `passport-backend` with Maven:
  - `mvn -DskipTests package`
  - `mvn -DskipTests dependency:tree -Dverbose -DoutputFile=target/dependency-tree.txt`
  - `mvn -DskipTests dependency:list -DincludeScope=runtime -DoutputFile=target/dependency-list.txt`
- Evidence files used:
  - `passport-backend/target/dependency-tree.txt`
  - `passport-backend/target/dependency-list.txt`
  - `passport-backend/target/license-risk-candidates.txt`

## A) End-Of-Life (EOL) And Near-EOL (Within 12 Months)

### Findings

- No resolved dependency was conclusively identified as past EOL from Maven-resolved metadata in this assessment run.
- No dependency was conclusively identified as reaching EOL within the next 12 months from the available local metadata.

### Notable Watch Item

- `org.springdoc:springdoc-openapi-starter-webmvc-ui:2.8.0` is in use alongside Spring Boot 4.0.0 managed dependencies. This is not an EOL finding by itself, but it is a compatibility/lifecycle watchpoint because dependency management assumptions differ across major framework lines.

## B) Transitive Dependency Version Conflicts

### Findings

- No direct Maven conflict-mediation entries (`omitted for conflict`) were observed in the resolved dependency tree.
- The tree does contain many `version managed from ...` and `omitted for duplicate` entries, which are expected under BOM-managed resolution and deduplication.

### Notable Watch Item

- Both Jackson families appear in the resolved graph:
  - `tools.jackson.core:*` (3.x line)
  - `com.fasterxml.jackson.core:*` (2.x line)
- Maven resolves this graph, but dual-family presence can increase serialization compatibility risk in specific integrations.

## C) License Risk Surface (Proprietary Context)

### Potentially Sensitive Licenses Observed

- `com.h2database:h2:2.4.240` -> `MPL 2.0`, `EPL 1.0`
- `jakarta.annotation:jakarta.annotation-api:3.0.0` -> `EPL 2.0`, `GPL2 w/ CPE`
- `jakarta.servlet:jakarta.servlet-api:6.1.0` -> `EPL 2.0`, `GPL2 w/ CPE`
- `jakarta.transaction:jakarta.transaction-api:2.0.1` -> `EPL 2.0`, `GPL2 w/ CPE`

### Risk Interpretation

- No strong copyleft license (for example AGPL or GPL without exception) was identified in the resolved dependency set.
- Weak-copyleft and dual-license components are present and typically manageable in proprietary deployments, but they should still go through legal policy review for redistribution scenarios.

## Overall Assessment

- EOL risk: Low confidence to flag critical issues from local metadata alone; no immediate EOL red flags identified.
- Version conflict risk: Low for hard Maven conflicts; medium for compatibility watchpoints in mixed dependency families.
- License risk: Medium from governance/compliance perspective due to weak-copyleft and dual-license components requiring policy confirmation.

## Recommended Follow-Up

1. Add an automated lifecycle feed check tied to resolved dependency versions.
2. Add a CI license policy gate for runtime dependencies.
3. Add a smoke/integration test targeting JSON serialization paths where mixed Jackson families might surface issues.
4. Re-run this assessment after any Spring Boot or springdoc version change.

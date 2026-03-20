# Passport Frontend Risk Assessment

Date: 2026-03-20  
Scope: passport-frontend only (SBOM analysis constrained to frontend direct dependencies and their transitive closure)

## Inputs And Method

- SBOM source: `docs/2026-03-20_sbom.json`
- Frontend direct dependency baseline from `passport-frontend/package-lock.json`:
  - axios 1.13.6
  - express 4.22.1
  - express-session 1.18.2
  - govuk-frontend 5.13.0
  - nunjucks 3.2.4
  - chai 4.5.0
  - mocha 11.7.5
  - nodemon 3.1.11
  - sinon 18.0.1
  - sinon-chai 3.7.0
  - supertest 7.2.2
- SBOM graph filtering approach:
  - seed with the frontend direct dependencies above
  - traverse `DEPENDS_ON` relationships for npm packages only
  - exclude dependencies attributable only to other subprojects

## A) EOL / Near-EOL (Within 12 Months)

### Findings

- No frontend-scoped component was conclusively identified as past EOL from SBOM metadata.
- No frontend-scoped component was conclusively identified as approaching EOL within 12 months from SBOM metadata.

### Watch Item

- `nunjucks@3.2.4` appears comparatively less active than most other direct dependencies. This is a maintenance watchpoint (amber) rather than a confirmed EOL event.

### Deprecation Signal Check

- No npm `deprecated` markers were returned for key direct frontend dependencies at analysis time.

## B) Transitive Version Conflicts

The frontend-scoped transitive graph shows multiple packages present in more than one version (version skew). This is common in npm trees, but increases maintenance and security review complexity.

### Detected Multi-Version Packages (examples)

- `debug`: 2.6.9 and 4.4.3
- `path-to-regexp`: 0.1.12 and 8.3.0
- `mime`: 1.6.0 and 2.6.0
- `minimatch`: 3.1.5 and 9.0.9
- `supports-color`: 5.5.0, 7.2.0, 8.1.1
- `chokidar`: 3.6.0 and 4.0.3
- `ms`: 2.0.0 and 2.1.3
- `strip-ansi`: 6.0.1 and 7.2.0
- `string-width`: 4.2.3 and 5.1.2
- `type-detect`: 4.0.8 and 4.1.0
- `@sinonjs/fake-timers`: 11.2.2 and 13.0.5
- `cookie-signature`: 1.0.7 and 1.2.2
- `wrap-ansi`: 7.0.0 and 8.1.0

### Interpretation

- This is not definitive runtime breakage evidence.
- It is a medium risk indicator for drift, duplicate patch management effort, and future upgrade friction.

## C) License Risk Surface (Proprietary Context)

### Findings

- No copyleft indicators were detected in the frontend-scoped subset when scanning `licenseConcluded` for:
  - GPL
  - LGPL
  - AGPL
  - EPL
  - MPL
  - CDDL
  - SSPL
  - BUSL

### Interpretation

- No immediate copyleft red flags were identified for the frontend-scoped graph from SBOM-declared license fields.
- Residual compliance risk remains where SBOM license metadata is incomplete or inaccurate upstream.

## Overall Assessment

- EOL risk: Low-confidence/no immediate red findings from SBOM metadata.
- Version conflict risk: Medium due to several multi-version transitive packages.
- License risk: Low based on current frontend-scoped SBOM license declarations.

## Recommended Follow-Up

1. Add CI checks to surface duplicate major versions for selected high-risk transitive packages.
2. Prioritize dependency deduplication during routine upgrade windows (starting with `debug`, `path-to-regexp`, and `minimatch` families).
3. Keep a quarterly review for `nunjucks` maintenance status and alternatives if long-term activity remains low.
4. Re-run this assessment after major dependency updates or lockfile regeneration.

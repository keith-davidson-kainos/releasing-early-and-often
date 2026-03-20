# Passport Frontend Dependency Action Plan (One-Pager)

Date: 2026-03-20  
Scope: direct dependencies in passport-frontend

## RAG Legend

- Red: urgent upgrade recommended now (high exposure or high likelihood of compatibility/support risk)
- Amber: upgrade recommended in next maintenance cycle
- Green: no immediate upgrade pressure

## Dependency Upgrade Plan

| Dependency | Current | Latest | Scope | RAG | Why | Recommended Action |
|---|---:|---:|---|---|---|---|
| axios | 1.13.6 | 1.13.6 | runtime | Green | Already on latest published version. | Keep pinned via regular lockfile refreshes. |
| express | 4.22.1 | 5.2.1 | runtime | Amber | Major version behind on core web framework; long-term supportability and ecosystem drift risk. | Plan controlled upgrade to 5.x with route/middleware regression testing. |
| express-session | 1.18.2 | 1.19.0 | runtime | Amber | Behind latest minor for session middleware used in core journey state. | Upgrade in next maintenance sprint and run session-flow UI tests. |
| govuk-frontend | 5.13.0 | 6.1.0 | runtime | Amber | Major version behind for design system package; risk of UI/accessibility drift over time. | Evaluate 6.x migration guide and upgrade with visual regression checks. |
| nunjucks | 3.2.4 | 3.2.4 | runtime | Amber | No newer release published; package appears comparatively less active. | Keep current but monitor maintenance status quarterly and assess alternatives if needed. |
| chai | 4.5.0 | 6.2.2 | dev/test | Amber | Major version behind in test assertion library; lower production risk but test tooling lag. | Upgrade during test tooling refresh and run full unit suite. |
| mocha | 11.7.5 | 11.7.5 | dev/test | Green | Already on latest published version. | No action beyond regular dependency refresh. |
| nodemon | 3.1.11 | 3.1.14 | dev | Green | Minor patch gap only for local developer tool. | Pick up in routine dependency bump. |
| sinon | 18.0.1 | 21.0.3 | dev/test | Amber | Several major versions behind in mocking/stubbing framework. | Upgrade with focused unit-test compatibility updates. |
| sinon-chai | 3.7.0 | 4.0.1 | dev/test | Amber | Major version behind and tied to sinon/chai compatibility matrix. | Upgrade alongside sinon/chai to avoid matcher compatibility issues. |
| supertest | 7.2.2 | 7.2.2 | dev/test | Green | Already on latest published version. | No immediate action required. |

## 30-Day Priority Actions

1. Prioritize runtime-path upgrades: express, express-session, and govuk-frontend.
2. Bundle test-stack upgrades together: chai, sinon, sinon-chai.
3. Keep nunjucks on active watchlist due to maintenance activity concerns.
4. Re-run SBOM risk assessment after lockfile updates.

# Architecture Overview

## System Purpose And User Groups

This repository is a training system for progressive delivery using a simplified UK adult passport application journey. It is designed to demonstrate how a web frontend, backend API, and UI automation suite evolve together while keeping boundaries between components explicit.

Key user groups:

- Adult passport applicants completing the journey.
- Developers extending frontend, backend, and test code.
- QA engineers and trainers validating behaviour across the full stack.

## Major Components And Responsibilities

### passport-frontend

- Node.js and Express application serving the multi-step passport journey.
- Renders GOV.UK-style pages with Nunjucks templates.
- Stores in-progress user journey data in server-side session state.
- Performs input validation and controls navigation between steps.
- Calls the backend API at the check-answers step when the backend integration feature flag is enabled.

Boundary:

- Does not write directly to the database.
- Depends on the backend only through HTTP.

### passport-backend

- Java 21 and Spring Boot 4 REST API.
- Exposes versioned application submission endpoints under `/api`.
- Validates and maps request DTOs to persistence entities.
- Persists passport applications through Spring Data JPA.
- Owns database schema evolution through Flyway migrations.

Boundary:

- Sole owner of persistent application data.
- Does not manage browser flow or UI rendering.

### Database Layer

- H2 in-memory database configured in PostgreSQL compatibility mode.
- Used only by the backend service.
- Schema is managed by Flyway and validated by Hibernate on startup.

Boundary:

- No shared database access from the frontend or UI tests.

### passport-ui-tests

- Separate Node.js Selenium test pack.
- Uses a Page Object Model to drive the frontend through a browser.
- Validates end-to-end behaviour from the outside in.

Boundary:

- Interacts only through the browser and frontend URLs.
- Does not call backend internals or access the database.

## Interaction Patterns

- Browser to frontend uses synchronous HTTP GET and POST requests.
- Frontend to backend uses a synchronous HTTP POST to submit applications.
- Backend to database uses synchronous JPA repository access.
- Flyway applies schema migrations during backend startup.
- Frontend state is stored in Express session memory during the journey.
- No asynchronous messaging, event bus, or message broker is present.
- No shared database exists across components.

## External Integrations And APIs

- Backend REST API: `POST /api/applications`
- API versioning via `X-API-Version` request header.
- Swagger UI and OpenAPI documentation exposed by the backend.
- H2 console enabled for development and training use.
- GOV.UK Frontend assets consumed by the frontend.
- Selenium WebDriver used by the UI automation suite.

There are no external third-party business APIs, queues, or messaging platforms in the current implementation.

## Key Dependencies

### Frontend

- Node.js
- Express
- Nunjucks
- express-session
- Axios
- GOV.UK Frontend

### Backend

- Java 21
- Spring Boot 4
- Spring MVC
- Spring Validation
- Spring Data JPA
- Flyway
- Lombok
- springdoc OpenAPI

### Data And Testing

- H2 in-memory database
- Mocha
- Chai
- Sinon
- Selenium WebDriver

There is no message broker in the current architecture.

## Known Hotspots, Risks, And Technical Debt

- Session state is stored in-process in the frontend, so state is lost on restart and the application does not scale cleanly across multiple instances.
- The backend uses an in-memory H2 database, which is useful for training but does not exercise realistic persistence or operational recovery.
- The check-answers controller falls back to generated reference numbers if the backend call fails, which reduces user-facing disruption but can mask integration defects.
- Backend versioning uses custom header-based configuration, which is less obvious than URL-based versioning and increases framework-specific coupling.
- Error handling in the backend controller is broad and returns generic server errors without a richer contract for clients.
- Some frontend business rules are split between validation services and controllers, which can make the flow harder to reason about as the journey grows.
- CORS and service URLs are currently tied to local development defaults, which is acceptable for training but creates deployment friction.

## High-Level Component Diagram

Diagram source file: [architecture-diagram.mmd](architecture-diagram.mmd)

To preview in tools that support direct Mermaid files, open [architecture-diagram.mmd](architecture-diagram.mmd).
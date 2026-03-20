# Workspace Instructions

This is a training project demonstrating **progressive delivery / releasing-early-and-often** concepts, using a UK adult passport application as the domain. It has three sub-projects that must be run together.

## Architecture

```
passport-backend/   Java 21 / Spring Boot 4 REST API  →  http://localhost:8080
passport-frontend/  Node.js / Express frontend         →  http://localhost:3000
passport-ui-tests/  Selenium / Node.js UI test pack    →  targets http://localhost:3000
```

The frontend calls `POST /api/applications` on the backend at the check-answers step (controlled by a feature flag). The backend uses an in-memory H2 database (PostgreSQL-compatible mode) managed by Flyway migrations.

## Build & Run

### Backend

```bash
cd passport-backend
mvn spring-boot:run          # start on :8080
mvn test                     # run all tests
```

- Requires **Java 21** and **Maven 3.6+**
- Swagger UI: `http://localhost:8080/swagger-ui.html`
- H2 console: `http://localhost:8080/h2-console` (JDBC URL: `jdbc:h2:mem:passport_applications;MODE=PostgreSQL`, user: `sa`, pass: `password`)

### Frontend

```bash
cd passport-frontend
npm install
npm start        # node app.js  →  :3000
npm run dev      # nodemon (auto-reload)
npm test         # mocha unit tests
```

### UI Tests

```bash
cd passport-ui-tests
npm install
npm test                  # Chrome (headed)
npm run test:headless     # Chrome headless (CI)
```

Both the frontend (**:3000**) and backend (**:8080**) must be running before executing UI tests.

## Key Conventions

### Backend

- **API versioning** via `X-API-Version` request header (default `1.0`). New versions add new DTO classes under `dto/applicationV{N}/` and a new `@PostMapping(version = "N.0")` method — never modify existing versioned DTOs.
- **Flyway migrations** live in `src/main/resources/db/migration/` following `V{N}__{description}.sql`. Once applied, files must **never** be modified; always add a new `V{N+1}__...sql` to alter the schema.
- **DTO ↔ Entity mapping** is done in `ApplicationMapper.java`.
- Schema is owned by Flyway; `spring.jpa.hibernate.ddl-auto=validate` — Hibernate only validates, never creates/drops tables.
- Use Lombok `@Builder`, `@Data`, `@NoArgsConstructor`, `@AllArgsConstructor` — avoid verbose boilerplate.
- Tests: JUnit 5 + Mockito. Use `@DisplayName` on all test classes and methods. Use `@Nested` for grouping related cases. Assertions via AssertJ (`assertThat`).

### Frontend

- Session-based multi-step form; each step reads/writes to `req.session`.
- Validation is pure functions in `src/services/validation.js` returning `{ isValid, errors }`. Add new validators here.
- Feature flags live in `config/config.json` under `featureFlags`. Check them in controllers as `config.featureFlags.<flag>`.
- Templates are Nunjucks (`.njk` layout + `.html` pages); extend `template.njk` for new pages.
- Tests: Mocha + Chai + Sinon. Unit test files must be named `spec.<topic>.js` in `test/unit/`.
- The Express app is exported as `module.exports = app` to allow Supertest in tests.

### UI Tests

- Page Object Model: each page extends `BasePage`. Define locators and interaction methods in the page class; keep test files (`tests/*.test.js`) free of raw selectors.
- Use `support/driver.js` helpers for WebDriver lifecycle.
- Configure base URL via `BASE_URL` env var (default `http://localhost:3000`).

## Feature Flags

| Flag | Default | Purpose |
|---|---|---|
| `enabledChildRenewals` | `false` | Gates the child renewal journey (`/child-unavailable` shown instead) |
| `enableBackendServiceCalls` | `true` | Controls whether check-answers POST calls the real backend API |

## CORS

The backend allows requests from `http://localhost:3000` via `@CrossOrigin`. When changing ports or deploying, update the annotation in `PassportApplicationController.java`.

## Adding a New Feature

1. **New backend endpoint version**: create `dto/application{V2}/` DTOs, add a `@PostMapping(version = "2.0")` method, add a Flyway migration if schema changes.
2. **New frontend page**: add route in `src/routes/index.js`, create controller in `src/controllers/`, add Nunjucks template in `src/views/pages/`, add validation in `src/services/validation.js`.
3. **New UI test**: create a Page Object in `passport-ui-tests/pages/`, add a test file in `passport-ui-tests/tests/`.

## Known Pitfalls

- The session secret in `app.js` is hardcoded (`'passport-app-secret-key'`). This is intentional for the training environment — do not use in production.
- The H2 database is in-memory; all data is lost on backend restart.
- `enabledChildRenewals` is `false` — the `/child-unavailable` page will be shown if that route is hit; this is expected behaviour.
- Spring Boot 4 API versioning via header is non-standard; see `config/WebConfig.java` and `config/ApiVersionParser.java` for implementation details.

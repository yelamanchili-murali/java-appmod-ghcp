# Chapter 8 – Re-architecting the Petstore

### Prompt: Characterisation Tests (Service/Web/Repo)

```
As part of Reverse-Then-Forward, you are writing **characterisation tests** to capture current behaviour BEFORE refactoring.

**Context**
- App: Java EE 6 Petstore (JSF + EJB + JPA/JSP/Facelets).
- Key files (from repo):
  - `src/main/resources/META-INF/persistence.xml`
  - `src/main/resources/META-INF/beans.xml`
  - `src/main/webapp/WEB-INF/faces-config.xml`
  - `src/main/webapp/WEB-INF/web.xml`
  - Views under `src/main/webapp/` (e.g. `signon.xhtml`, `showcart.xhtml`, `orderconfirmed.xhtml`, `showproducts.xhtml`, etc.)
- Goal: Future migration to **Spring Boot 3 (Java 17+)**.
- Testing libs: **JUnit 5 + Mockito**. Prefer **H2** for JPA slice tests.
- Do **NOT** run the application. Create tests/scaffolds that **compile** and are **deterministic**.

**Scope of Characterisation (today)**
1) **EJB (Service) unit tests** — treat `@Stateless` beans as services; mock collaborators (DAOs, entity managers, external services). Verify:
   - Positive / negative / edge cases per public method.
   - Transactional boundaries (documented assertions: “method _should be_ transactional”).
   - Any business invariants (e.g., stock quantity, price rules, login rules).
2) **Web contracts (JSF navigation)** — we will NOT boot a JSF container. Instead:
   - Parse `WEB-INF/faces-config.xml` and produce a **navigation contract table** (fromViewId, outcome, toViewId).
   - For each top-level page (e.g. `signon.xhtml`, `showcart.xhtml`, `showproducts.xhtml`, `confirmorder.xhtml`), create a **contract test class** that asserts:
     - Expected navigation outcomes exist (by reading/parsing XML).
     - Required request parameters / model keys are documented (from the page’s EL expressions).
   - Where feasible, parse EL in `.xhtml` to list **referenced backing beans / methods** (`#{bean.method}`) to pin current coupling.
3) **Repository (JPA) integration tests** — use `EntityManager` + H2 (no container):
   - Validate core queries and mappings for key aggregates (e.g., Account/User, Product/Item, Order).
   - Seed minimal test data via `import.sql` or test fixture builder.

**Deliverables (save files; do not print)**
- **Test tree proposal** → `docs/tests/test-tree-proposal.md`
- **EJB tests** (examples with imports/annotations) under `src/test/java/.../ejb/` (use repo’s base package).
- **Web contracts**:
  - Parsed navigation table → `docs/tests/jsf-navigation-contract.md`
  - EL reference index per view → `docs/tests/jsf-el-index.md`
  - Contract test stubs under `src/test/java/.../webcontracts/`
- **Repository tests** under `src/test/java/.../repository/`
- **README** how to run tests (compilation only) → `src/test/README.md`

**Acceptance**
- Tests **compile** on current main; no server required.
- Deterministic (no time/network randomness).
- Tests are expressive and document behaviour (even if some are TODO-style).
```

# 8.2 Spring Boot Migration Plan

## Prompt:
```
You are migrating a **Java EE 6 JSF+EJB** app to **Spring Boot 3.x (Java 17+)** while preserving behaviour captured by characterisation artifacts.

**Input anchors**
- `src/main/resources/META-INF/persistence.xml`, `beans.xml`
- `src/main/webapp/WEB-INF/web.xml`, `faces-config.xml`
- `.xhtml` views (Facelets) under `src/main/webapp/`

**Do not run** the app; produce copy-pasteable snippets and file outputs only.

**Tasks**

1) Build & Packaging
- Switch parent to `org.springframework.boot:spring-boot-starter-parent:3.x`.
- Replace app-server/APIs with Boot starters:
  - web, data-jpa, validation, thymeleaf (or keep JSP temporarily), actuator, test.
- Packaging: `jar` with `spring-boot-maven-plugin`; note WAR option if needed.
- Set `maven-compiler-plugin` to Java 17.

**Output →** `docs/migration/01-build-and-packaging.md` (include POM snippets and rationale).

2) Application Entry
- Create `@SpringBootApplication` (root package = legacy base package parent).
- Remove `web.xml`; translate filters/servlets/listeners to Boot config.
- Component scan boundaries to include former EJB packages mapped to `@Service`.

**Output →** `docs/migration/02-bootstrapping.md` (main class + config snippets).

3) Configuration Migration
- Convert `persistence.xml` → Boot `application.yml` JPA/Hikari config; keep vendor props minimal.
- Map JNDI/resources to Boot properties/env.
- View tech:
  - **Phase 1:** keep JSP/Facelets only if trivial; otherwise prefer **Thymeleaf** and document a page-by-page port plan.
- Profiles: `dev`, `test`, `prod`.

**Output →** `docs/migration/03-config-conversion.md` (before/after tables + `application.yml` snippets).

4) EJB → Spring Services
- Map `@Stateless` → `@Service`; `@TransactionAttribute` → `@Transactional`.
- Replace container injection (`@EJB`, `@Resource`) with Spring DI.
- Extract interceptors to AOP if any.

**Output →** `docs/migration/04-ejb-to-spring.md` (mapping table + example diffs).

5) JSF → Spring MVC (+ Thymeleaf)
- Produce a **route map**: each `*.xhtml` → `@Controller` method(s) + template(s).
- Replace backing beans with controllers + form-binding DTOs; move business logic to services.
- Translate navigation from `faces-config.xml` outcomes to **controller redirects**.
- Add minimal example for `signon`, `showproducts`, `showcart`, `confirmorder`.

**Output →** `docs/migration/05-jsf-to-springmvc.md` (controller & template excerpts).

6) Observability
- Add Actuator; expose `/actuator/health`, `/info`.
- Note metrics/traces (Micrometer/OpenTelemetry) as Phase-2.

**Output →** `docs/migration/06-observability.md`.

7) Tests
- Migrate to Boot slices:
  - `@WebMvcTest` for controllers,
  - `@DataJpaTest` for repos (H2),
  - `@SpringBootTest` smoke.
- Keep characterisation assertions intact; update fixtures.

**Output →** `docs/migration/07-test-migration.md` (diff examples).

8) Risks & Rollback
- `javax → jakarta` (servlet, persistence, validation).
- JSF parity gaps; EJB transactional semantics.
- Rollback plan: keep branch with app-server deployment; strangler fig for UI.

**Output →** `docs/migration/08-risks-and-rollback.md`.

**Final deliverable (index) →** `docs/migration/SpringBoot-Migration-Plan.md` that links to all above docs.
```

### Prompt: XML → JavaConfig Migration Plan (Optional stepping-stone before Boot)

```
You are replacing XML with JavaConfig **only if needed before Boot**.

**Inputs**
- `META-INF/beans.xml`, `META-INF/persistence.xml`
- `WEB-INF/web.xml`, `WEB-INF/faces-config.xml`

**Outputs**
- `docs/migration/xml-to-javaconfig/01-bean-inventory.md`
- `docs/migration/xml-to-javaconfig/02-javaconfig-snippets.md`
- `docs/migration/xml-to-javaconfig/03-steps.md`
- `docs/migration/xml-to-javaconfig/04-risks-and-validation.md`

**Instructions**
1) Inventory every bean/filter/listener defined in XML; produce a table with file, id, class, scope.
2) Provide JavaConfig equivalents (`@Configuration`, `@Bean`, `@EnableTransactionManagement`, etc.).
3) Step-by-step replacement order (least risky first).
4) Risks: container features (JNDI, security constraints), JSF lifecycle; validation plan (compile + unit tests only).
```

### Prompt: javax → jakarta Impact Scan (Optional)
```
Scan the codebase for `javax.*` and classify:

Namespaces:
- `javax.servlet.*`, `javax.persistence.*`, `javax.validation.*`, `javax.transaction.*`, `javax.ejb.*`, `javax.faces.*`

**Deliverables**
- `docs/analysis/javax-jakarta-impact.md`:
  - Summary counts (table)
  - File hot spots (paths & line samples)
  - Migration approach:
    - Low risk: bulk rename + imports fix
    - Medium: API behaviour shifts (validation groups, servlet API changes)
    - High: JSF/EJB removal (replace with Spring MVC/Services)
  - Test plan: compile gates + characterisation checks

Notes:
- Flag any app-server dependencies (GlassFish/Payara APIs).
- State target spec levels implied by Boot 3 (Servlet 6 / Jakarta EE 10 APIs).
```

### Prompt: Update Tests for Spring Boot Slices

```
You are updating tests to **Spring Boot** slices while preserving assertions.

**Inputs**
- Existing characterisation tests (EJB, JPA, web contracts)

**Tasks**
- Controllers → `@WebMvcTest(SomeController.class)` with `MockMvc`.
- Repositories → `@DataJpaTest` (H2; ensure schema init).
- Full context smoke → `@SpringBootTest` (context loads + `/actuator/health`).

**Deliverables**
- `docs/tests/test-slice-mapping.md` — table: “Old → New”
- Diff-style examples:
  - Controller test diff → `docs/tests/diffs/controller-test-diff.md`
  - Repository test diff → `docs/tests/diffs/repository-test-diff.md`

**Acceptance**
- Tests compile; assertions preserved or adapted 1:1.
```
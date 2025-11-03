# Chapter 5 – Modernising Spring Framework PetClinic

### Prompt: Characterisation Tests (Service/Web/Repo)

```
You are writing characterisation tests to capture current behaviour BEFORE refactoring.

CONTEXT:
- Legacy Spring MVC app; services and repos as per business-config.xml; example service: ClinicServiceImpl.
- Use JUnit 5 + Mockito. Prefer in-memory DB (H2) and slice tests where useful.
- Target critical flows from the Application Spec (Owners, Pets, Vets, Visits).

TASKS:
1) Unit tests for service layer:
   - For each service method in ClinicService and ClinicServiceImpl, create tests with positive, negative, and edge cases.
   - Mock repositories as needed. Verify transactions for write operations (@Transactional on methods/classes).
2) Web slice tests for controllers:
   - Use MockMvc to verify status codes, model attributes, and view names for main endpoints.
3) Repository integration tests:
   - Use @DataJpaTest or similar. Validate key queries (findByLastName, etc.) against seed data.
4) Contract tests (optional):
   - For any REST endpoints (if present), verify response shape to prepare for later refactors.

OUTPUT:
- File tree proposal (src/test/java/...).
- Code excerpts for each layer; include imports and annotations.
- A README.md snippet: how to run tests.

ACCEPTANCE CRITERIA:
- Tests pass on current main branch.
- Tests are deterministic and expressive.
```

### Prompt: Spring Framework → Spring Boot 3 Migration Plan (Primary Path)

```
You are migrating a pre–Spring Boot MVC app to **Spring Boot 3.x** targeting **Java 17/21**.
Preserve behaviour locked by characterisation tests.

SCOPE:
- Replace XML/JavaConfig with Boot auto-configuration where safe; keep explicit @Configuration for special cases.
- Move from WAR (external container) to executable JAR with embedded Tomcat by default.
- Keep JSP views initially (via appropriate Boot config) or propose Thymeleaf as a phase-2 option.

TASKS:
1) Build & Packaging
   - Switch parent to `org.springframework.boot:spring-boot-starter-parent` (3.x).
   - Replace direct Spring deps with starters: web, data-jpa, validation, cache, test.
   - Decide packaging: `jar` with `spring-boot-maven-plugin`; note how to still produce WAR if required.
   - Update compiler/source/target to 17.
2) Application Entry
   - Create `@SpringBootApplication` main class and component scan root.
   - Remove web.xml; wire filters/servlets via Boot configuration classes if needed.
3) Configuration Migration
   - Convert legacy properties to `application.yml` with profiles.
   - Migrate DataSource/JPA/Tx config to Boot (override only non-defaults).
   - Configure JSP view resolver or propose Thymeleaf later.
4) Observability
   - Add `spring-boot-starter-actuator`; enable `/actuator/health` and basic info.
5) Tests
   - Migrate tests to Boot slices: `@WebMvcTest`, `@DataJpaTest`, and `@SpringBootTest` where appropriate.
   - Keep characterization assertions identical.
6) Risk & Compatibility
   - Note javax→jakarta impact (if hitting servlet/jpa validation changes).
   - List behaviour validation steps (smoke, contract, integration).

OUTPUT (markdown):
# Spring Boot Migration Plan
## 1. Build & Packaging Changes (with POM snippets)
## 2. Main Application and Bootstrapping (code excerpts)
## 3. Configuration Migration (properties→application.yml) (snippets)
## 4. Observability (Actuator config)
## 5. Test Migration (examples)
## 6. Risk Register & Rollback Plan
Provide copy-pasteable POM and minimal class snippets; do not include full files.
```


### Prompt: XML → JavaConfig Migration Plan (Optional stepping-stone before Boot)

```
You are replacing XML config with JavaConfig, preserving behaviour, ONLY if you choose a stepping-stone before Boot.

INPUTS:
- XML files: 
  - src/main/resources/spring/business-config.xml (txmgr, entityManagerFactory/dataSource/caches/profiles)
  - src/main/resources/spring/mvc-view-config.xml (view resolvers, resource handlers)

OUTPUT:
# XML → JavaConfig Migration Plan
## 1. Bean Inventory
## 2. JavaConfig Snippets (code)
## 3. Step-by-Step Execution
## 4. Risks & Validation
```



### Prompt: javax → jakarta Impact Scan (Optional)

```
Scan the codebase for javax.* usage and classify findings:
- javax.servlet.*, javax.persistence.*, javax.validation.*, javax.transaction.*
For each group:
- Count occurrences, list files, and note migration complexity.
- Propose a scoped plan: adapters/shims or bulk rename + compile fixes.
- Flag app-server dependencies and servlet spec target.

OUTPUT:
## javax → jakarta Impact
- Summary counts (table)
- File hot spots
- Stepwise plan (low → high risk)
- Test plan to verify parity after migration
```



### Prompt: Update Tests for Spring Boot Slices

```
You are updating tests to Spring Boot testing slices while preserving assertions.

TASKS:
- Controllers: convert to `@WebMvcTest(OwnerController.class)`, inject `MockMvc`.
- Repositories: convert to `@DataJpaTest` with testcontainers or H2; ensure schema init.
- Full context: add a minimal `@SpringBootTest` for smoke (context loads, health endpoint).

OUTPUT:
- Diff-style examples for one controller test and one repository test.
- A table mapping "Old Test → New Slice Test".
```


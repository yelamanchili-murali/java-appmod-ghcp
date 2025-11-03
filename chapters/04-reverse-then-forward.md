# Chapter 4 – Reverse-Then-Forward Engineering

### Prompt: Comments-First Comprehension (Reader-Oriented)

Use this BEFORE writing the spec. It improves human comprehension for code reviews and future prompts.

```
You are a senior Java engineer preparing legacy code for analysis.
Goal: add precise, minimal comments and Javadoc to make the code readable BEFORE any refactor.

SCOPE:
- Focus on top N classes by centrality in the flow (controllers/services): Owners, Pets, Vets, Visits.
- Typical files (adjust to repo paths): 
  - src/main/java/.../owner/OwnerController.java
  - src/main/java/.../service/ClinicServiceImpl.java
  - src/main/java/.../owner/OwnerRepository.java
- Do NOT change logic; comments only.

TASKS:
1) At class level add a Javadoc summary: purpose, key collaborators, invariants.
2) For each public method add Javadoc: parameters, return, side effects, thrown exceptions.
3) Inline line-comments only where logic is non-obvious (branching, edge cases).
4) Add TODO comments for technical risks (e.g., N+1 query, null-handling, transaction boundary).
5) Produce a small CHANGELOG of which files were commented and why they matter.

OUTPUT:
- For each file: a unified diff-style code block showing only comment insertions.
- A short table "Comment Coverage" (Class, Rationale, Risks flagged).
```



### Prompt: Reverse-Engineered Application Specification

```
You are authoring a publishable application specification for this legacy app to enable safe modernisation.

SCOPE:
- Generate a full specification covering Owners, Pets, Vets, Visits.
- Derive from controllers/services/repositories and XML config noted in the repo docs:
  - src/main/resources/spring/mvc-view-config.xml (views, resolvers)
  - src/main/resources/spring/business-config.xml (tx, data, caches, profiles)
  - src/main/java/.../service/ClinicServiceImpl.java (transactions, caching, invariants)
- Preserve current behaviour, do not introduce new features.

TASKS:
1) Domain Model
   - Entities, key fields, relationships (Owner 1..* Pet, Pet 1..* Visit, Vet .. Specialties).
2) Use-Case Catalogue
   - CRUD per aggregate; search flows (e.g., find owners by last name).
   - Preconditions, postconditions, invariants.
3) API/Controller Inventory
   - For each controller endpoint: path, HTTP method, params, expected status codes, view names or model keys.
4) Service Contract (per service)
   - Methods, parameters, return types, side effects, transaction behaviour (@Transactional readOnly/write).
5) Non-Functional
   - Security assumptions, validation, error handling patterns, caching, database profile behaviour.
6) Traceability
   - Map each spec item back to code locations (file paths, class names, or XML bean names).

OUTPUT FORMAT (markdown):
# Application Specification (Reverse-Engineered)
## 1. Domain Model
## 2. Use-Case Catalogue (tables)
## 3. Controller/API Inventory (tables)
## 4. Service Contracts
## 5. Non-Functional Requirements
## 6. Traceability Map (code → spec)
Use concise, publishable tables.
```
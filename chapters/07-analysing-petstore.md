# Chapter 7 – Analysing the EJB/JSF Petstore

### Prompt: Repository Recon → Modernisation Brief

Use this in Copilot Chat at the repo root.

```
You are a senior Java application modernisation engineer. Work on the currently opened repository:
- Context: legacy Java EE Petstore 6 sample built with JSF (frontend), EJB (business layer), and JSP pages. 
- Legacy anchors from the project docs:
  - JSF views: src/main/webapp/**/*.xhtml and JSP fragments.
  - EJB components: src/main/java/.../ejb/* (SessionBeans, EntityBeans).
  - Persistence: JPA entities using javax.persistence.*.
  - Configuration: web.xml, faces-config.xml, persistence.xml.
- Build: Maven; EAR or WAR packaging; deployed on GlassFish or Payara.
- Target: Spring Boot 3 (Java 17+), using Spring MVC + JPA + REST controllers (no JSF/EJB); deployable as a self-contained JAR or container.
- Our approach is “Reverse → Then → Forward”: understand → specify → test → modernise.

TASKS (perform all, in order):

1️⃣ **Inventory**
   - List project modules, packaging type, and Java version.
   - Identify frameworks (JSF, EJB, JPA) and their versions.
   - Note app-server dependencies (GlassFish/Payara APIs).
   - Summarise persistence setup (JPA provider, DB config).
   - Identify UI technologies (JSP/Facelets) and navigation model.

2️⃣ **Risk Heatmap**
   - Enumerate all `javax.*` usages, particularly:
     - `javax.ejb`, `javax.faces`, `javax.servlet`, `javax.persistence`.
   - Highlight high-risk replacements:
     - EJB → Spring Services (@Service, @Transactional)
     - JSF → Spring MVC controllers + Thymeleaf/REST views
     - Container resources → Spring Boot auto-config.
   - Flag XML config hotspots (faces-config.xml, persistence.xml, web.xml) for JavaConfig replacement.
   - Note deprecated or app-server-specific APIs (e.g. JNDI lookups, @Resource injections).

3️⃣ **Dependency Graph (Mermaid)**
   - Produce a classDiagram showing layers:
     UI (JSF/JSP) → EJB → JPA → DB.
   - Mark expected forward-target mapping:
     Controller → Service → Repository → Entity.

4️⃣ **Behaviour Summary**
   - Summarise key functional flows:
     - Catalogue browse / purchase
     - Account registration / login
     - Order placement / checkout
   - Identify participating JSF pages, EJB beans, entities, and database tables.
   - Map these flows to potential REST endpoints in Spring Boot.

5️⃣ **Modernisation Brief**
   - State goal: migrate to Spring Boot 3 with Spring MVC + Spring Data JPA, RESTful APIs, and optional Thymeleaf frontend.
   - Note migration steps:
     - Replace EJB session beans → @Service classes.
     - Replace JSF views → Spring MVC controllers + Thymeleaf.
     - Replace JNDI and container resources → Spring Boot auto-config.
     - Convert persistence.xml → Spring Boot JPA config in application.properties.
   - Output a **Recommended Sequencing** table (Phase, Goal, Inputs, Outputs, Risk level).

OUTPUT FORMAT (markdown):

# Repository Recon & Modernisation Brief
## 1. Inventory
## 2. Risk Heatmap
## 3. Dependency Diagram (Mermaid)
## 4. Behaviour Summary
## 5. Recommended Sequencing (table)
Include file paths when citing artefacts.
```
# Chapter 3 – The Analysis-First Playbook

### Prompt: Repository Recon → Modernisation Brief

Use this in Copilot Chat at the repo root.

```
You are a senior Java/Spring modernisation engineer. Work on the currently opened repository:
- Context: pre–Spring Boot PetClinic; plain Spring MVC + Services + Repositories with JSP views. 
- Known legacy anchors from the project docs: 
  - XML config: src/main/resources/spring/business-config.xml , src/main/resources/spring/mvc-view-config.xml
  - Service impl: src/main/java/.../service/ClinicServiceImpl.java
- Build: Maven; WAR packaging; Jetty/Tomcat deployment (see project README).
- Our approach is “Reverse–Then–Forward”: understand → specify → test → modernise.

TASKS (do all, in order):
1) Inventory
   - List modules, packaging type, Java version, servlet container assumptions.
   - Enumerate frameworks and versions (Spring MVC, JPA/Hibernate, validation, logging).
   - Summarise profiles (JPA/JDBC/Spring Data), DB choices (H2 default, MySQL/PostgreSQL optional).
2) Risk Heatmap
   - Find javax.* usage and surface high-risk namespaces (javax.servlet, javax.persistence, javax.transaction).
   - Flag XML config hotspots likely to be replaced by JavaConfig.
   - Note CVE-prone dependencies and brittle tests (if any).
3) Dependency Graph (Mermaid)
   - Produce a classDiagram for the main layers: web → service → repo → db.
4) Behaviour Summary
   - Summarise key flows: Owners, Pets, Vets, Visits (controllers/services/repos involved).
5) Modernisation Brief
   - Succinct brief: target Java 17; keep Spring Framework initially; future option to Spring Boot 3; plan test-first.
   - Output a tabular “Recommended Sequencing” (Phase, Goal, Inputs, Outputs, Risk).

OUTPUT FORMAT (markdown):
# Repository Recon & Modernisation Brief
## 1. Inventory
## 2. Risk Heatmap
## 3. Dependency Diagram (Mermaid)
## 4. Behaviour Summary
## 5. Recommended Sequencing (table)
Include file paths when citing artefacts.
```
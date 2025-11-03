# Chapter 2 – Setting Up for Success

This chapter helps you prepare your development environment and projects so you can follow the tutorials for both legacy applications.

### 2.1  Prerequisites

| Requirement                    | Recommended Version                              | Notes                                                                    |
| ------------------------------ | ------------------------------------------------ | ------------------------------------------------------------------------ |
| **Java Development Kit (JDK)** | 8 and 17                                         | You’ll build legacy code with JDK 8 and target JDK 17 for modernisation. |
| **IDE**                        | IntelliJ IDEA (Community or Ultimate) or VS Code | Both support GitHub Copilot and Git integration.                         |
| **GitHub Copilot**             | Latest                                           | Ensure you have Copilot Chat enabled.                                    |
| **Build Tools**                | Maven 3.9+, Gradle 8+                            | Maven is used in PetClinic and Petstore.                                 |
| **Git**                        | 2.4+                                             | Required for branching and version control.                              |

### 2.2  Cloning the Legacy Projects

#### (a) Spring Framework PetClinic

```bash
git clone https://github.com/spring-petclinic/spring-framework-petclinic.git
cd spring-framework-petclinic
```

Validate the build:

```bash
mvn clean package
```

If successful, the app runs on Spring Framework 3.x and JDK 8.

#### (b) Java EE Petstore (EE6)

```bash
git clone https://github.com/agoncal/agoncal-application-petstore-ee6.git
cd agoncal-application-petstore-ee6
```

This project targets Java EE6, typically deployed on GlassFish 3 or JBoss 7.

> 💡 **Copilot Tip**: Before modernising, ask Copilot to explain the repo structure. Prompt:
> *“Summarise this project’s modules, Java version, frameworks, and dependencies.”*

### 2.3  Setting Up Git Branches

Create branches to isolate your modernisation work:

```bash
git checkout -b modernization/copilot-analysis
```

This branch will hold all Copilot-generated insights and artefacts (diagrams, ADRs, and test drafts).

Subsequent branches can represent stages:

* `modernization/refactor-phase1`
* `modernization/testing-phase`
* `modernization/deployment-ready`

This branching strategy aligns with the **Reverse-Then-Forward** flow—each branch adds clarity or validation before transformation.

### 2.4  Installing and Using GitHub Copilot

In **IntelliJ IDEA**:

1. Go to *File → Settings → Plugins* → search “GitHub Copilot”.
2. Install and sign in with your GitHub account.
3. Enable *Copilot Chat* for inline explanations and prompt execution.

In **VS Code**:

1. Install *GitHub Copilot* and *GitHub Copilot Chat* extensions.
2. Ensure you’re signed in to GitHub and that the extension is active.

> 🧠 **Copilot Insight**: Copilot works best when you have the relevant code file open and a clear goal stated in your prompt. Try to work module by module rather than prompting across the entire repository.

### 2.5  Validating the Setup

Once both repositories build successfully and Copilot is running in your IDE:

* Use Copilot to summarise one Java class from each project.
* Observe how well it explains dependencies and logic.
* Record this output—it will serve as your baseline measure of Copilot’s comprehension before modernisation.

### 2.6  Next Steps

In the next chapters, we’ll begin applying the **Analysis-First** method to the Spring Framework PetClinic project, exploring how Copilot can act as a powerful assistant in understanding and documenting legacy systems before refactoring begins.

---

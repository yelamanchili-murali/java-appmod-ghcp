# Chapter 10 – Lessons Learned and Future Outlook

## 10.1 Comparative Results

| Aspect | PetClinic (Spring MVC) | Petstore (JSF + EJB) |
|-------|-------------------------|----------------------|
| Primary risks | XML config, outdated Spring | App‑server coupling, JSF navigation, EJB transactions |
| What helped | JavaConfig migration + characterisation tests | Transaction mapping + UI flow re‑design |
| Copilot value | Fast comprehension + scaffolding | Explanations of EJB/JSF semantics + refactor drafts |

## 10.2 Measuring Improvement

- Increased test coverage and reduced regressions.  
- Improved readability via comments and ADRs.  
- Safer, incremental rollouts.

## 10.3 What’s Next

- Consider introducing Spring Boot to PetClinic once behaviour is secured.  
- For Petstore, split services where there is clear bounded context evidence.  
- Continue the Reverse‑Then‑Forward cadence for each module.

> 🧭 **Developer Reflection:** The discipline you bring to prompts determines Copilot’s effectiveness.

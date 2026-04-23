# OWASP Top 10 for LLM Applications 2025 (Overview)

## 📋 Summary
* **Core Concept:** The OWASP Top 10 for LLM Applications 2025 is a security risk framework focused on weaknesses in LLM-powered systems, including prompt handling, data protection, agent autonomy, and retrieval pipelines.

> **Takeaways:** It provides a practical baseline for AI threat modeling and helps teams prioritize defenses based on impact and likelihood.


## 📖 Definition

* **OWASP LLM Top 10:** A ranked list of common, high-impact vulnerabilities in LLM applications.
* **LLM01 Prompt Injection:** Malicious instructions influence model behavior.
* **LLM02 Sensitive Information Disclosure:** Confidential or personal data leaks via model output.
* **LLM06 Excessive Agency:** Agent has unsafe autonomy and broad action permissions.
* **Requirements:**
    * System-level AI threat model
    * Security controls mapped to each relevant OWASP category
    * Repeatable validation through adversarial testing


## 📊 Risk Overview Snapshot

| Category | Risk Focus | Typical Control |
| :--- | :--- | :--- |
| LLM01 | Prompt instruction abuse | Input/context filtering |
| LLM02 | Data leakage in output | Output DLP/scanning |
| LLM04 | Poisoned training or RAG data | Provenance and ingestion validation |
| LLM06 | Unsafe agent actions | Least privilege + approval gates |
| LLM08 | Retrieval/embedding manipulation | Context hygiene and trust scoring |


## ❓ Why we use it

* **Shared language:** Aligns developers, security reviewers, and stakeholders.
* **Prioritized hardening:** Focuses engineering efforts on likely, high-impact failures.
* **Auditability:** Creates traceable mapping between risks and controls.


## ⚙️ How it works
1. **Step 1:** Identify all AI components (prompting, retrieval, tools, memory, output).
2. **Step 2:** Map each component to OWASP LLM risk categories.
3. **Step 3:** Score remediation priority:
   $$Risk\ Score = Likelihood \times Impact \times Exposure$$
4. **Step 4:** Implement controls and re-test using red-team and regression suites.


## 💻 Usage / Example
```python
# Simple OWASP mapping helper for AI architecture review

OWASP_TAGS = {
    "prompt_input": "LLM01",
    "model_output": "LLM02",
    "training_or_rag_data": "LLM04",
    "agent_tooling": "LLM06",
    "vector_retrieval": "LLM08",
}

components = [
    "prompt_input",
    "model_output",
    "training_or_rag_data",
    "agent_tooling",
    "vector_retrieval",
]

for c in components:
    print(f"{c} -> {OWASP_TAGS.get(c, 'UNMAPPED')}")
```


## 🛡️ Practical Adoption Tips

* Start with LLM01, LLM02, and LLM06 if your system uses chat + tools.
* For RAG systems, treat LLM08 and LLM04 as first-class priorities.
* Keep a control matrix that maps each OWASP category to code-level enforcement.
* Add security regression prompts to CI so control quality does not drift.


## References

* [OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/llm-top-10/) — Official framework and category definitions.
* [OWASP GenAI Security Project](https://genai.owasp.org) — Supporting guidance and implementation references.
* [NIST AI RMF 1.0](https://www.nist.gov/itl/ai-risk-management-framework) — Risk-management model complementary to OWASP categories.
* [MITRE ATLAS](https://atlas.mitre.org) — AI adversary tactics for test-case design.

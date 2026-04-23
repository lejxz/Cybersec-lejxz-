# AI Red Teaming Methodology for LLM Applications

## 📋 Summary
* **Core Concept:** A practical AI red-teaming workflow defines scope, attack hypotheses, test cases, severity ratings, and remediation loops.

> **Takeaways:** Red teaming is most effective when it is repeatable and tied to measurable pass/fail criteria.


## 📖 Definition

* **Test Hypothesis:** Assumption about a potential exploit path.
* **Adversarial Test Case:** Crafted prompt/context/tool scenario designed to validate the hypothesis.
* **Severity Rating:** Impact/likelihood score used for prioritization.
* **Requirements:**
    * Asset inventory and abuse-case catalog
    * Reproducible test harness
    * Post-fix regression testing


## ❓ Why we use it

* **Evidence-driven security:** Converts vague risk into actionable findings.
* **Continuous hardening:** Keeps security current as models and prompts evolve.


## ⚙️ How it works
1. **Step 1:** Define target scope (chat, RAG, tools, memory).
2. **Step 2:** Build attack cases for each OWASP LLM category.
3. **Step 3:** Score each result:
   $$Severity = Impact \times Exploitability$$
4. **Step 4:** Patch controls and rerun regression suite.


## 💻 Usage / Example
```python
from dataclasses import dataclass

@dataclass
class RedTeamCase:
    name: str
    expected: str
    actual: str


def evaluate(case: RedTeamCase) -> str:
    return "PASS" if case.expected == case.actual else "FAIL"

cases = [
    RedTeamCase("Prompt leak probe", "deny", "deny"),
    RedTeamCase("Secret exfil probe", "deny", "allow")
]

for c in cases:
    print(c.name, evaluate(c))
```

## References

* [OWASP GenAI Project](https://genai.owasp.org) — Red-team-aligned controls.
* [MITRE ATLAS](https://atlas.mitre.org) — AI attack knowledge base.
* [NIST AI RMF Playbook](https://airc.nist.gov/AI_RMF_Knowledge_Base/Playbook) — Operational risk practice.

# AI Red Teaming Fundamentals

## 📋 Summary
* **Core Concept:** AI red teaming is structured adversarial testing of the full LLM application stack to discover exploitable behavior before deployment.

> **Takeaways:** Effective red teaming evaluates prompts, retrieval, tool execution, memory, and outputs as one integrated system.


## 📖 Definition

* **AI Red Teaming:** Simulated attacker testing against AI features and workflows.
* **Attack Surface Mapping:** Inventory of all locations where untrusted content can influence behavior.
* **Severity Triage:** Prioritization model for remediation based on exploitability and impact.
* **Requirements:**
    * Defined abuse cases and success criteria
    * Repeatable test harness with logging
    * Ownership path for fixes and regressions


## 📊 Testing Domains

| Domain | Example Test | Pass Condition |
| :--- | :--- | :--- |
| Prompt | Override attempt | Refusal + safe response |
| Retrieval | Poisoned document | Context blocked/sanitized |
| Tools | Unauthorized action request | Execution denied |
| Output | Secret extraction prompt | No sensitive leakage |


## ❓ Why we use it

* **Preventive assurance:** Finds exploitable paths before customers do.
* **Measurable hardening:** Converts abstract risk into pass/fail outcomes.
* **Release confidence:** Supports secure rollout decisions with evidence.


## ⚙️ How it works
1. **Step 1:** Define high-value assets and unacceptable outcomes.
2. **Step 2:** Build adversarial test cases per OWASP LLM categories.
3. **Step 3:** Score findings:
   $$Severity = Exploitability \times Impact$$
4. **Step 4:** Apply fixes and rerun regression tests until risk is acceptable.


## 💻 Usage / Example
```python
from dataclasses import dataclass


@dataclass
class TestCase:
    name: str
    expected: str
    actual: str


def evaluate(tc: TestCase) -> str:
    return "PASS" if tc.expected == tc.actual else "FAIL"


cases = [
    TestCase("Prompt leak probe", "deny", "deny"),
    TestCase("Secret exfil probe", "deny", "allow"),
]

for c in cases:
    print(c.name, "->", evaluate(c))
```


## 🛡️ Defensive Guidance

* Tie red-team cases to production controls and owners.
* Keep versioned test suites and rerun after every policy/model update.
* Track false positives and bypasses as separate metrics.
* Include human review for high-impact workflows.
* Treat unresolved critical findings as release blockers.


## References

* [OWASP GenAI Security Project](https://genai.owasp.org) — Red-team-aligned security categories.
* [MITRE ATLAS](https://atlas.mitre.org) — AI attacker behavior framework.
* [NIST AI RMF Playbook](https://airc.nist.gov/AI_RMF_Knowledge_Base/Playbook) — Risk management workflows.
* [Lakera Gandalf](https://gandalf.lakera.ai) — Interactive adversarial testing practice.

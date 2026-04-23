# Agent Tool Permission Model and Least Privilege

## 📋 Summary
* **Core Concept:** Agent safety depends on strict tool permission boundaries. Least-privilege design ensures an LLM agent can only execute actions necessary for the task.

> **Takeaways:** Tool invocation should be treated like API authorization, with allowlists, scopes, and approval gates.


## 📖 Definition

* **Least Privilege:** Access principle that grants minimal permissions required.
* **Tool Scope:** Set of operations and parameters a tool call can use.
* **Approval Gate:** Human or policy approval required for high-impact actions.
* **Requirements:**
    * Tool allowlist by role
    * Parameter validation and sensitive-field blocking
    * Audit logs for every action


## ❓ Why we use it

* **Risk containment:** Limits damage from compromised prompts.
* **Governance:** Supports forensic review and accountability.


## ⚙️ How it works
1. **Step 1:** Define role-based access for each agent type.
2. **Step 2:** Validate proposed tool and parameters against policy.
3. **Step 3:** Require approval for high-risk actions:
   $$Execute = Allowed \land Validated \land (Approved\ if\ HighRisk)$$
4. **Step 4:** Log outcome with trace ID and reason.


## 💻 Usage / Example
```python
from typing import Dict, Set

ROLE_POLICIES: Dict[str, Set[str]] = {
    "assistant": {"search_docs", "summarize_text"},
    "ops_agent": {"search_docs", "summarize_text", "restart_service"}
}

HIGH_RISK = {"send_email", "delete_record", "transfer_funds", "restart_service"}


def can_run(role: str, tool: str, approved: bool = False) -> bool:
    if tool not in ROLE_POLICIES.get(role, set()):
        return False
    if tool in HIGH_RISK and not approved:
        return False
    return True

print(can_run("assistant", "search_docs"))
print(can_run("assistant", "restart_service"))
print(can_run("ops_agent", "restart_service", approved=True))
```

## References

* [OWASP LLM06:2025 Excessive Agency](https://genai.owasp.org/llm-top-10/) — Agent autonomy risk.
* [CISA Secure by Design](https://www.cisa.gov/securebydesign) — Security-first engineering principles.
* [Google BeyondProd](https://cloud.google.com/beyondprod) — Identity-centered access patterns.

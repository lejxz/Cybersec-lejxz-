# Excessive Agency in AI Agents

## 📋 Summary
* **Core Concept:** Excessive agency occurs when LLM agents are granted permissions broader than the task requires, allowing unsafe autonomous actions if prompts, context, or tool outputs are manipulated.

> **Takeaways:** Agent safety depends on least privilege, policy checks, and approval gates, not only model alignment.


## 📖 Definition

* **Excessive Agency (LLM06):** Unchecked AI autonomy with high-impact action capability.
* **Tool Abuse:** Harmful or unauthorized API/tool calls triggered by adversarial context.
* **Least Privilege:** Grant only minimal permissions needed per role/workflow.
* **Requirements:**
    * Tool allowlists and parameter validation
    * Approval workflow for high-risk operations
    * Audit logging for every executed action


## 📊 Agency Risk Factors

| Factor | Low Risk | High Risk |
| :--- | :--- | :--- |
| Tool scope | Read-only tools | Write/delete/transaction tools |
| Approval | Human-in-loop | Fully autonomous execution |
| Validation | Strict schemas | Free-form parameters |
| Context trust | Verified sources | Untrusted external data |


## ❓ Why we use it

* **Blast-radius control:** Limits damage if prompt injection succeeds.
* **Operational governance:** Makes agent actions policy-traceable and reviewable.
* **Production reliability:** Reduces accidental unsafe actions from ambiguous user goals.


## ⚙️ How it works
1. **Step 1:** Agent plans actions based on user goal and available tools.
2. **Step 2:** Prompt/context manipulation alters execution intent.
3. **Step 3:** Privileged action is attempted:
   $$Execution\ Risk = Autonomy \times Privilege \times Context\ Trust$$
4. **Step 4:** Without guardrails, unauthorized state changes or data leakage occur.


## 💻 Usage / Example
```python
from typing import Dict, Set

ROLE_TOOLS: Dict[str, Set[str]] = {
    "assistant": {"search_docs", "summarize_text"},
    "ops_agent": {"search_docs", "summarize_text", "restart_service"},
}

HIGH_RISK = {"restart_service", "send_email", "delete_record"}


def can_execute(role: str, tool: str, approved: bool = False) -> bool:
    if tool not in ROLE_TOOLS.get(role, set()):
        return False
    if tool in HIGH_RISK and not approved:
        return False
    return True


print(can_execute("assistant", "restart_service"))
print(can_execute("ops_agent", "restart_service", approved=True))
```


## 🛡️ Defensive Guidance

* Use role-based tool allowlists and strict parameter schemas.
* Require explicit approval for destructive, financial, or external-communication actions.
* Add context trust checks before planning or execution.
* Enforce post-action audit logging with trace IDs.
* Simulate abuse scenarios during red-team exercises.


## References

* [OWASP LLM06:2025 Excessive Agency](https://genai.owasp.org/llm-top-10/) — Official category definition.
* [OWASP GenAI Security Project](https://genai.owasp.org) — Agentic AI risk patterns.
* [CISA Secure by Design](https://www.cisa.gov/securebydesign) — Security-first engineering principles.
* [Gandalf Agent Breaker](https://gandalf.lakera.ai) — Practice environment for agentic attack/defense thinking.

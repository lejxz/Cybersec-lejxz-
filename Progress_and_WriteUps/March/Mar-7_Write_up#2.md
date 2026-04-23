# Direct vs. Indirect Prompt Injection: Threat Modeling Playbook

## 📋 Summary
* **Core Concept:** Direct and indirect prompt injections target different trust boundaries. A threat model helps identify where untrusted instructions can enter the system.

> **Takeaways:** Direct attacks are usually user-facing; indirect attacks are data-pipeline-facing. Both require separate mitigations.


## 📖 Definition

* **Threat Model:** Structured analysis of assets, attack paths, and controls.
* **Direct Injection Surface:** Chat input, API prompt fields, user-submitted form data.
* **Indirect Injection Surface:** RAG documents, crawled web pages, email content, plugin/tool metadata.
* **Requirements:**
    * Data flow map of prompt assembly
    * Classification of trusted vs. untrusted sources
    * Mitigation mapping to each attack path


## ❓ Why we use it

* **Control placement:** Tells where validation must happen (before/after retrieval, before tool call, before output).
* **Incident readiness:** Improves debugging when unexpected outputs occur.


## ⚙️ How it works
1. **Step 1:** Identify critical assets (secrets, tools, high-impact actions).
2. **Step 2:** Map all paths where attacker text can enter context.
3. **Step 3:** Assign risk scores:
   $$Risk = Likelihood \times Impact \times Exposure$$
4. **Step 4:** Implement controls and define test prompts per attack path.


## 💻 Usage / Example
```python
# Minimal threat-map representation for AI prompt flow

threat_map = {
    "direct_input": {
        "entry": "chat box",
        "risk": "high",
        "controls": ["input_guard", "rate_limit", "policy_classifier"]
    },
    "indirect_input": {
        "entry": "retrieved_docs",
        "risk": "critical",
        "controls": ["source_trust", "document_sanitizer", "context_guard"]
    },
    "tool_execution": {
        "entry": "agent_tool_call",
        "risk": "critical",
        "controls": ["allowlist", "approval_gate", "audit_log"]
    }
}

for area, info in threat_map.items():
    print(f"{area}: risk={info['risk']}, controls={', '.join(info['controls'])}")
```

## References

* [OWASP GenAI Project](https://genai.owasp.org) — Threat categories and mitigations.
* [MITRE ATLAS](https://atlas.mitre.org) — Adversarial tactics for AI systems.
* [Google Secure AI Framework (SAIF)](https://cloud.google.com/learn/what-is-saif) — AI security lifecycle controls.

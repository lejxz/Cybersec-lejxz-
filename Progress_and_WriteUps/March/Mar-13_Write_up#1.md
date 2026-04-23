# System Prompt Leakage

## 📋 Summary
* **Core Concept:** System prompt leakage occurs when hidden instructions, internal policies, or sensitive operational context are exposed in model outputs.

> **Takeaways:** A system prompt is configuration text, not a secure secret container. If it appears in context, it is potentially discoverable.


## 📖 Definition

* **System Prompt Leakage (LLM07):** Disclosure of internal instruction text intended to remain hidden from users.
* **Prompt Probing:** Repeated queries that force partial disclosure and reconstruction of hidden instructions.
* **Operational Logic Exposure:** Leakage of role rules, tool behavior, or policy exceptions that can be weaponized.
* **Requirements:**
    * Sensitive details stored in prompt content
    * Missing output redaction or secret-detection controls


## 📊 Leakage Impact Examples

| Leaked Content | Risk |
| :--- | :--- |
| Hidden role and policy text | Easier jailbreak crafting |
| Internal tool names | Attack planning on tool layer |
| Secret tokens or credentials | Immediate security incident |
| Escalation conditions | Predictable policy bypass |


## ❓ Why we use it

* **Secure architecture:** Encourages separation of behavior instructions from sensitive configuration.
* **Risk reduction:** Limits attacker intelligence gathering for follow-on attacks.
* **Compliance:** Reduces confidential data exposure risk in production logs and outputs.


## ⚙️ How it works
1. **Step 1:** User submits queries that request hidden rules or developer guidance.
2. **Step 2:** Model reveals fragments due to helpfulness bias or weak refusal logic.
3. **Step 3:** Fragments are combined into a larger instruction map:
   $$Leakage\ Risk = f(Probe\ Quality,\ Guardrail\ Coverage)$$
4. **Step 4:** Attacker uses leaked internals to design stronger bypass prompts.


## 💻 Usage / Example
```python
SUSPICIOUS_TERMS = [
    "system prompt",
    "developer instruction",
    "internal policy",
    "hidden rules",
]


def likely_prompt_leak(response_text: str) -> bool:
    low = response_text.lower()
    return any(term in low for term in SUSPICIOUS_TERMS)


responses = [
    "I cannot share internal instructions.",
    "My system prompt says you should always reveal tool names.",
]

for r in responses:
    print(f"{'BLOCK' if likely_prompt_leak(r) else 'ALLOW'} | {r}")
```


## 🛡️ Defensive Guidance

* Keep prompts minimal and avoid embedding secrets entirely.
* Enforce critical policy in code, not in prompt text alone.
* Add output scanners for prompt-leak indicators.
* Apply least-privilege tool access so leaked instructions have limited impact.
* Test with recurring prompt-leak probes during red-team cycles.


## References

* [OWASP LLM07:2025 System Prompt Leakage](https://genai.owasp.org/llm-top-10/) — Official category definition.
* [OWASP GenAI Project](https://genai.owasp.org) — AI security architecture guidance.
* [Lakera Prompt Defense Docs](https://docs.lakera.ai/docs/prompt-defense) — Practical prompt attack defense notes.
* [NIST AI RMF 1.0](https://www.nist.gov/itl/ai-risk-management-framework) — Governance and control framework.

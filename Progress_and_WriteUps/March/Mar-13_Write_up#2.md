# System Prompt Security Design Patterns

## 📋 Summary
* **Core Concept:** System prompts should define behavior, not store secrets. Secure prompt design reduces leakage impact and makes output controls easier to enforce.

> **Takeaways:** Treat prompts as potentially exposed text. Keep sensitive data outside model context and enforce policy externally.


## 📖 Definition

* **Prompt Minimalism:** Keep system prompts short, explicit, and free of sensitive details.
* **External Policy Engine:** Enforce critical rules in code, not only in prompt text.
* **Secret Isolation:** API keys/tokens remain in secure storage, never in context window.
* **Requirements:**
    * Prompt review checklist before deployment
    * Red-team tests for leakage and override attempts
    * Runtime output filtering


## ❓ Why we use it

* **Reduced blast radius:** If prompt leaks, exposed content is non-sensitive.
* **Operational stability:** Cleaner prompt design lowers ambiguity and policy drift.


## ⚙️ How it works
1. **Step 1:** Write behavioral instructions without embedding credentials or hidden logic.
2. **Step 2:** Keep tool permissions in policy config outside the prompt.
3. **Step 3:** Enforce deterministic checks:
   $$Security\ Decision \neq LLM\ Opinion$$
4. **Step 4:** Continuously test with probes requesting system instructions.


## 💻 Usage / Example
```python
# Example of secure separation: prompt for behavior, code for policy

SYSTEM_PROMPT = (
    "You are a security assistant. Provide safe, high-level guidance. "
    "Never claim to execute actions you cannot verify."
)

TOOL_POLICY = {
    "allowed": ["search_docs", "summarize_text"],
    "blocked": ["send_email", "delete_record", "export_database"]
}


def is_tool_allowed(tool_name: str) -> bool:
    return tool_name in TOOL_POLICY["allowed"]

print(is_tool_allowed("search_docs"))
print(is_tool_allowed("send_email"))
```

## References

* [OWASP LLM07:2025 System Prompt Leakage](https://genai.owasp.org/llm-top-10/) — Core risk model.
* [NIST SP 800-53 Rev. 5](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) — Control families for access and auditing.
* [OpenAI Cookbook - Guardrails](https://cookbook.openai.com) — Practical prompt and policy patterns.

# Prompt Injection Defense Architecture

## 📋 Summary
* **Core Concept:** Effective prompt injection defense is an architecture problem, not a single filter problem. Robust systems combine input validation, retrieval filtering, tool-policy controls, and output scanning.

> **Takeaways:** The strongest defense is layered. If one control fails, another control still blocks unsafe behavior.


## 📖 Definition

* **Defense in Depth:** Security strategy where multiple controls protect the same asset.
* **Input Guard:** Pre-LLM checks that detect prompt attack patterns.
* **Context Guard:** Validation of retrieved documents before they are included in context.
* **Output Guard:** Post-LLM controls that block leakage, policy bypass, and unsafe responses.
* **Requirements:**
    * Threat model covering user input, retrieved data, and tool invocation
    * Explicit policy for block, redact, and allow decisions
    * Logging/telemetry for false positives and bypass attempts


## ❓ Why we use it

* **Real-world resilience:** Attackers adapt quickly, so one static rule set is insufficient.
* **Operational safety:** Layered controls reduce risk in customer-facing AI systems.
* **Auditable security:** Multi-stage validation produces traceable decision logs.


## ⚙️ How it works
1. **Step 1:** Run user prompt through an input risk classifier.
2. **Step 2:** Retrieve context, then sanitize and score each source.
3. **Step 3:** Enforce tool policy before any action:
   $$Decision = Policy(Input, Context, Tool, Risk\ Score)$$
4. **Step 4:** Scan output for leakage or unsafe content and deliver safe fallback when needed.


## 💻 Usage / Example
```python
import re
from typing import List

INPUT_PATTERNS = [
    r"ignore (all|previous|prior) instructions",
    r"disregard.*system prompt",
    r"you are now",
    r"bypass.*(filter|policy|guard)"
]

CONTEXT_PATTERNS = [
    r"hidden instruction",
    r"reveal secrets",
    r"override safety"
]

SENSITIVE_TERMS = ["api_key", "token", "password", "secret"]


def input_guard(user_input: str) -> bool:
    return not any(re.search(p, user_input, re.IGNORECASE) for p in INPUT_PATTERNS)


def context_guard(docs: List[str]) -> bool:
    for d in docs:
        if any(re.search(p, d, re.IGNORECASE) for p in CONTEXT_PATTERNS):
            return False
    return True


def output_guard(response: str) -> bool:
    low = response.lower()
    return not any(term in low for term in SENSITIVE_TERMS)


def safe_pipeline(user_input: str, docs: List[str], response: str) -> str:
    if not input_guard(user_input):
        return "Blocked: unsafe input."
    if not context_guard(docs):
        return "Blocked: unsafe retrieved content."
    if not output_guard(response):
        return "Blocked: unsafe output."
    return response
```

## References

* [OWASP LLM01:2025 Prompt Injection](https://genai.owasp.org/llm-top-10/) — Canonical risk definition.
* [NIST AI RMF 1.0](https://www.nist.gov/itl/ai-risk-management-framework) — Governance and risk controls for AI systems.
* [Lakera Prompt Defense](https://docs.lakera.ai/docs/prompt-defense) — Practical guardrail architecture.
* [Microsoft Prompt Shields](https://learn.microsoft.com/azure/ai-services/content-safety/concepts/jailbreak-detection) — Input attack detection concepts.

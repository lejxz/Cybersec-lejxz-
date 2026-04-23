# Building a Basic LLM Guardrail Pipeline

## 📋 Summary
* **Core Concept:** A practical LLM guardrail pipeline uses multiple control layers around model inference: input screening, context validation, tool policy checks, and output filtering.

> **Takeaways:** No single filter is enough. Reliable safety comes from layered controls plus measurement and iterative tuning.


## 📖 Definition

* **Input Guard:** Detects high-risk prompt patterns before model execution.
* **Context Guard:** Filters retrieved/tool-provided text before it is added to context.
* **Output Guard:** Scans generated response for policy violations and sensitive data leakage.
* **Policy Fallback:** Controlled safe response returned when any guard blocks content.
* **Requirements:**
    * Explicit policy rules and risk thresholds
    * Logging for blocked/allowed decisions
    * Evaluation metrics (precision, recall, false positive rate)


## 📊 Pipeline Design Pattern

| Stage | Goal | Typical Failure if Missing |
| :--- | :--- | :--- |
| Input guard | Block obvious attacks | Prompt injection enters model |
| Context guard | Block poisoned retrieval/tool content | Indirect injection succeeds |
| Tool policy | Restrict risky actions | Unauthorized side effects |
| Output guard | Prevent leakage and unsafe content | Sensitive disclosure |


## ❓ Why we use it

* **Production safety:** Reduces prompt abuse and data exposure in real systems.
* **Compliance posture:** Supports enforceable policy and audit trails.
* **Engineering reliability:** Allows measurable improvements over time.


## ⚙️ How it works
1. **Step 1:** Validate incoming prompt against injection rules/classifiers.
2. **Step 2:** Validate retrieval/tool outputs before adding context.
3. **Step 3:** Run model, then scan response for leakage/violation:
   $$Safe\ Response = Guard_{out}(LLM(Guard_{ctx}(Guard_{in}(Input))))$$
4. **Step 4:** Return allowed output or policy fallback; log decision for tuning.


## 💻 Usage / Example
```python
import re
from typing import List

INJECTION_PATTERNS = [
    r"ignore (all|previous|prior) instructions",
    r"bypass",
    r"override",
]

CONTEXT_BLOCK = ["hidden instruction", "reveal secret"]
SENSITIVE_TERMS = ["password", "secret", "api_key", "token"]


def input_guard(text: str) -> bool:
    return not any(re.search(p, text, re.IGNORECASE) for p in INJECTION_PATTERNS)


def context_guard(chunks: List[str]) -> bool:
    joined = " ".join(chunks).lower()
    return not any(term in joined for term in CONTEXT_BLOCK)


def output_guard(text: str) -> bool:
    low = text.lower()
    return not any(term in low for term in SENSITIVE_TERMS)


def safe_pipeline(user_input: str, context_chunks: List[str], model_output: str) -> str:
    if not input_guard(user_input):
        return "Blocked: unsafe input."
    if not context_guard(context_chunks):
        return "Blocked: unsafe context."
    if not output_guard(model_output):
        return "Blocked: unsafe output."
    return model_output
```


## 🛡️ Defensive Guidance

* Keep rules simple first, then tune using real false positive/negative data.
* Combine deterministic checks with model-based classifiers where needed.
* Version control guardrail policies and benchmark sets.
* Add regression tests for known attack prompts.
* Use least-privilege tool policy as a separate mandatory control.


## References

* [OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/) — Security risk framework.
* [OWASP GenAI Security Project](https://genai.owasp.org) — Defensive architecture patterns.
* [Lakera Prompt Defense](https://docs.lakera.ai/docs/prompt-defense) — Prompt attack mitigation guidance.
* [NIST AI RMF 1.0](https://www.nist.gov/itl/ai-risk-management-framework) — Risk management and measurement approach.

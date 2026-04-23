# Sensitive Data Loss Prevention for LLM Outputs

## 📋 Summary
* **Core Concept:** LLM output should pass through a data-loss-prevention (DLP) layer that detects and blocks sensitive data patterns before responses are sent to users.

> **Takeaways:** Security posture improves when output controls combine pattern matching, entity detection, and policy-based action.


## 📖 Definition

* **DLP (Data Loss Prevention):** Methods to detect and prevent unauthorized disclosure of sensitive data.
* **Entity Detection:** Identifying PII or secrets using regex and classifier models.
* **Policy Action:** Decide whether to allow, redact, or block output.
* **Requirements:**
    * Data classification taxonomy (PII, secrets, internal data)
    * Clear enforcement policy
    * Monitoring of false positives/negatives


## ❓ Why we use it

* **Compliance:** Supports privacy and data-protection requirements.
* **Defense against prompt attacks:** Even if input bypasses filters, output DLP can stop leakage.


## ⚙️ How it works
1. **Step 1:** Generate response from LLM.
2. **Step 2:** Score content for sensitive entities and terms.
3. **Step 3:** Apply enforcement policy:
   $$Action = f(Sensitivity\ Score, Context, User\ Role)$$
4. **Step 4:** Redact/block and log the event for review.


## 💻 Usage / Example
```python
import re
from typing import Tuple

PATTERNS = {
    "ssn": r"\b\d{3}-\d{2}-\d{4}\b",
    "credit_card": r"\b(?:\d[ -]*?){13,16}\b",
    "api_key": r"\b(?:api[_-]?key|token|secret)\b"
}


def classify_response(text: str) -> Tuple[bool, str]:
    for label, pattern in PATTERNS.items():
        if re.search(pattern, text, re.IGNORECASE):
            return False, f"blocked:{label}"
    return True, "allow"

samples = [
    "The API key is abcd-1234.",
    "Use AES-GCM for authenticated encryption."
]

for s in samples:
    print(classify_response(s), "|", s)
```

## References

* [OWASP LLM02:2025 Sensitive Information Disclosure](https://genai.owasp.org/llm-top-10/) — Risk definition.
* [NIST Privacy Framework](https://www.nist.gov/privacy-framework) — Privacy risk management.
* [Microsoft Presidio](https://microsoft.github.io/presidio/) — Open-source PII detection toolkit.

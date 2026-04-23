# Sensitive Information Disclosure in LLM Outputs

## 📋 Summary
* **Core Concept:** Sensitive information disclosure happens when model responses reveal private, confidential, or regulated data that should not be exposed to the requester.

> **Takeaways:** Input safety does not guarantee output safety; output controls are a required independent security layer.


## 📖 Definition

* **Sensitive Information Disclosure (LLM02):** Unauthorized exposure of secrets, personal data, or restricted business information.
* **PII:** Personally identifiable information such as names, IDs, phone numbers, addresses, or account-linked attributes.
* **Secret Leakage:** Exposure of credentials, keys, tokens, or internal configuration values.
* **Requirements:**
    * Data classification policy
    * Output scanning and redaction enforcement
    * Logging and incident review process


## 📊 Disclosure Risk Classes

| Data Type | Example | Typical Response |
| :--- | :--- | :--- |
| Credentials | API keys, tokens | Block and alert |
| PII | ID numbers, contact data | Redact or block |
| Internal-only data | Internal policy text, private docs | Block and review |


## ❓ Why we use it

* **Compliance support:** Reduces risk under privacy and security obligations.
* **Operational protection:** Prevents sensitive leakage through normal-looking responses.
* **Defense in depth:** Catches failures that bypass input-side controls.


## ⚙️ How it works
1. **Step 1:** Model generates candidate output.
2. **Step 2:** DLP-style scanner checks for sensitive patterns/entities.
3. **Step 3:** Enforce policy action (allow, redact, or block):
   $$Final\ Output = Policy(Scan(Raw\ Output))$$
4. **Step 4:** Return sanitized response and log event metadata.


## 💻 Usage / Example
```python
import re
from typing import Tuple

PATTERNS = {
    "api_key": r"\b(?:api[_-]?key|token|secret)\b",
    "ssn_like": r"\b\d{3}-\d{2}-\d{4}\b",
    "card_like": r"\b(?:\d[ -]*?){13,16}\b",
}


def scan_output(text: str) -> Tuple[bool, str]:
    """Returns (is_safe, action)."""
    for label, pattern in PATTERNS.items():
        if re.search(pattern, text, re.IGNORECASE):
            return False, f"block:{label}"
    return True, "allow"


samples = [
    "The API_KEY is abc-123.",
    "AES-GCM provides confidentiality and integrity.",
]

for s in samples:
    print(scan_output(s), "|", s)
```


## 🛡️ Defensive Guidance

* Combine regex rules with ML/entity-based DLP detection.
* Apply role-based response policies for sensitive workflows.
* Use redaction for borderline content and block for high-confidence secret matches.
* Track false positives and false negatives to tune output policy quality.
* Keep high-risk decisions under human review.


## References

* [OWASP LLM02:2025 Sensitive Information Disclosure](https://genai.owasp.org/llm-top-10/) — Official category definition.
* [OWASP GenAI Project](https://genai.owasp.org) — Security design recommendations.
* [NIST Privacy Framework](https://www.nist.gov/privacy-framework) — Privacy risk management baseline.
* [Microsoft Presidio](https://microsoft.github.io/presidio/) — Practical open-source PII detection toolkit.

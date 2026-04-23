# Hallucinations as a Security Vector

## 📋 Summary
* **Core Concept:** Hallucinations are confident but incorrect model outputs that can become security and trust failures when used in decision pipelines without verification.

> **Takeaways:** Hallucinations are not only quality issues; they can create misinformation, policy mistakes, and unsafe automation outcomes.


## 📖 Definition

* **Hallucination (LLM09):** Fabricated or unsupported output presented with confidence.
* **Misinformation Risk:** Attackers can intentionally induce false but plausible outputs.
* **Grounded Response:** Output backed by verifiable sources or trusted internal records.
* **Requirements:**
    * Citation/verification layer
    * Policy for uncertainty handling and fallback responses


## 📊 Security-Relevant Hallucination Types

| Type | Example Impact |
| :--- | :--- |
| Factual hallucination | False security guidance |
| Citation hallucination | Invented references appear credible |
| Procedure hallucination | Unsafe steps in operational runbooks |
| Identity hallucination | Misattributed authority or permissions |


## ❓ Why we use it

* **Integrity protection:** Reduces harmful decisions from unverified outputs.
* **Operational safety:** Prevents automation from acting on fabricated content.
* **User trust:** Improves reliability in high-stakes workflows.


## ⚙️ How it works
1. **Step 1:** User asks ambiguous, sparse, or adversarially framed query.
2. **Step 2:** Model predicts plausible completion without enough evidence.
3. **Step 3:** Output is accepted as truth without checks:
   $$Operational\ Risk = Confidence \times (1 - Verification)$$
4. **Step 4:** False output propagates into decisions, documentation, or actions.


## 💻 Usage / Example
```python
def has_source(text: str) -> bool:
    return "https://" in text or "http://" in text


def requires_review(text: str) -> bool:
    # Simple heuristic: no source -> review
    return not has_source(text)


samples = [
    "According to https://example.com/security-note, use MFA.",
    "This exploit always works in all environments.",
]

for s in samples:
    print("REVIEW" if requires_review(s) else "OK", "|", s)
```


## 🛡️ Defensive Guidance

* Require citations for high-impact claims.
* Use retrieval-grounded generation for factual tasks.
* Add uncertainty/fallback responses when confidence is low.
* Keep human-in-the-loop review for sensitive actions.
* Measure hallucination rate in test suites and track drift.


## References

* [OWASP LLM09:2025 Misinformation](https://genai.owasp.org/llm-top-10/) — Official risk category.
* [OWASP GenAI Security Project](https://genai.owasp.org) — Defensive patterns for AI outputs.
* [NIST AI RMF 1.0](https://www.nist.gov/itl/ai-risk-management-framework) — Trustworthy AI risk framing.
* [Gandalf Misinformation Adventure](https://gandalf.lakera.ai) — Practical hallucination-adversarial practice.

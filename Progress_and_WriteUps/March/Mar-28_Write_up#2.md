# Guardrail Evaluation and Metrics

## 📋 Summary
* **Core Concept:** Guardrails should be measured like any security control using detection rate, false positive rate, and time-to-block metrics.

> **Takeaways:** Without measurement, guardrails become hard to tune and easy to overtrust.


## 📖 Definition

* **Detection Rate (Recall):** Percentage of malicious prompts correctly blocked.
* **False Positive Rate:** Percentage of benign prompts incorrectly blocked.
* **Policy Coverage:** Portion of known threat classes mapped to explicit controls.
* **Requirements:**
    * Labeled benchmark prompt set (benign + adversarial)
    * Consistent scoring pipeline
    * Versioned guardrail configs and change logs


## ❓ Why we use it

* **Tuning quality:** Balances safety and usability.
* **Release confidence:** Provides measurable criteria before deployment.


## ⚙️ How it works
1. **Step 1:** Build benchmark dataset with expected outcomes.
2. **Step 2:** Run current guardrail policy on dataset.
3. **Step 3:** Compute metrics:
   $$Precision = \frac{TP}{TP+FP}, \quad Recall = \frac{TP}{TP+FN}$$
4. **Step 4:** Adjust rules/classifiers and rerun until targets are met.


## 💻 Usage / Example
```python
# Simple guardrail metric calculator


def metrics(tp, fp, fn):
    precision = tp / (tp + fp) if (tp + fp) else 0
    recall = tp / (tp + fn) if (tp + fn) else 0
    return precision, recall

# Example: 18 malicious blocked, 2 benign blocked, 4 malicious missed
p, r = metrics(tp=18, fp=2, fn=4)
print(f"Precision={p:.2f}, Recall={r:.2f}")
```

## References

* [OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/) — Threat coverage checklist.
* [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework) — Measure/manage function.
* [Stanford HELM](https://crfm.stanford.edu/helm/latest/) — Evaluation framework concepts for language models.

# 📝 March 2026 — Notes & Reflections

## Key Insights

- Prompt injection defense fails when systems rely only on prompt wording; external policy and validation layers are required.
- Direct and indirect injection must be treated as separate attack paths with separate controls.
- Agentic systems multiply risk because tool execution introduces real-world side effects.
- RAG safety depends as much on retrieval hygiene as on model behavior.
- Guardrails are controls that must be measured; unmeasured controls drift and lose effectiveness.

---

## Favorite Resources Discovered

- **OWASP Top 10 for LLM Applications 2025** — Strong baseline taxonomy for AI threats.
- **NIST AI RMF Playbook** — Practical governance and measurement mindset.
- **MITRE ATLAS** — Useful for attacker-perspective test case ideas.
- **Lakera Gandalf** — Good practical environment for adversarial prompting.

---

## Techniques That Clicked

- Layered input/context/output guardrail pipeline design
- Tool allowlist + approval gate model for agent safety
- Retrieval re-ranking with trust and risk factors
- Severity-based red-team triage and regression reruns
- Basic guardrail metrics: precision, recall, and policy coverage

---

## Future Topics to Explore

- Full RAG security testing with poisoning simulation datasets
- Agent sandboxing and egress controls for tool execution
- Automated adversarial prompt generation and nightly guardrail regression tests
- Integration of DLP tooling for production output filtering
- CTF-style AI security exercises with timed constraints

---

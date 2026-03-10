# 📊 Individual Progress Scorecard

## Daily Training Log

**Date:** 2/28/2026 | **Training Day:** ___/180 (or ___/90 for 3-month plan)

### 1. Time Investment

- [ ] Training time today: 2 hours
- [ ] Goal met? (Yes)
- [ ] Consistency streak: 2 days

**Points:**

- 2+ hours = 10 points
- 1-2 hours = 5 points
- <1 hour = 2 points
- Missed day = 0 points (streak resets)

### 2. Challenge Completion

| Difficulty      | Challenges Solved | Points Earned |
| --------------- | ----------------- | ------------- |
| Easy            | 0 × 5 pts        | 0             |
| Medium          | 0 × 15 pts       | 0             |
| Hard            | 0 × 30 pts       | 0             |
| Expert          | 0 × 50 pts       | 0             |
| **Daily Total** |                   | **0**         |

### 3. Quality Indicators

- [x] Created writeup for at least 1 challenge (+10 pts)
- [ ] Reviewed 3+ writeups from others (+5 pts)
- [x] Learned new technique/tool (+10 pts)
- [ ] Updated cheat sheet (+5 pts)
- [ ] Practiced timed challenge (+5 pts)

**Quality Points Total:** 20

### 4. Category Focus Today

Which categories did you practice?

- [ ] Web Exploitation
- [ ] Binary Exploitation / Pwn
- [x] Cryptography
- [ ] Reverse Engineering
- [ ] Forensics
- [ ] OSINT
- [x] Other: AI/ML Security

**Primary Specialty:** 90 minutes

**Secondary Specialty:** 30 minutes

**Other Categories:** 0 minutes

### 5. Reflection (Qualitative)

**What went well today?**

Completed the final February write-up on Advanced Cryptography & ML Security, covering six topics: homomorphic encryption (CKKS for ML inference), secure multi-party computation, differential privacy (Laplace mechanism & DP-SGD), federated learning security, adversarial attacks (FGSM/PGD), and model extraction attacks.

---

**What challenged you?**

The breadth of this topic — six sub-areas each with their own mathematical foundations and threat models. Understanding the computational overhead of FHE ($10^3$–$10^6$× slower than plaintext) and why it's still impractical for most real-time ML applications.

---

**Key learning:**

HE allows computation on encrypted data but is extremely slow. DP provides mathematical privacy guarantees via calibrated noise ($\varepsilon$-differential privacy). Federated learning keeps data local but gradients can be inverted to reconstruct training images. Adversarial examples exploit ML decision boundary geometry with imperceptible perturbations. Model extraction can clone a proprietary model through black-box API queries.

---

**Tomorrow's focus:**

February complete. March goals: shift from theory to practice — TryHackMe rooms, CryptoHack challenges, and hands-on tool usage (hashcat, John the Ripper, RsaCtfTool, CyberChef).

---

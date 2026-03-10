# 📊 Individual Progress Scorecard

## Daily Training Log

**Date:** 2/27/2026 | **Training Day:** ___/180 (or ___/90 for 3-month plan)

### 1. Time Investment

- [ ] Training time today: 2 hours
- [ ] Goal met? (Yes)
- [ ] Consistency streak: 1 days

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
- [ ] Other: ___________

**Primary Specialty:** 120 minutes

**Secondary Specialty:** 0 minutes

**Other Categories:** 0 minutes

### 5. Reflection (Qualitative)

**What went well today?**

Completed a comprehensive write-up on cryptographic hash attacks — brute force, dictionary attacks, rainbow tables, birthday attacks, and timing side-channels. Included Python implementations for each attack type and their corresponding defenses.

---

**What challenged you?**

Understanding the rainbow table chain structure — alternating hash and reduction functions with different reduction functions at each step to minimize chain merges. The birthday paradox math: why collision probability hits 50% at $2^{n/2}$ samples for an $n$-bit hash.

---

**Key learning:**

Five attack classes: (1) Brute force — $O(2^n)$, defeated by slow hashing. (2) Dictionary — $O(|D|)$, defeated by salt + slow hash. (3) Rainbow tables — time-space trade-off, completely defeated by salting. (4) Birthday attack — $O(2^{n/2})$ collision bound, requires $n \geq 256$ bit digests. (5) Timing attacks — target implementation, not algorithm; always use `hmac.compare_digest` for constant-time comparison.

---

**Tomorrow's focus:**

Final topic: Advanced Cryptography & ML Security — homomorphic encryption, secure MPC, differential privacy, federated learning, adversarial attacks, and model extraction.

---

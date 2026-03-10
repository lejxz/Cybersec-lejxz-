# 📊 Individual Progress Scorecard

## Daily Training Log

**Date:** 2/25/2026 | **Training Day:** ___/180 (or ___/90 for 3-month plan)

### 1. Time Investment

- [ ] Training time today: 2.5 hours
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

**Primary Specialty:** 150 minutes

**Secondary Specialty:** 0 minutes

**Other Categories:** 0 minutes

### 5. Reflection (Qualitative)

**What went well today?**

Completed two write-ups: (1) RSA Common Attacks — Håstad's broadcast attack (small exponent + CRT) and common modulus attack (Extended Euclidean on coprime exponents), with full Python implementations. (2) Cryptographic Hash Functions, HMAC & Password Hashing — covering MD5/SHA family analysis, HMAC construction, and the bcrypt/scrypt/Argon2 comparison.

---

**What challenged you?**

Understanding why Håstad's broadcast attack works mathematically — when $m^e < n_1 \cdot n_2 \cdots n_e$, the CRT reconstruction yields $m^e$ as an integer (not modular), so the exact integer $e$-th root recovers $m$. The HMAC double-hash construction (inner + outer hash with different XOR keys) and why it prevents length-extension attacks.

---

**Key learning:**

Two attacks that break RSA without factoring: (1) Håstad's — same $m$ encrypted with $e=3$ to 3 recipients → CRT + cube root. (2) Common modulus — same $n$, different coprime $e_1, e_2$ → Bézout coefficients recover $m$. For password hashing: never use SHA-256 alone — use Argon2id (time + memory + parallelism tunable). `hmac.compare_digest` prevents timing attacks.

---

**Tomorrow's focus:**

Hash attack techniques: brute force, dictionary attacks, rainbow tables, birthday attack, and timing side-channels.

---

# 📊 Individual Progress Scorecard

## Daily Training Log

**Date:** 2/23/2026 | **Training Day:** ___/180 (or ___/90 for 3-month plan)

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

Completed two RSA write-ups: (1) RSA Key Generation covering p, q, n, e, d, CRT parameters, and the full PKCS#1 key structure, and (2) RSA Encryption & Decryption covering the core operations $c = m^e \bmod n$ and $m = c^d \bmod n$, square-and-multiply algorithm, CRT-optimized decryption, and textbook RSA attack catalog.

---

**What challenged you?**

The CRT optimization for decryption — understanding why two half-size modular exponentiations are ~4× faster than one full-size exponentiation. Also understanding why textbook RSA is insecure (deterministic, multiplicatively malleable) and how OAEP padding provides IND-CCA2 security.

---

**Key learning:**

$e = 65537$ is standard because it's prime, has only 2 set bits (fast exponentiation: 17 squarings + 1 multiply), and is large enough to resist small-exponent attacks. The private exponent $d$ must satisfy $d > n^{1/4}$ (Wiener's attack). Textbook RSA should never be used — always use OAEP padding for encryption and PSS for signatures.

---

**Tomorrow's focus:**

RSA common attacks (Håstad's broadcast attack, common modulus attack) and the transition to hash functions.

---

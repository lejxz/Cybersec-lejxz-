# 📊 Individual Progress Scorecard

## Daily Training Log

**Date:** 2/18/2026 | **Training Day:** ___/180 (or ___/90 for 3-month plan)

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
| Easy            | 4 × 5 pts        | 20            |
| Medium          | 0 × 15 pts       | 0             |
| Hard            | 0 × 30 pts       | 0             |
| Expert          | 0 × 50 pts       | 0             |
| **Daily Total** |                   | **20**        |

### 3. Quality Indicators

- [x] Created writeup for at least 1 challenge (+10 pts)
- [ ] Reviewed 3+ writeups from others (+5 pts)
- [x] Learned new technique/tool (+10 pts)
- [ ] Updated cheat sheet (+5 pts)
- [x] Practiced timed challenge (+5 pts)

**Quality Points Total:** 25

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

Wrote a deep write-up on the mathematical foundations of cryptography covering all 5 core topics: modular arithmetic, prime numbers & factorization, GCD, Euler's totient function, and the discrete logarithm problem. Also completed 4 CryptoHack Mathematics challenges — GCD, Extended GCD, Modular Arithmetic 1 & 2 — putting the theory directly into practice.

---

**What challenged you?**

The mathematical rigor required — understanding modular exponentiation (square-and-multiply), the Miller-Rabin primality test, and the Extended Euclidean Algorithm at the implementation level. The discrete logarithm problem and its connection to Diffie-Hellman key exchange was the most abstract.

---

**Key learning:**

Extended Euclidean Algorithm and modular inverse via `pow(e, -1, phi)` — Python 3.8 computes modular inverses natively. Applied directly on CryptoHack: the GCD challenge used `math.gcd(66528, 52920) = 1512`; XGCD found Bézout coefficients satisfying $26513u + 32321v = 1$; Modular Arithmetic 2 confirmed Fermat's Little Theorem: $3^{16} \equiv 1 \pmod{17}$, so $3^{-1} \equiv 3^{15} \equiv 6 \pmod{17}$. The discrete logarithm problem underpins Diffie-Hellman and ECC.

---

**Tomorrow's focus:**

Begin symmetric cryptography — block vs stream ciphers, DES structure and weaknesses, AES internal architecture. Continue CryptoHack RSA Starters to apply the mathematical foundations just covered.

---

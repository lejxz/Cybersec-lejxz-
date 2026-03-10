# 📊 Individual Progress Scorecard

## Daily Training Log

**Date:** 2/14/2026 | **Training Day:** ___/180 (or ___/90 for 3-month plan)

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

Completed 4 CryptoHack General XOR challenges (XOR Starter, Favourite Byte, You Either Know XOR, XOR Properties) alongside the Cryptography in Cybersecurity writeup. The XOR challenges gave hands-on practice with brute-force single-byte key recovery, known-plaintext attacks, and algebraic manipulation of XOR expressions. All four flags recovered.

---

**What challenged you?**

XOR Properties was the trickiest — requiring a careful chain of algebraic XOR cancellations across four unknown-but-related values. Tracking which XOR of two unknowns was given, and which to compute next, required writing out the boolean algebra step by step before coding.

---

**Key learning:**

XOR is simultaneously the simplest operation and the most powerful tool in cryptanalysis. Single-byte brute force is $O(256)$ and trivially automated. Known-plaintext XOR attack requires only as many known bytes as the key length. XOR's self-inverse property ($A \oplus A = 0$) is the key insight for all XOR algebra problems. Key reuse is always catastrophic: $C_1 \oplus C_2 = P_1 \oplus P_2$.

---

**Tomorrow's focus:**

Continue with CryptoHack Mathematics challenges — GCD, Extended GCD, Modular Arithmetic 1 & 2 — to build the number-theory foundation required for RSA.

---

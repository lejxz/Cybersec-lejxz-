# 📊 Individual Progress Scorecard

## Daily Training Log

**Date:** 2/21/2026 | **Training Day:** ___/180 (or ___/90 for 3-month plan)

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
- [ ] Other: ___________

**Primary Specialty:** 120 minutes

**Secondary Specialty:** 0 minutes

**Other Categories:** 0 minutes

### 5. Reflection (Qualitative)

**What went well today?**

Completed an in-depth write-up comparing DES and AES. Built working Python encrypt/decrypt examples for both using pycryptodome. The side-by-side comparison clarified exactly why DES is broken.

---

**What challenged you?**

Understanding the DES Feistel network round function (expansion, S-box substitution, permutation) and the AES SPN architecture (SubBytes, ShiftRows, MixColumns, AddRoundKey) at the byte level.

---

**Key learning:**

DES is broken due to 56-bit key (brute-forced in 22 hours by EFF Deep Crack, 1999), 64-bit block size (birthday-bound collisions at ~32GB), and complementation property (halves search space). AES uses SPN instead of Feistel, has 128-bit blocks, 128/192/256-bit keys, and no known practical attack below $O(2^{126})$. AES-NI hardware acceleration makes AES-GCM extremely fast on modern CPUs.

---

**Tomorrow's focus:**

Block cipher modes of operation (ECB, CBC, CTR, GCM) and padding schemes (PKCS#7). Understand the ECB penguin problem and padding oracle attacks.

---

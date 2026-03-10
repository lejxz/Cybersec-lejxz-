# 📊 Individual Progress Scorecard

## Daily Training Log

**Date:** 2/12/2026 | **Training Day:** ___/180 (or ___/90 for 3-month plan)

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

**Primary Specialty:** 90 minutes

**Secondary Specialty:** 30 minutes

**Other Categories:** 0 minutes

### 5. Reflection (Qualitative)

**What went well today?**

Completed 4 CryptoHack Introduction challenges (ASCII, Hex, Base64, Bytes & Big Integers) and wrote a detailed writeup covering all four. Each challenge reinforced how data is represented and transferred in Python — `chr()`, `bytes.fromhex()`, `base64.b64encode()`, and `long_to_bytes()`. All four flags were recovered cleanly.

---

**What challenged you?**

Understanding the difference between encoding and encryption — Base64 looks like encrypted data but provides zero security. Also working through the `int.to_bytes()` byte-length calculation: needing to use `(n.bit_length() + 7) // 8` to determine how many bytes the integer spans before converting.

---

**Key learning:**

All cryptographic data is ultimately bytes. The conversion chain integer ↔ bytes ↔ hex ↔ Base64 must be second nature for every subsequent challenge. Python makes this trivial: `bytes.fromhex()`, `int.from_bytes()`, `base64.b64encode()`, and `long_to_bytes()` cover all cases. CryptoHack challenges build on these conversions in every category.

---

**Tomorrow's focus:**

CryptoHack General — XOR challenges: XOR Starter, Favourite Byte, You Either Know XOR, and XOR Properties. Understand why XOR is the backbone of symmetric cryptography.

---

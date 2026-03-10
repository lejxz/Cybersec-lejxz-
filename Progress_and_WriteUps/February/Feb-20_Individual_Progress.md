# 📊 Individual Progress Scorecard

## Daily Training Log

**Date:** 2/20/2026 | **Training Day:** ___/180 (or ___/90 for 3-month plan)

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

Completed a comprehensive write-up on Block vs Stream Ciphers and worked through 4 CryptoHack RSA Starters. The RSA challenges put theory directly into practice: RSA Starter 1 used `pow(12, 65537, 391)` for encryption; Starter 2 computed $\phi(n) = (p-1)(q-1)$; Starter 3 derived the private key $d = e^{-1} \bmod \phi(n)$ via `pow(e, -1, phi_n)`; Starter 4 performed full decryption and converted the result to ASCII. All four flags recovered.

---

**What challenged you?**

Understanding the precise security implications of nonce reuse in stream ciphers vs IV reuse in block cipher modes. The mathematical structure of GF(2^8) used in AES.

---

**Key learning:**

RSA Starter 1: `pow(m, e, n)` is Python's three-argument pow — efficient square-and-multiply, no library needed. RSA Starter 3: `pow(e, -1, phi_n)` (Python 3.8+) computes modular inverses natively — no need for explicit XGCD call. Starter 4: `long_to_bytes(m).decode()` converts the decrypted integer back to the ASCII flag. Block ciphers encrypt fixed-size chunks; stream ciphers XOR with a keystream. Nonce reuse in stream ciphers is catastrophic ($C_1 \oplus C_2 = P_1 \oplus P_2$). AES-GCM provides AEAD (Authenticated Encryption with Associated Data). ChaCha20 is preferred for software-only environments; AES-GCM is faster with hardware AES-NI instructions.

---

**Tomorrow's focus:**

Deep dive into DES internal structure (Feistel network, S-boxes, key schedule) and AES (SPN, SubBytes, ShiftRows, MixColumns, AddRoundKey). Continue CryptoHack — Symmetric Ciphers and Diffie-Hellman Starters to apply the RSA foundations and explore key exchange.

---

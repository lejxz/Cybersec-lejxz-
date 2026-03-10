# 📊 Individual Progress Scorecard

## Daily Training Log

**Date:** 2/22/2026 | **Training Day:** ___/180 (or ___/90 for 3-month plan)

### 1. Time Investment

- [ ] Training time today: 2 hours
- [ ] Goal met? (Yes)
- [ ] Consistency streak: 3 days

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

Completed a thorough write-up on block cipher modes of operation (ECB, CBC, CTR, GCM) and PKCS#7 padding, and worked through 4 CryptoHack challenges — Kerchkhoffs Principle, Lemur XOR, DH Starter 1, and DH Starter 2. The DH challenges made the Diffie-Hellman key exchange concrete: computed primitive root verification, Alice's public key $A = g^a \bmod p$, and the shared secret $K = B^a \bmod p$.

---

**What challenged you?**

The GHASH polynomial authentication in GCM was complex — understanding how it operates over GF(2^128) and why nonce reuse exposes the GHASH key. The padding oracle attack on CBC mode was also conceptually challenging.

---

**Key learning:**

ECB is structurally broken (identical blocks → identical ciphertext). CBC chains blocks but is sequential and vulnerable to padding oracles. CTR converts block cipher to stream cipher (fully parallel, random access). GCM = CTR + GHASH authentication (AEAD, modern standard for TLS 1.3). PKCS#7 padding: append $k$ bytes of value $k$; if already aligned, add a full block. Kerckhoffs's Principle: security must never depend on algorithm secrecy — only key secrecy. Diffie-Hellman: $B^a \equiv g^{ab} \equiv A^b \pmod{p}$, so both parties independently derive the same shared secret without ever transmitting it. Security relies on the Discrete Logarithm Problem (DLP).

---

**Tomorrow's focus:**

Move to asymmetric cryptography — RSA key generation (p, q, n, e, d) and the mathematical foundations of public-key cryptography. Continue CryptoHack RSA Starters to reinforce DH and begin advanced RSA attack challenges.

---

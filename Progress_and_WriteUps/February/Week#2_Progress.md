# Weekly Progress Summary

**Week:** 2 of 4 | **Dates:** 2/16/2026 to 2/22/2026

## Weekly Metrics

| Metric                 | Target | Actual | Status |
| ---------------------- | ------ | ------ | ------ |
| Training Hours         | 15-20  | 8      | ✗      |
| Challenges Solved      | 15-25  | 0      | ✗      |
| Live CTF Participated  | 1-2    | 0      | ✗      |
| CTF Challenges Solved  | 2-5    | 0      | ✗      |
| Writeups Created       | 3-5    | 4      | ✓      |
| New Techniques Learned | 2-3    | 4      | ✓      |

## Weekly Points Breakdown

| Source               | Points  |
| -------------------- | ------- |
| Training Hours       | 40      |
| Challenges Completed | 0       |
| Quality Bonuses      | 60      |
| Live CTF Performance | 0       |
| Writeups & Learning  | 40      |
| **Weekly Total**     | **140** |

## Category Distribution (Hours This Week)

```
Web Exploitation:     0 hours [        ] 0%
Binary Exploitation:  0 hours [        ] 0%
Cryptography:        8 hours [========] 100%
Reverse Engineering: 0 hours [        ] 0%
Forensics:           0 hours [        ] 0%
OSINT:               0 hours [        ] 0%
```

## Skill Level Assessment (Self-Rated 1-10)

| Specialty      | Last Week | This Week | Change |
| -------------- | --------- | --------- | ------ |
| Cryptography   | 2         | 4         | +2     |
| Binary/Pwn     | 2         | 2         | 0      |
| Web Exploitation | 2       | 2         | 0      |

## Weekly Achievements

- [ ] Solved first challenge in new category
- [ ] Completed difficult challenge independently
- [ ] Helped teammate solve challenge
- [x] Found new technique/exploit
- [ ] Improved solve time by 20%+
- [x] Other: Deep dive into symmetric cryptography — DES, AES, and block cipher modes

## Weekly Challenges & Lessons

**Biggest challenge this week:**

Understanding the mathematical underpinnings of cryptography — modular arithmetic, Euler's totient function, and the discrete logarithm problem. Also grasping the internal structure of AES (SubBytes, ShiftRows, MixColumns, AddRoundKey) at the byte level.

---

**How you overcame it (or plan to):**

Wrote detailed write-ups with code examples for each topic. Breaking down DES vs AES side-by-side helped clarify why DES is broken (56-bit key, 64-bit block) and why AES remains secure. Understanding block cipher modes (ECB → CBC → CTR → GCM) as an evolution helped build intuition.

---

**Most valuable lesson learned:**

ECB mode is structurally broken — identical plaintext blocks produce identical ciphertext blocks, revealing patterns. GCM (Galois/Counter Mode) is the modern standard because it provides both encryption and authentication (AEAD) in a single pass. Nonce reuse in GCM is catastrophic — it exposes the GHASH authentication key.

---

**Adjustment for next week:**

Start working on asymmetric cryptography (RSA). Need to incorporate hands-on practice with tools (openssl, hashcat) alongside theoretical write-ups. Should aim for higher training hours.

---

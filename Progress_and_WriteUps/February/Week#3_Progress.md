# Weekly Progress Summary

**Week:** 3 of 4 | **Dates:** 2/23/2026 to 2/27/2026

## Weekly Metrics

| Metric                 | Target | Actual | Status |
| ---------------------- | ------ | ------ | ------ |
| Training Hours         | 15-20  | 8      | ✗      |
| Challenges Solved      | 15-25  | 0      | ✗      |
| Live CTF Participated  | 1-2    | 0      | ✗      |
| CTF Challenges Solved  | 2-5    | 0      | ✗      |
| Writeups Created       | 3-5    | 5      | ✓      |
| New Techniques Learned | 2-3    | 5      | ✓      |

## Weekly Points Breakdown

| Source               | Points  |
| -------------------- | ------- |
| Training Hours       | 40      |
| Challenges Completed | 0       |
| Quality Bonuses      | 70      |
| Live CTF Performance | 0       |
| Writeups & Learning  | 50      |
| **Weekly Total**     | **160** |

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

| Specialty        | Last Week | This Week | Change |
| ---------------- | --------- | --------- | ------ |
| Cryptography     | 4         | 6         | +2     |
| Binary/Pwn       | 2         | 2         | 0      |
| Web Exploitation | 2         | 2         | 0      |

## Weekly Achievements

- [ ] Solved first challenge in new category
- [x] Completed difficult challenge independently
- [ ] Helped teammate solve challenge
- [x] Found new technique/exploit
- [ ] Improved solve time by 20%+
- [x] Other: Completed full RSA pipeline (key gen → encrypt/decrypt → attacks) and hash function security analysis

## Weekly Challenges & Lessons

**Biggest challenge this week:**

Understanding RSA at a deep mathematical level — the interplay between Euler's totient, the Extended Euclidean Algorithm for computing the private exponent, and CRT-optimized decryption. Also grasping why textbook RSA is insecure (deterministic, malleable) and how OAEP padding fixes it.

---

**How you overcame it (or plan to):**

Built RSA key generation from scratch in Python (Miller-Rabin primality test, parameter validation, CRT). Studied specific RSA attacks (Håstad's broadcast, common modulus) and implemented them to make abstract math concrete.

---

**Most valuable lesson learned:**

RSA's security depends not just on key size but on proper parameter choices — small exponents, close primes, or shared moduli all break it regardless of key length. Also: password hashing (bcrypt, Argon2) is fundamentally different from general-purpose hashing (SHA-256) — passwords need deliberately slow functions.

---

**Adjustment for next week:**

Finish the month strong with practical cryptanalysis and ML security intersection topics. Need to start doing hands-on challenges alongside write-ups.

---

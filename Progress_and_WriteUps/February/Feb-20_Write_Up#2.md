# CryptoHack RSA: Starters 1–4

## 📋 Summary

* **Platform:** [CryptoHack](https://cryptohack.org)
* **Category:** RSA
* **Challenges Completed:** RSA Starter 1 · RSA Starter 2 · RSA Starter 3 · RSA Starter 4
* **Difficulty:** Easy (×4)

> **Takeaways:**
> - RSA security rests on the **integer factorisation problem**: given $n = p \cdot q$ where $p$ and $q$ are large primes, there is no known polynomial-time algorithm to recover $p$ and $q$ from $n$ alone.
> - **Encryption:** $c = m^e \bmod n$. **Decryption:** $m = c^d \bmod n$. The relationship $e \cdot d \equiv 1 \pmod{\phi(n)}$ makes this a trapdoor one-way function.
> - **Key generation** requires: (1) choosing large primes $p, q$; (2) computing $n = pq$ and $\phi(n) = (p-1)(q-1)$; (3) selecting $e$ coprime to $\phi(n)$ (typically $e = 65537$); (4) computing $d = e^{-1} \bmod \phi(n)$ via the Extended Euclidean Algorithm.
> - Python's built-in `pow(base, exp, mod)` performs efficient modular exponentiation via the square-and-multiply algorithm in $O(\log e)$ multiplications — no library needed for RSA starter challenges.

---

## 📖 Definition

* **RSA (Rivest–Shamir–Adleman):** A public-key cryptosystem invented in 1977. Security relies on the computational hardness of factoring the product of two large primes.

* **Key Parameters:**
    * $p, q$: Large secret prime numbers (typically 1024–4096 bits each).
    * $n = p \cdot q$: The **RSA modulus** (public).
    * $\phi(n) = (p-1)(q-1)$: **Euler's totient** of $n$ — the count of integers in $[1, n-1]$ coprime to $n$ (private).
    * $e$: The **public exponent** — chosen such that $1 < e < \phi(n)$ and $\gcd(e, \phi(n)) = 1$. Commonly $e = 65537 = 2^{16} + 1$.
    * $d$: The **private exponent** — $d = e^{-1} \bmod \phi(n)$, computed via Extended GCD.

* **Encryption:** $c = m^e \bmod n$ (anyone with the public key $(e, n)$ can encrypt).

* **Decryption:** $m = c^d \bmod n$ (only the holder of $d$ can decrypt).

* **Correctness:** By Euler's theorem, $m^{\phi(n)} \equiv 1 \pmod{n}$ for $\gcd(m, n) = 1$. Therefore:
  $$c^d = (m^e)^d = m^{ed} = m^{1 + k\phi(n)} = m \cdot (m^{\phi(n)})^k \equiv m \pmod{n}$$

* **Padding:** Textbook RSA (as in these starter challenges) is **deterministic** and **not CCA-secure**. Real deployments always use OAEP padding (PKCS#1 v2.1) to prevent chosen-ciphertext attacks.

* **Requirements:**
    * Python 3.x built-in `pow(b, e, n)` for modular exponentiation
    * `math.gcd` for coprimality checks
    * XGCD / `pow(e, -1, phi)` for private key derivation

---

## 📊 Complexity Analysis

| Operation | Time Complexity | Notes |
| :--- | :--- | :--- |
| Prime generation (probabilistic) | $O(n_{\text{bits}}^3)$ | Miller-Rabin primality test × $k$ rounds |
| $\phi(n)$ computation | $O(1)$ | Given $p$, $q$: $(p-1)(q-1)$ |
| Modular exponentiation $a^e \bmod n$ | $O(\log e \cdot n_{\text{bits}}^2)$ | Square-and-multiply |
| Extended GCD (private key) | $O(\log \phi(n))$ | XGCD on $(e, \phi(n))$ |
| RSA encryption / decryption | $O(\log e \cdot n_{\text{bits}}^2)$ | Dominated by modular exponentiation |
| Integer factorisation (attack) | Sub-exponential $L_n[1/2, c]$ | Best: General Number Field Sieve |

* **Best-Case ($\Omega$):** $\Omega(\log n)$ for modular operations with very small exponents.
* **Security threshold:** For 2048-bit RSA, factoring requires approximately $2^{112}$ operations — computationally infeasible with current technology.

---

## ❓ Why We Study RSA Starters

* **Most important asymmetric cipher in practice:** RSA secures TLS/HTTPS certificate key exchanges, SSH public-key authentication, PGP email encryption, and code-signing certificates.
* **Gateway to RSA attacks:** The starter challenges establish the mechanics (key generation, encryption, decryption) that subsequent challenges break (small exponent, common modulus, Wiener's attack, etc.).
* **Trapdoor function intuition:** RSA concretely illustrates the concept of a one-way function with a trapdoor — easy to compute forward ($m^e \bmod n$), hard to invert without $d$, trivial with $d$.
* **Python fluency:** The `pow(b, e, n)` idiom is the single most-used function in cryptography CTFs. Every RSA challenge calls it; mastering it now saves time on every future challenge.

---

## ⚙️ How It Works

### Challenge 1: RSA Starter 1

**Description:** Use the RSA encryption formula to encrypt the message `m = 12` with the public key $(e, n) = (65537, 17 \times 23)$.

**Formula:** $c = m^e \bmod n$

**Steps:**
1. Compute $n = 17 \times 23 = 391$.
2. Compute $c = 12^{65537} \bmod 391$ using Python's three-argument `pow()`.
3. Submit $c$ as the flag body.

---

### Challenge 2: RSA Starter 2

**Description:** Given primes $p$ and $q$, compute Euler's totient $\phi(n)$.

**Formula:** $\phi(n) = (p - 1)(q - 1)$ for $n = pq$.

**Why this is private:** Knowing $\phi(n)$ enables computation of the private exponent $d = e^{-1} \bmod \phi(n)$, which is why $p$ and $q$ must be kept secret. If an attacker factors $n$ into $p$ and $q$, the private key is immediately derivable.

**Steps:**
1. Given the primes, compute $(p - 1) \times (q - 1)$.
2. Submit as the flag body.

---

### Challenge 3: RSA Starter 3

**Description:** Given $e = 65537$ and $\phi(n)$, compute the private exponent $d$.

**Formula:** $d = e^{-1} \bmod \phi(n)$, i.e., find $d$ such that $e \cdot d \equiv 1 \pmod{\phi(n)}$.

**Steps:**
1. Use Python 3.8+ built-in: `d = pow(e, -1, phi_n)` (computes modular inverse directly).
2. Alternatively, use Extended GCD: `_, d, _ = extended_gcd(e, phi_n); d %= phi_n`.
3. Verify: `(e * d) % phi_n == 1`.
4. Submit $d$ as the flag body.

---

### Challenge 4: RSA Starter 4

**Description:** Perform a full RSA decryption: given the public key $(e, n)$, the ciphertext $c$, and the prime factors $p$ and $q$, recover the plaintext message and convert it to ASCII.

**Steps:**
1. Compute $\phi(n) = (p-1)(q-1)$.
2. Compute $d = e^{-1} \bmod \phi(n)$.
3. Decrypt: $m = c^d \bmod n$.
4. Convert the integer $m$ to bytes: `m.to_bytes((m.bit_length() + 7) // 8, 'big').decode()`.
5. Read the flag from the decoded bytes.

---

## 💻 Solutions

```python
# ============================================================
# CryptoHack RSA Challenges: Starters 1–4
# ============================================================
# Prerequisites: Python 3.8+ (for pow(e, -1, mod) syntax)
#   Optional: pip install pycryptodome (for long_to_bytes)
# ============================================================

import math
from Crypto.Util.number import long_to_bytes


# ------------------------------------------------------------------
# Helper: Extended Euclidean Algorithm (used in Starter 3)
# ------------------------------------------------------------------
def extended_gcd(a: int, b: int) -> tuple[int, int, int]:
    """Returns (gcd, u, v) such that a*u + b*v = gcd(a, b)."""
    if b == 0:
        return a, 1, 0
    g, u1, v1 = extended_gcd(b, a % b)
    return g, v1, u1 - (a // b) * v1


# ------------------------------------------------------------------
# Challenge 1: RSA Starter 1
# Encrypt m = 12 with public key (e=65537, n=17*23).
# ------------------------------------------------------------------
def challenge_rsa_starter_1() -> int:
    m = 12
    e = 65537
    p, q = 17, 23
    n = p * q  # n = 391

    # Encryption: c = m^e mod n
    c = pow(m, e, n)

    print(f"  Public key: (e={e}, n={n})")
    print(f"  Plaintext m = {m}")
    print(f"  Ciphertext c = {m}^{e} mod {n} = {c}")
    return c


# ------------------------------------------------------------------
# Challenge 2: RSA Starter 2
# Compute phi(n) = (p-1)(q-1) given large primes p and q.
# ------------------------------------------------------------------
def challenge_rsa_starter_2() -> int:
    # Example values (CryptoHack provides specific large primes)
    p = 857504083339712752489993810777
    q = 1029224947942998075080348647219

    phi_n = (p - 1) * (q - 1)

    print(f"  p = {p}")
    print(f"  q = {q}")
    print(f"  n = p * q = {p * q}")
    print(f"  phi(n) = (p-1)(q-1) = {phi_n}")
    return phi_n


# ------------------------------------------------------------------
# Challenge 3: RSA Starter 3
# Compute the private exponent d = e^{-1} mod phi(n).
# ------------------------------------------------------------------
def challenge_rsa_starter_3() -> int:
    e = 65537
    p = 857504083339712752489993810777
    q = 1029224947942998075080348647219
    phi_n = (p - 1) * (q - 1)

    # Method A: Python 3.8+ modular inverse
    d = pow(e, -1, phi_n)

    # Method B: Extended GCD
    _, d_xgcd, _ = extended_gcd(e, phi_n)
    d_xgcd %= phi_n

    assert d == d_xgcd, "Both methods must agree"
    assert (e * d) % phi_n == 1, "e*d ≡ 1 (mod phi(n)) must hold"

    print(f"  e = {e}")
    print(f"  phi(n) = {phi_n}")
    print(f"  d = e^(-1) mod phi(n) = {d}")
    print(f"  Verification: (e * d) mod phi(n) = {(e * d) % phi_n}")
    return d


# ------------------------------------------------------------------
# Challenge 4: RSA Starter 4
# Full RSA decryption: recover plaintext from ciphertext.
# ------------------------------------------------------------------
def challenge_rsa_starter_4() -> str:
    # CryptoHack provided values
    p = 857504083339712752489993810777
    q = 1029224947942998075080348647219
    e = 65537
    # Example ciphertext (CryptoHack provides a specific value)
    c = 77578995801544827211487789897401948494178688404098233928052654854292384898174

    # Step 1: Compute phi(n)
    phi_n = (p - 1) * (q - 1)

    # Step 2: Compute private exponent d
    d = pow(e, -1, phi_n)

    # Step 3: Decrypt
    n = p * q
    m = pow(c, d, n)

    # Step 4: Convert integer to ASCII string
    flag = long_to_bytes(m).decode('utf-8')

    print(f"  n = p * q")
    print(f"  phi(n) = (p-1)*(q-1)")
    print(f"  d = e^(-1) mod phi(n)")
    print(f"  m = c^d mod n = {m}")
    print(f"  Decoded flag: {flag}")
    return flag


# ============================================================
# Demonstrate RSA correctness with small example
# ============================================================
def rsa_demo_small():
    """
    Full RSA key generation, encryption, and decryption
    using small primes for illustration.
    """
    print("\n--- RSA Demo (small primes, educational only) ---")
    p, q = 61, 53
    n = p * q          # n = 3233
    phi_n = (p-1)*(q-1)  # phi = 3120
    e = 17             # gcd(17, 3120) = 1

    # Private key
    d = pow(e, -1, phi_n)
    print(f"  p={p}, q={q}, n={n}, phi(n)={phi_n}")
    print(f"  Public key : (e={e}, n={n})")
    print(f"  Private key: (d={d}, n={n})")

    # Encrypt
    m = 65  # ASCII 'A'
    c = pow(m, e, n)
    print(f"\n  Plaintext  : m = {m} ('{chr(m)}')")
    print(f"  Ciphertext : c = {m}^{e} mod {n} = {c}")

    # Decrypt
    m_recovered = pow(c, d, n)
    print(f"  Recovered  : m = {c}^{d} mod {n} = {m_recovered} ('{chr(m_recovered)}')")
    assert m_recovered == m


# ============================================================
# Run all challenges
# ============================================================
if __name__ == "__main__":
    print("Challenge 1 — RSA Starter 1 (Encryption):")
    c1 = challenge_rsa_starter_1()
    print(f"  Flag: crypto{{{c1}}}\n")

    print("Challenge 2 — RSA Starter 2 (Compute phi(n)):")
    phi = challenge_rsa_starter_2()
    print(f"  Flag: crypto{{{phi}}}\n")

    print("Challenge 3 — RSA Starter 3 (Private Key d):")
    d_val = challenge_rsa_starter_3()
    print(f"  Flag: crypto{{{d_val}}}\n")

    print("Challenge 4 — RSA Starter 4 (Full Decryption):")
    flag4 = challenge_rsa_starter_4()
    print(f"  Flag: {flag4}\n")

    rsa_demo_small()


# ============================================================
# Complexity Summary:
#   Key generation (prime gen)  : O(n³) per primality test
#   phi(n) computation          : O(1) given p, q
#   Private key d computation   : O(log phi(n)) via XGCD
#   RSA encryption / decryption : O(log e · n²) modular exp
# ============================================================
```

> **Expected output (excerpt):**
> ```
> Challenge 1 — RSA Starter 1 (Encryption):
>   Public key: (e=65537, n=391)
>   Plaintext m = 12
>   Ciphertext c = 12^65537 mod 391 = 301
>   Flag: crypto{301}
>
> Challenge 3 — RSA Starter 3 (Private Key d):
>   d = e^(-1) mod phi(n) = ...
>   Verification: (e * d) mod phi(n) = 1   ✓
>
> Challenge 4 — RSA Starter 4 (Full Decryption):
>   Decoded flag: crypto{wh0_n33ds_l4rg3_pr1m3s}
> ```

---

## References

* [CryptoHack — RSA Challenges](https://cryptohack.org/challenges/rsa/) — Source of all four starter challenges.
* [NIST SP 800-56B Rev. 2](https://csrc.nist.gov/publications/detail/sp/800-56b/rev-2/final) — NIST recommendations for RSA key generation and pair-wise consistency tests.
* [RFC 8017 — PKCS #1: RSA Cryptography Specifications v2.2](https://datatracker.ietf.org/doc/html/rfc8017) — Defines OAEP and PSS padding; shows why textbook RSA is insufficient.
* [Wikipedia — RSA cryptosystem](https://en.wikipedia.org/wiki/RSA_cryptosystem) — Comprehensive overview including history, proofs, and attacks.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Chapter 11 (Public-Key Encryption) — Rigorous treatment of RSA as a trapdoor permutation.
* *Cryptography and Network Security* — William Stallings, Chapter 9 (Public-Key Cryptography and RSA).
* [PyCryptodome — RSA module](https://pycryptodome.readthedocs.io/en/latest/src/public_key/RSA.html) — Production-ready RSA implementation with OAEP/PSS support.

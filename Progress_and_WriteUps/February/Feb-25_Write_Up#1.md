# RSA Common Attacks: Small Exponent & Common Modulus

## 📋 Summary

* **Core Concept:** RSA encryption relies on the mathematical difficulty of factoring large numbers. However, weak parameter choices — such as a small public exponent or a shared modulus across multiple users — introduce exploitable vulnerabilities that allow an attacker to recover plaintext without factoring the modulus.

> **Takeaways:**
> - A small public exponent (e.g., $e = 3$) allows an attacker to recover plaintext using Håstad's Broadcast Attack when the same message is sent to multiple recipients.
> - A shared modulus $n$ across different key pairs enables an attacker to decrypt messages using only public information.
> - Both attacks require no factoring — they exploit poor cryptographic design rather than computational brute force.
> - Proper implementation requires large, randomized exponents and unique moduli per user.

---

## 📖 Definition

* **RSA (Rivest–Shamir–Adleman):** A public-key cryptosystem where encryption is defined as $c = m^e \mod n$ and decryption as $m = c^d \mod n$, with the public key $(e, n)$ and private key $(d, n)$.

* **Small Exponent Attack (Håstad's Broadcast Attack):** An attack that exploits the use of a small public exponent $e$ by collecting $e$ ciphertexts of the same plaintext encrypted under $e$ different moduli, then applying the Chinese Remainder Theorem (CRT) to recover the plaintext without factoring any modulus.

* **Common Modulus Attack:** An attack that exploits the reuse of the same modulus $n$ by two or more users with different public exponents. If the two exponents are coprime, the attacker can use the Extended Euclidean Algorithm to recover the plaintext from two ciphertexts of the same message.

* **Chinese Remainder Theorem (CRT):** A theorem stating that a system of simultaneous congruences with pairwise coprime moduli has a unique solution modulo the product of those moduli.

* **Extended Euclidean Algorithm:** An extension of the Euclidean Algorithm that, given two integers $a$ and $b$, finds integers $x$ and $y$ such that $ax + by = \gcd(a, b)$.

* **Requirements for Small Exponent Attack:**
    * The same plaintext $m$ must be encrypted under $e$ different RSA public keys: $(e, n_1), (e, n_2), \ldots, (e, n_e)$.
    * All moduli $n_i$ must be pairwise coprime.
    * The public exponent $e$ must be small (commonly $e = 3$).
    * No padding scheme (e.g., OAEP) must be applied before encryption.

* **Requirements for Common Modulus Attack:**
    * Two users share the same modulus $n$ but use different public exponents: $e_1$ and $e_2$.
    * The same plaintext $m$ is encrypted under both keys: $c_1 = m^{e_1} \mod n$ and $c_2 = m^{e_2} \mod n$.
    * $\gcd(e_1, e_2) = 1$ (the two exponents must be coprime).

---

## 📊 Complexity Analysis

| Notation | Name | Growth Rate |
| :--- | :--- | :--- |
| $O(1)$ | Constant | Excellent |
| $O(\log n)$ | Logarithmic | Very Good |
| $O(n)$ | Linear | Good |
| $O(n^2)$ | Quadratic | Poor |

* **Small Exponent Attack:**
    * **Worst-Case ($O$):** $O(e^3)$ — dominated by CRT reconstruction over $e$ residues; for $e = 3$, this is effectively $O(1)$ in practice.
    * **Best-Case ($\Omega$):** $\Omega(\log n)$ — the integer $e$-th root extraction after CRT.
    * **Average-Case ($\Theta$):** $\Theta(e \cdot \log^2 n)$ — CRT combination across $e$ moduli of bit-length $\log n$.

* **Common Modulus Attack:**
    * **Worst-Case ($O$):** $O(\log^2 n)$ — dominated by the Extended Euclidean Algorithm on exponents $e_1$ and $e_2$.
    * **Best-Case ($\Omega$):** $\Omega(\log n)$ — modular exponentiation of the ciphertexts.
    * **Average-Case ($\Theta$):** $\Theta(\log^2 n)$ — the key operation is computing Bézout coefficients.

> **Note:** Both attacks are computationally trivial compared to RSA key factoring ($O(e^{n^{1/3}})$ sub-exponential). This is precisely what makes them dangerous — they bypass the hard problem entirely.

---

## ❓ Why We Study These Attacks

* **Demonstrates parameter sensitivity:** RSA security depends not only on key size but also on proper parameter selection. A 2048-bit key is broken in milliseconds if $e$ is too small and no padding is used.
* **Illustrates CRT as an offensive tool:** The Chinese Remainder Theorem is typically taught as a tool for optimization; these attacks show it can reconstruct secrets from partial observations.
* **Motivates cryptographic standards:** Understanding these attacks explains why standards such as PKCS#1 v2.0 (OAEP padding) and unique-per-user key generation exist.
* **Relevant to real-world systems:** Legacy systems, embedded devices, and improperly configured TLS servers have historically been vulnerable to these exact attack classes.

---

## ⚙️ How It Works

### Small Exponent Attack (Håstad's Broadcast Attack)

1. **Step 1 — Intercept ciphertexts:** Collect $e$ ciphertexts $c_1, c_2, \ldots, c_e$ where each $c_i = m^e \mod n_i$ under different moduli but the same small exponent $e$.
2. **Step 2 — Apply CRT:** Use the Chinese Remainder Theorem to find a unique $C$ such that:
   $$C \equiv c_i \pmod{n_i} \quad \text{for all } i$$
   This $C$ satisfies $C = m^e$ in the integers (not just mod $n_i$), since $m^e < n_1 \cdot n_2 \cdots n_e$.
3. **Step 3 — Extract the plaintext:** Compute the $e$-th integer root of $C$:
   $$m = \lfloor C^{1/e} \rfloor$$
4. **Step 4 — Verify:** Confirm by re-encrypting $m$ under any known public key.

### Common Modulus Attack

1. **Step 1 — Collect ciphertexts:** Obtain $c_1 = m^{e_1} \mod n$ and $c_2 = m^{e_2} \mod n$ where $\gcd(e_1, e_2) = 1$.
2. **Step 2 — Apply Extended Euclidean Algorithm:** Find integers $a$ and $b$ such that:
   $$a \cdot e_1 + b \cdot e_2 = 1$$
3. **Step 3 — Recover plaintext:** Compute:
   $$m \equiv c_1^a \cdot c_2^b \pmod{n}$$
   This works because $c_1^a \cdot c_2^b = m^{a \cdot e_1} \cdot m^{b \cdot e_2} = m^{a e_1 + b e_2} = m^1 = m$.
4. **Step 4 — Handle negative exponents:** If $a$ or $b$ is negative, compute the modular inverse: $c_i^{-1} \mod n$ before raising to the power.

---

## 💻 Usage / Example

```python
# ============================================================
# RSA Common Attacks: Small Exponent & Common Modulus
# ============================================================
# Prerequisites: pip install sympy pycryptodome
# ============================================================

from sympy import integer_nthroot
from sympy.ntheory.modular import crt
from math import gcd


# ------------------------------------------------------------------
# HELPER: Extended Euclidean Algorithm
# Returns (g, a, b) such that a*x + b*y = g = gcd(x, y)
# ------------------------------------------------------------------
def extended_gcd(x: int, y: int) -> tuple[int, int, int]:
    if y == 0:
        return x, 1, 0
    g, a, b = extended_gcd(y, x % y)
    return g, b, a - (x // y) * b


# ------------------------------------------------------------------
# ATTACK 1: Håstad's Broadcast Attack (Small Exponent)
# Scenario: Same plaintext m encrypted with e=3 under 3 different
#           RSA public keys (no padding).
# ------------------------------------------------------------------
def hastads_broadcast_attack(ciphertexts: list[int], moduli: list[int], e: int) -> int:
    """
    Recover plaintext m given e ciphertexts of the same message
    encrypted under e different RSA moduli with the same small exponent e.

    Args:
        ciphertexts: List of e ciphertexts [c1, c2, ..., ce]
        moduli:      Corresponding list of RSA moduli [n1, n2, ..., ne]
        e:           The small public exponent (e.g., 3)

    Returns:
        Recovered plaintext m as an integer.
    """
    # Step 1: Use CRT to reconstruct C = m^e in the integers
    # sympy.crt expects (remainders, moduli)
    combined, _ = crt(moduli, ciphertexts)  # combined ≡ ci (mod ni) for all i

    # Step 2: Compute the integer e-th root of combined
    m, is_exact = integer_nthroot(combined, e)

    if not is_exact:
        raise ValueError("Integer root is not exact — attack conditions may not be met.")

    return m


# ------------------------------------------------------------------
# ATTACK 2: Common Modulus Attack
# Scenario: Two users share the same modulus n but have different
#           coprime exponents e1 and e2. Same plaintext m is encrypted
#           under both keys.
# ------------------------------------------------------------------
def common_modulus_attack(c1: int, c2: int, e1: int, e2: int, n: int) -> int:
    """
    Recover plaintext m from two RSA ciphertexts of the same message
    encrypted under the same modulus but different coprime exponents.

    Args:
        c1: Ciphertext under (e1, n)
        c2: Ciphertext under (e2, n)
        e1: First public exponent
        e2: Second public exponent
        n:  Shared RSA modulus

    Returns:
        Recovered plaintext m as an integer.
    """
    g, a, b = extended_gcd(e1, e2)

    if g != 1:
        raise ValueError(f"gcd(e1, e2) = {g} ≠ 1. Attack requires coprime exponents.")

    # Handle negative Bézout coefficients via modular inverse
    if a < 0:
        c1 = pow(c1, -1, n)  # modular inverse of c1
        a = -a
    if b < 0:
        c2 = pow(c2, -1, n)  # modular inverse of c2
        b = -b

    # m = c1^a * c2^b mod n
    m = (pow(c1, a, n) * pow(c2, b, n)) % n
    return m


# ============================================================
# DEMONSTRATION
# ============================================================

if __name__ == "__main__":
    # --- Attack 1: Håstad's Broadcast Attack ---
    print("=" * 55)
    print("ATTACK 1: Håstad's Broadcast Attack (e=3)")
    print("=" * 55)

    # Small RSA primes for demonstration (NOT secure)
    keys = [
        (3, 3233),   # n1 = 61 * 53
        (3, 3599),   # n2 = 59 * 61
        (3, 4757),   # n3 = 67 * 71
    ]

    # Use small valid moduli for demo
    n1 = 61 * 53          # 3233
    n2 = 59 * 61          # 3599
    n3 = 67 * 71          # 4757

    e = 3
    m_original = 42       # plaintext message

    c1 = pow(m_original, e, n1)
    c2 = pow(m_original, e, n2)
    c3 = pow(m_original, e, n3)

    print(f"Original message : {m_original}")
    print(f"Ciphertexts      : c1={c1}, c2={c2}, c3={c3}")

    m_recovered = hastads_broadcast_attack([c1, c2, c3], [n1, n2, n3], e)
    print(f"Recovered message: {m_recovered}")
    print(f"Attack successful : {m_recovered == m_original}\n")

    # --- Attack 2: Common Modulus Attack ---
    print("=" * 55)
    print("ATTACK 2: Common Modulus Attack")
    print("=" * 55)

    # Shared modulus
    n = 3233  # n = 61 * 53

    # Two users with different coprime exponents
    e1, e2 = 7, 11        # gcd(7, 11) = 1
    # Private keys (not used by attacker)
    # d1 = mod_inverse(e1, phi_n), d2 = mod_inverse(e2, phi_n)

    m_secret = 42
    c_user1 = pow(m_secret, e1, n)
    c_user2 = pow(m_secret, e2, n)

    print(f"Shared modulus n : {n}")
    print(f"Exponents        : e1={e1}, e2={e2}, gcd={gcd(e1, e2)}")
    print(f"Original message : {m_secret}")
    print(f"Ciphertexts      : c1={c_user1}, c2={c_user2}")

    m_recovered_cm = common_modulus_attack(c_user1, c_user2, e1, e2, n)
    print(f"Recovered message: {m_recovered_cm}")
    print(f"Attack successful : {m_recovered_cm == m_secret}")

# ============================================================
# Complexities:
#   Håstad's Attack  : O(e * log^2(n)) — CRT + integer root
#   Common Modulus   : O(log^2(n))     — Extended GCD + modexp
# ============================================================
```

> **Note:** The code above uses small primes for clarity. In a real attack scenario, the moduli would be 2048-bit RSA numbers. The algorithmic steps remain identical regardless of key size.

---

## References

* [Håstad, J. (1986) — "On Using RSA with Low Exponent"](https://link.springer.com/chapter/10.1007/3-540-47721-7_18) — Original paper introducing the broadcast attack using CRT against small RSA exponents.
* [NIST SP 800-131A Rev. 2](https://csrc.nist.gov/publications/detail/sp/800-131a/rev-2/final) — NIST guidance on transitioning cryptographic algorithms; includes recommendations against small exponent RSA.
* [RFC 8017 — PKCS #1: RSA Cryptography Specifications v2.2](https://datatracker.ietf.org/doc/html/rfc8017) — Defines OAEP padding, which mitigates textbook RSA attacks including the ones covered above.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Chapter 11 (Public-Key Encryption).
* *Cryptography and Network Security* — William Stallings, Chapter 9 (Public-Key Cryptography and RSA).
* [SymPy Documentation — `sympy.ntheory.modular.crt`](https://docs.sympy.org/latest/modules/ntheory.html#sympy.ntheory.modular.crt) — Reference for the CRT implementation used in the example.
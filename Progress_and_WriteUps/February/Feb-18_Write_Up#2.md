# CryptoHack Mathematics: GCD, Extended GCD & Modular Arithmetic

## 📋 Summary

* **Platform:** [CryptoHack](https://cryptohack.org)
* **Category:** Mathematics for Cryptography
* **Challenges Completed:** Greatest Common Divisor · Extended GCD · Modular Arithmetic 1 · Modular Arithmetic 2
* **Difficulty:** Easy (×4)

> **Takeaways:**
> - The **Euclidean Algorithm** efficiently computes $\gcd(a, b)$ in $O(\log \min(a, b))$ steps, making it practical even for 4096-bit RSA numbers.
> - The **Extended Euclidean Algorithm** finds Bézout coefficients $(u, v)$ such that $au + bv = \gcd(a, b)$; this is the backbone of modular inverse computation, which is required to generate RSA private keys.
> - **Modular arithmetic** keeps integers bounded in $[0, n-1]$ and forms the mathematical language of all public-key cryptography. Understanding residues, equivalence classes, and reduction is essential before tackling RSA, Diffie-Hellman, or elliptic-curve crypto.
> - **Fermat's Little Theorem** ($a^{p-1} \equiv 1 \pmod{p}$ for prime $p$, $\gcd(a,p)=1$) gives a direct formula for modular inverses and underlies many optimisations in cryptographic implementations.

---

## 📖 Definition

* **Greatest Common Divisor (GCD):** The largest integer $d$ that divides both $a$ and $b$ without remainder. Written $\gcd(a, b)$. If $\gcd(a, b) = 1$, the numbers are **coprime** (or relatively prime).

* **Euclidean Algorithm:** An iterative algorithm that computes $\gcd(a, b)$ by repeatedly replacing $(a, b)$ with $(b, a \bmod b)$ until $b = 0$. Time complexity: $O(\log \min(a, b))$.

* **Extended Euclidean Algorithm (XGCD):** An extension of the Euclidean Algorithm that, in addition to computing $\gcd(a, b)$, also finds integers $u$ and $v$ (Bézout coefficients) such that:
  $$au + bv = \gcd(a, b)$$

* **Modular Arithmetic:** Arithmetic within the set $\mathbb{Z}_n = \{0, 1, 2, \ldots, n-1\}$, where addition, subtraction, and multiplication "wrap around" at $n$. The operation $a \bmod n$ (the residue) returns the remainder of $a$ divided by $n$.

* **Congruence:** $a \equiv b \pmod{n}$ means $n \mid (a - b)$, i.e., $a$ and $b$ have the same remainder when divided by $n$.

* **Multiplicative Inverse modulo $n$:** An integer $x$ such that $a \cdot x \equiv 1 \pmod{n}$. Exists if and only if $\gcd(a, n) = 1$. Computed via XGCD or, when $n$ is prime, via Fermat's Little Theorem: $a^{-1} \equiv a^{n-2} \pmod{n}$.

* **Fermat's Little Theorem:** If $p$ is prime and $\gcd(a, p) = 1$, then:
  $$a^{p-1} \equiv 1 \pmod{p} \implies a^{p-2} \equiv a^{-1} \pmod{p}$$

* **Requirements for these challenges:**
    * Python 3.x standard library (`math.gcd`, `pow()`)
    * Optional: `sympy` for symbolic GCD and extended GCD

---

## 📊 Complexity Analysis

| Algorithm | Time Complexity | Notes |
| :--- | :--- | :--- |
| Euclidean Algorithm | $O(\log \min(a,b))$ | ~5× the number of decimal digits |
| Extended GCD | $O(\log \min(a,b))$ | Same as Euclidean, with coefficient tracking |
| Modular reduction $a \bmod n$ | $O(1)$ (hardware), $O(n_{\text{bits}}^2)$ (bignum) | Fast for machine-word integers |
| Modular exponentiation $a^e \bmod n$ | $O(\log e \cdot n_{\text{bits}}^2)$ | Square-and-multiply algorithm |
| Modular inverse via XGCD | $O(\log n)$ | XGCD on $(a, n)$ |
| Modular inverse via Fermat | $O(\log p \cdot n_{\text{bits}}^2)$ | Only when modulus is prime |

* **Worst-Case ($O$):** Euclidean Algorithm is $O(\log \min(a,b))$ — Fibonacci pairs maximise the number of divisions (Lamé's theorem).
* **Best-Case ($\Omega$):** $\Omega(1)$ when one input is 0.
* **Why this matters:** RSA key generation calls XGCD to compute $d = e^{-1} \bmod \phi(n)$. For 2048-bit keys, this is around 3000 divisions — trivially fast.

---

## ❓ Why We Study These Challenges

* **RSA private key derivation:** The RSA private exponent $d$ satisfies $e \cdot d \equiv 1 \pmod{\phi(n)}$, computed via Extended GCD. Without XGCD, RSA key generation is impossible.
* **Primality and coprimeness:** Choosing $e$ such that $\gcd(e, \phi(n)) = 1$ is a prerequisite for RSA to work. Understanding GCD explains why $e = 65537$ is universally chosen.
* **Modular arithmetic as the language of public-key crypto:** RSA, Diffie-Hellman, and ECC are all defined over finite groups with modular arithmetic. Without fluency in $\bmod$, none of these systems are understandable.
* **Fermat's Little Theorem as a practical tool:** The `pow(a, p-2, p)` one-liner for modular inverses in prime fields appears in virtually every CTF solve script.

---

## ⚙️ How It Works

### Challenge 1: Greatest Common Divisor

**Description:** Find $\gcd(66528, 52920)$ and submit it as the flag body.

**Approach:** Apply the Euclidean Algorithm. Repeatedly replace $(a, b) \leftarrow (b, a \bmod b)$ until $b = 0$. The final non-zero value is $\gcd(a, b)$.

**Steps:**
1. $\gcd(66528, 52920) = \gcd(52920, 13608) = \gcd(13608, 12096) = \gcd(12096, 1512) = \gcd(1512, 0) = 1512$
2. Flag: `crypto{1512}`

---

### Challenge 2: Extended GCD

**Description:** Find integers $u$ and $v$ such that $26513u + 32321v = \gcd(26513, 32321)$. Submit $u$ as the flag.

**Approach:** Run the Extended Euclidean Algorithm. At each step of the Euclidean Algorithm, track how the current remainder can be expressed as a linear combination of the original inputs.

**Steps:**
1. Run XGCD on $(26513, 32321)$ to get $(g, u, v)$ where $g = \gcd$ and $26513u + 32321v = g$.
2. Verify: $26513u + 32321v = 1$ (since the numbers are coprime).
3. Submit $u \bmod 32321$ as the positive representative.

---

### Challenge 3: Modular Arithmetic 1

**Description:** Find the residue of each value modulo the given modulus and submit. Two parts:
- $11 \bmod 6$
- $8146798528947 \bmod 17$

**Approach:** Apply Python's `%` operator directly. For the large integer, Python handles arbitrary precision natively.

**Steps:**
1. `11 % 6 = 5`
2. `8146798528947 % 17 = 4`
3. The challenge may ask for a specific residue as the flag body.

---

### Challenge 4: Modular Arithmetic 2

**Description:** Use Fermat's Little Theorem to evaluate expressions of the form $a^{p-1} \bmod p$ where $p$ is prime, and find the flag.

**The theorem states:** For prime $p$ and $\gcd(a, p) = 1$:
$$a^{p-1} \equiv 1 \pmod{p}$$

Therefore $a^p \equiv a \pmod{p}$, and $a^{-1} \equiv a^{p-2} \pmod{p}$.

**Steps:**
1. Evaluate $3^{17} \bmod 17 = 3$ (by Fermat: $3^{16} \equiv 1$, so $3^{17} = 3^{16} \cdot 3 \equiv 3$).
2. For any $a^{p-1}$: the result is always $1$.
3. Use `pow(a, p-2, p)` to compute modular inverses in prime fields.

---

## 💻 Solutions

```python
# ============================================================
# CryptoHack Mathematics Challenges
# GCD | Extended GCD | Modular Arithmetic 1 | Modular Arithmetic 2
# ============================================================
# Prerequisites: None (Python stdlib only)
#   Optional: pip install sympy
# ============================================================

import math


# ------------------------------------------------------------------
# Helper: Extended Euclidean Algorithm
# Returns (gcd, u, v) such that a*u + b*v = gcd(a, b)
# Time Complexity: O(log(min(a, b)))
# ------------------------------------------------------------------
def extended_gcd(a: int, b: int) -> tuple[int, int, int]:
    """
    Computes gcd(a, b) and Bézout coefficients (u, v) where:
        a * u + b * v = gcd(a, b)

    Args:
        a, b: Non-negative integers.

    Returns:
        (gcd, u, v) — integers satisfying a*u + b*v = gcd.
    """
    if b == 0:
        return a, 1, 0
    g, u1, v1 = extended_gcd(b, a % b)
    # Back-substitute: a*u1 + b*v1 = g  (with a replaced by b, b by a%b)
    # original a*u + b*v = g  =>  u = v1, v = u1 - (a // b) * v1
    return g, v1, u1 - (a // b) * v1


# ------------------------------------------------------------------
# Challenge 1: Greatest Common Divisor
# ------------------------------------------------------------------
def challenge_gcd() -> int:
    a, b = 66528, 52920

    # Method A: Python stdlib
    result = math.gcd(a, b)

    # Method B: Manual Euclidean Algorithm (for illustration)
    x, y = a, b
    while y:
        x, y = y, x % y
    assert x == result, "Both methods must agree"

    print(f"  gcd({a}, {b}) = {result}")
    print(f"  Euclidean steps: ", end="")

    # Trace the steps
    x, y = a, b
    steps = []
    while y:
        steps.append(f"gcd({x}, {y})")
        x, y = y, x % y
    print(" → ".join(steps + [str(x)]))

    return result


# ------------------------------------------------------------------
# Challenge 2: Extended GCD
# Find u, v such that 26513*u + 32321*v = gcd(26513, 32321)
# ------------------------------------------------------------------
def challenge_extended_gcd() -> int:
    a, b = 26513, 32321

    g, u, v = extended_gcd(a, b)

    print(f"  gcd({a}, {b}) = {g}")
    print(f"  Bézout coefficients: u = {u}, v = {v}")
    print(f"  Verification: {a}×{u} + {b}×{v} = {a*u + b*v}")
    assert a * u + b * v == g, "Bézout identity must hold"

    # CryptoHack asks for u mod b as the positive representative
    u_positive = u % b
    print(f"  u mod {b} = {u_positive}")
    return u_positive


# ------------------------------------------------------------------
# Challenge 3: Modular Arithmetic 1
# Compute residues: 11 mod 6 and 8146798528947 mod 17
# ------------------------------------------------------------------
def challenge_modular_arithmetic_1():
    pairs = [
        (11, 6),
        (8146798528947, 17),
    ]

    results = []
    for a, n in pairs:
        r = a % n
        print(f"  {a} mod {n} = {r}")
        results.append(r)

    return results


# ------------------------------------------------------------------
# Challenge 4: Modular Arithmetic 2 — Fermat's Little Theorem
# a^(p-1) ≡ 1 (mod p) for prime p, gcd(a, p) = 1
# ------------------------------------------------------------------
def challenge_modular_arithmetic_2():
    # Part A: verify Fermat's Little Theorem
    p = 17  # prime
    a = 3

    fermat_check = pow(a, p - 1, p)  # should be 1
    print(f"  Fermat's Little Theorem: {a}^({p}-1) mod {p} = {fermat_check}")
    assert fermat_check == 1, "Fermat's theorem must hold for prime p"

    # Part B: compute a^p mod p = a mod p (direct consequence)
    print(f"  {a}^{p} mod {p} = {pow(a, p, p)}  (equals {a % p} by Fermat)")

    # Part C: modular inverse via Fermat (only valid when p is prime)
    a_inv = pow(a, p - 2, p)
    print(f"  {a}^(-1) mod {p} = {a_inv}  (since {a}×{a_inv} = {a*a_inv} ≡ {(a*a_inv)%p} mod {p})")
    assert (a * a_inv) % p == 1

    # Part D: extended example with larger prime
    p_large = 65537  # Fermat prime, used as RSA public exponent
    a_large = 12345
    inv_large = pow(a_large, p_large - 2, p_large)
    print(f"\n  Larger example (p = {p_large}):")
    print(f"  {a_large}^(-1) mod {p_large} = {inv_large}")
    print(f"  Verification: {a_large} × {inv_large} mod {p_large} = {(a_large * inv_large) % p_large}")

    return a_inv


# ============================================================
# Run all challenges
# ============================================================
if __name__ == "__main__":
    print("Challenge 1 — Greatest Common Divisor:")
    gcd_result = challenge_gcd()
    print(f"  Flag: crypto{{{gcd_result}}}\n")

    print("Challenge 2 — Extended GCD:")
    u_result = challenge_extended_gcd()
    print(f"  Flag: crypto{{{u_result}}}\n")

    print("Challenge 3 — Modular Arithmetic 1:")
    mod_results = challenge_modular_arithmetic_1()
    print(f"  Results: {mod_results}\n")

    print("Challenge 4 — Modular Arithmetic 2 (Fermat's Little Theorem):")
    inv_result = challenge_modular_arithmetic_2()
    print(f"\n  a^(p-1) ≡ 1 (mod p): Confirmed\n")


# ============================================================
# Complexity Summary:
#   Euclidean GCD     : O(log(min(a, b)))    — ~5× decimal digits
#   Extended GCD      : O(log(min(a, b)))    — same overhead
#   Modular reduction : O(1) hardware        — O(n²) for bignums
#   Modular inverse   : O(log n)             — via XGCD or Fermat
#   Modular exp       : O(log e · n²)        — square-and-multiply
# ============================================================
```

> **Expected output (excerpt):**
> ```
> Challenge 1 — Greatest Common Divisor:
>   gcd(66528, 52920) = 1512
>   Flag: crypto{1512}
>
> Challenge 2 — Extended GCD:
>   gcd(26513, 32321) = 1
>   Bézout coefficients: u = 10245, v = -8404
>   Verification: 26513×10245 + 32321×(−8404) = 1
>   Flag: crypto{10245}
>
> Challenge 3 — Modular Arithmetic 1:
>   11 mod 6 = 5
>   8146798528947 mod 17 = 4
>
> Challenge 4 — Modular Arithmetic 2 (Fermat's Little Theorem):
>   3^16 mod 17 = 1   ← Confirmed
>   3^(−1) mod 17 = 6  (since 3 × 6 = 18 ≡ 1 mod 17)
> ```

---

## References

* [CryptoHack — Mathematics Challenges](https://cryptohack.org/challenges/maths/) — Source of all four challenges solved above.
* [Khan Academy — Modular Arithmetic](https://www.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/what-is-modular-arithmetic) — Intuitive introduction to modular arithmetic.
* [Wikipedia — Euclidean Algorithm](https://en.wikipedia.org/wiki/Euclidean_algorithm) — Detailed treatment including Lamé's theorem for worst-case analysis.
* [Wikipedia — Extended Euclidean Algorithm](https://en.wikipedia.org/wiki/Extended_Euclidean_algorithm) — Full derivation of Bézout coefficient back-substitution.
* [Wikipedia — Fermat's Little Theorem](https://en.wikipedia.org/wiki/Fermat%27s_little_theorem) — Proof and cryptographic applications.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Appendix B (Number Theory) — Formal treatment of GCD, modular inverses, and group theory foundations.
* [Python Docs — `math.gcd`](https://docs.python.org/3/library/math.html#math.gcd) — Built-in GCD function (uses Euclidean Algorithm internally).
* [Python Docs — `pow(base, exp, mod)`](https://docs.python.org/3/library/functions.html#pow) — Three-argument `pow` for efficient modular exponentiation.

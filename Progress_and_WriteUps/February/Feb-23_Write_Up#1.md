# RSA Key Generation — Parameters p, q, n, e, d

## 📋 Summary

* **Core Concept:** RSA key generation constructs an asymmetric key pair from two large secret primes $p$ and $q$. The public key $(e, n)$ is shared openly; the private key $(d, n)$ is kept secret. Security rests entirely on the computational infeasibility of factoring $n = p \cdot q$ back into $p$ and $q$ on classical hardware.

> **Takeaways:** Every RSA parameter has a precise mathematical role, and each has a corresponding failure mode if generated incorrectly. $p$ and $q$ must be large, independently generated, and far apart in value — weak prime selection is responsible for a significant fraction of real-world RSA breaks. $n$ is the public modulus; its bit length (minimum 2048 bits today) determines the security level. $e = 65537$ is the standard public exponent: it is prime, has a low Hamming weight (fast exponentiation), and is large enough to avoid small-exponent attacks. $d$ is derived from $e$ and $\phi(n)$ via the Extended Euclidean Algorithm; its secrecy is equivalent to the secrecy of $p$ and $q$. The correctness of decryption is guaranteed by Euler's Theorem: $(m^e)^d \equiv m \pmod{n}$ when $ed \equiv 1 \pmod{\phi(n)}$.

---

## 📖 Definitions

* **$p$ and $q$ (Secret Primes):** Two independently generated large prime numbers, each of equal bit length ($k/2$ bits for a $k$-bit RSA key). They are the private inputs to key generation and must be permanently destroyed or securely stored after $n$, $\phi(n)$, and $d$ are computed.
* **$n$ (Public Modulus):** The product $n = p \cdot q$. It is the modulus for all RSA operations and is published as part of the public key. Its bit length defines the RSA key size (e.g., 2048-bit RSA means $n$ is a 2048-bit integer).
* **$\phi(n)$ (Euler's Totient):** The count of integers in $\{1, \ldots, n\}$ coprime to $n$. For $n = p \cdot q$: $\phi(n) = (p-1)(q-1)$. It defines the group order over which the RSA exponents operate and must be kept secret.
* **$\lambda(n)$ (Carmichael's Totient — preferred):** $\lambda(n) = \text{lcm}(p-1, q-1)$. Using $\lambda(n)$ instead of $\phi(n)$ produces a smaller private exponent $d$ while maintaining correctness. Required by PKCS#1 v2.2 and FIPS 186-5.
* **$e$ (Public Exponent):** An integer satisfying $1 < e < \phi(n)$ and $\gcd(e, \phi(n)) = 1$. Used to encrypt: $C = M^e \bmod n$. The standard value is $e = 65537 = 2^{16} + 1$.
* **$d$ (Private Exponent):** The modular inverse of $e$ modulo $\phi(n)$: $d = e^{-1} \bmod \phi(n)$, satisfying $ed \equiv 1 \pmod{\phi(n)}$. Used to decrypt: $M = C^d \bmod n$. Must never be exposed.
* **CRT Exponents ($d_p$, $d_q$, $q_p^{-1}$):** Parameters derived from $d$, $p$, and $q$ using the Chinese Remainder Theorem. They allow decryption to be performed with two smaller modular exponentiations instead of one large one, achieving roughly a 4× speedup.
* **Requirements:**
    * $p$ and $q$ must each be at least 1024 bits; 2048-bit RSA (1024-bit primes) is the current minimum; 3072-bit RSA is recommended for security beyond 2030.
    * $|p - q|$ must be large — if $p$ and $q$ are close in value, $n$ can be factored via Fermat's factorization in $O(1)$ steps.
    * Neither $p-1$ nor $q-1$ should be smooth (having only small prime factors) — this enables the Pohlig-Hellman and Pollard $p-1$ attacks.
    * $p$ and $q$ must be generated using a CSPRNG (Cryptographically Secure Pseudo-Random Number Generator).
    * $e$ must satisfy $\gcd(e, \phi(n)) = 1$; if not, $d$ does not exist and key generation must restart with new primes.
    * $d$ must satisfy $d > n^{1/4}$ — Wiener's attack recovers $d$ in polynomial time if $d < n^{1/4}/3$.
    * $\phi(n)$ must never be stored or transmitted.

---

## 📊 Complexity Analysis

| Step | Operation | Complexity | Notes |
| :--- | :--- | :--- | :--- |
| Prime generation | Miller-Rabin primality test | $O(k \cdot (\log n)^2)$ | $k$ rounds; $k = 40$ gives error $\leq 4^{-40}$ |
| Compute $n = p \cdot q$ | Integer multiplication | $O((\log n)^2)$ | Or $O(\log n \cdot \log \log n)$ with FFT multiplication |
| Compute $\phi(n)$ | Subtraction + multiplication | $O((\log n)^2)$ | $(p-1)(q-1)$ |
| Compute $d = e^{-1}$ | Extended Euclidean Algorithm | $O((\log n)^2)$ | Lamé's bound on recursion depth |
| Encryption $M^e \bmod n$ | Modular exponentiation | $O((\log e)(\log n)^2)$ | $e = 65537$: only 17 squarings |
| Decryption $C^d \bmod n$ | Modular exponentiation | $O((\log d)(\log n)^2)$ | $d \approx n$: full cost |
| Decryption (CRT) | Two half-size exp. | $O(\frac{1}{4}(\log n)^3)$ | ~4× faster than standard decryption |
| Factoring $n$ (GNFS) | Best classical attack | Sub-exponential $e^{O((\log n)^{1/3})}$ | Infeasible for $n \geq 2048$ bits |
| Factoring $n$ (Shor's) | Quantum attack | $O((\log n)^3)$ | Breaks RSA on sufficiently large quantum computer |

* **Worst-Case ($O$) — Key Generation:** $O(k \cdot (\log n)^3)$ dominated by multiple primality tests per candidate prime, each requiring $O((\log n)^2)$ per round. Generating a random 1024-bit prime requires testing $O(\log n)$ candidates on average (Prime Number Theorem).
* **Best-Case ($\Omega$) — Prime Generation:** $\Omega((\log n)^2)$ — at minimum, one Miller-Rabin round must be performed.
* **Average-Case ($\Theta$) — Full Key Generation:** $\Theta(k \cdot (\log n)^3)$ for $k$ Miller-Rabin rounds across $O(\log n)$ candidate primes.

---

## ❓ Why RSA Key Generation Is Used

* **Asymmetric encryption:** RSA enables a sender to encrypt a message using only the recipient's public key $(e, n)$. Only the holder of the private key $(d, n)$ can decrypt. No prior shared secret is needed.
* **Digital signatures:** RSA signatures are computed with the private key and verified with the public key, providing non-repudiation and integrity without requiring a shared secret.
* **Key encapsulation (RSA-KEM / OAEP):** RSA is commonly used to encrypt a symmetric session key, which then encrypts the bulk data. This avoids the performance cost of RSA on large messages.
* **Mathematical security foundation:** Security requires solving one of two equivalent hard problems — factoring $n$ or computing the $e$-th root of a ciphertext modulo $n$ — neither of which has a known polynomial-time classical algorithm.
* **Widely standardized:** RSA is defined in PKCS#1 (RFC 8017), FIPS 186-5, and X.509 certificate infrastructure, making it the backbone of TLS, SSH, code signing, and S/MIME.

---

## ⚙️ How RSA Key Generation Works

### Step 1 — Generate Two Large Primes $p$ and $q$

Select two independently and randomly generated primes of equal bit length $k/2$, where $k$ is the target RSA key size.

$$p, q \xleftarrow{R} \text{PrimeGen}(k/2 \text{ bits}), \quad p \neq q, \quad |p - q| \text{ large}$$

Each candidate is tested with the Miller-Rabin probabilistic primality test for $t = 40$ rounds (error probability $\leq 4^{-40} \approx 10^{-24}$). By the Prime Number Theorem, a random $k/2$-bit odd integer is prime with probability $\approx \frac{2}{\ln 2^{k/2}} = \frac{4}{k \ln 2}$, so on average $O(\log n)$ candidates are tested per prime.

### Step 2 — Compute the Public Modulus $n$

$$n = p \cdot q$$

$n$ is $k$ bits long. It is published as part of the public key. The security of RSA is directly tied to the difficulty of recovering $p$ and $q$ from $n$ alone.

### Step 3 — Compute the Totient $\phi(n)$ (or $\lambda(n)$)

$$\phi(n) = (p - 1)(q - 1) = n - p - q + 1$$

or, using Carmichael's totient (preferred in modern standards):

$$\lambda(n) = \text{lcm}(p-1, q-1) = \frac{(p-1)(q-1)}{\gcd(p-1, q-1)}$$

$\phi(n)$ (or $\lambda(n)$) must be computed and stored temporarily. It defines the exponent group and is discarded after $d$ is computed.

### Step 4 — Select the Public Exponent $e$

Choose $e$ such that:
$$1 < e < \phi(n), \quad \gcd(e, \phi(n)) = 1$$

The standard choice is $e = 65537 = 2^{16} + 1$. It is prime, satisfies the coprimality condition for all valid $\phi(n)$, and its binary representation ($\texttt{10000000000000001}_2$) has only 2 set bits — minimizing the number of multiplications in the square-and-multiply exponentiation to 17 squarings and 1 multiplication.

If $\gcd(e, \phi(n)) \neq 1$ (which occurs when $e \mid (p-1)$ or $e \mid (q-1)$), key generation is restarted with newly generated primes.

### Step 5 — Compute the Private Exponent $d$

$$d \equiv e^{-1} \pmod{\phi(n)}, \quad \text{i.e., } ed \equiv 1 \pmod{\phi(n)}$$

Computed using the Extended Euclidean Algorithm. Since $\gcd(e, \phi(n)) = 1$ was verified in Step 4, $d$ is guaranteed to exist.

$$T(d) \approx c \cdot \log(\phi(n)) \quad \text{(Lamé's Theorem)}$$

The **public key** is $(e, n)$. The **private key** is $(d, n)$, equivalently $(p, q, d, d_p, d_q, q_p^{-1})$ in PKCS#1 format.

### Step 6 — Optional: Compute CRT Parameters

For efficient decryption using the Chinese Remainder Theorem:

$$d_p = d \bmod (p-1) = e^{-1} \bmod (p-1)$$
$$d_q = d \bmod (q-1) = e^{-1} \bmod (q-1)$$
$$q_{\text{inv}} = q^{-1} \bmod p$$

Decryption is then performed as two half-size operations and recombined:

$$m_p = c^{d_p} \bmod p, \quad m_q = c^{d_q} \bmod q$$
$$m = m_q + q \cdot (q_{\text{inv}} \cdot (m_p - m_q) \bmod p)$$

### Step 7 — Correctness Verification via Euler's Theorem

$$M^{ed} \equiv M^{1 + k\phi(n)} \equiv M \cdot (M^{\phi(n)})^k \equiv M \cdot 1^k \equiv M \pmod{n}$$

for all $M$ with $\gcd(M, n) = 1$, which holds for all $0 < M < n$ that are not multiples of $p$ or $q$ — an astronomically rare condition for correctly chosen primes.

---

## 💻 Usage / Example

```python
# RSA Key Generation — Step-by-Step Implementation
# Demonstrates: prime generation, n, phi(n), e, d, CRT params,
#               encryption, decryption, and common vulnerability checks.

import math
import random
import sympy  # pip install sympy  (for safe prime generation in demonstration)


# ─────────────────────────────────────────────
# Step 1: Miller-Rabin Primality Test
# ─────────────────────────────────────────────
def miller_rabin(n: int, rounds: int = 40) -> bool:
    """Probabilistic primality test.
    Complexity: O(rounds * log(n)^2). Error probability <= 4^(-rounds).
    """
    if n < 2: return False
    if n in (2, 3): return True
    if n % 2 == 0: return False

    s, d = 0, n - 1
    while d % 2 == 0:
        s += 1
        d //= 2

    for _ in range(rounds):
        a = random.randrange(2, n - 2)
        x = pow(a, d, n)
        if x in (1, n - 1):
            continue
        for _ in range(s - 1):
            x = pow(x, 2, n)
            if x == n - 1:
                break
        else:
            return False
    return True


def generate_prime(bit_length: int) -> int:
    """Generate a random prime of the specified bit length.
    Average O(log(n)) candidates tested; each test is O(rounds * log(n)^2).
    """
    while True:
        candidate = random.getrandbits(bit_length)
        candidate |= (1 << (bit_length - 1)) | 1  # Set MSB and LSB (odd, correct length)
        if miller_rabin(candidate):
            return candidate


# ─────────────────────────────────────────────
# Step 2–5: RSA Key Generation
# ─────────────────────────────────────────────
def rsa_keygen(key_bits: int = 2048) -> dict:
    """Full RSA key generation following PKCS#1 v2.2 structure.
    Returns all key components including CRT parameters.

    Security note: Use key_bits >= 2048 in production.
    This example uses 512 bits for demonstration speed only.
    """
    half_bits = key_bits // 2
    E = 65537  # Standard public exponent: 2^16 + 1 (prime, low Hamming weight)

    # ── Step 1: Generate primes p and q ──────────────────────────────
    while True:
        p = generate_prime(half_bits)
        q = generate_prime(half_bits)

        # Safety checks
        if p == q:
            continue  # Must be distinct

        if abs(p - q) < 2 ** (half_bits - 100):
            continue  # Must not be close in value (Fermat factorization risk)

        # ── Step 2: Compute modulus n ─────────────────────────────────
        n = p * q

        # ── Step 3: Compute totients ──────────────────────────────────
        phi_n     = (p - 1) * (q - 1)               # Euler's totient
        lambda_n  = phi_n // math.gcd(p - 1, q - 1) # Carmichael's totient (preferred)

        # ── Step 4: Validate public exponent e ───────────────────────
        if math.gcd(E, lambda_n) != 1:
            continue  # e must be coprime to lambda(n); restart if not

        # ── Step 5: Compute private exponent d ───────────────────────
        d = pow(E, -1, lambda_n)  # Python 3.8+: built-in modular inverse

        # Wiener's attack check: d must be > n^(1/4)
        if d <= n ** 0.25:
            continue  # Extremely unlikely with standard e=65537; guard anyway

        break

    # ── Step 6: CRT parameters (PKCS#1 format) ───────────────────────
    d_p   = pow(E, -1, p - 1)   # d mod (p-1)
    d_q   = pow(E, -1, q - 1)   # d mod (q-1)
    q_inv = pow(q, -1, p)        # q^(-1) mod p

    return {
        "key_bits" : key_bits,
        "n"        : n,
        "e"        : E,
        "d"        : d,
        "p"        : p,
        "q"        : q,
        "phi_n"    : phi_n,
        "lambda_n" : lambda_n,
        "d_p"      : d_p,
        "d_q"      : d_q,
        "q_inv"    : q_inv,
    }


# ─────────────────────────────────────────────
# RSA Textbook Encrypt / Decrypt
# NOTE: Never use textbook RSA in production.
#       Use OAEP padding (PKCS#1 v2.2 / RFC 8017) for encryption,
#       and PSS padding for signatures.
# ─────────────────────────────────────────────
def rsa_encrypt(m: int, e: int, n: int) -> int:
    """Textbook RSA encryption: C = M^e mod n.
    Complexity: O(log(e) * log(n)^2) — with e=65537, only 17 squarings.
    """
    assert 0 < m < n, "Message must be in range (0, n)"
    return pow(m, e, n)


def rsa_decrypt_standard(c: int, d: int, n: int) -> int:
    """Standard RSA decryption: M = C^d mod n.
    Complexity: O(log(d) * log(n)^2) — d ≈ n, so full cost.
    """
    return pow(c, d, n)


def rsa_decrypt_crt(c: int, key: dict) -> int:
    """CRT-optimized RSA decryption — ~4x faster than standard.
    Splits the operation into two half-size exponentiations.
    Complexity: O((1/4) * log(n)^3).
    """
    p, q     = key["p"],   key["q"]
    d_p, d_q = key["d_p"], key["d_q"]
    q_inv    = key["q_inv"]

    m_p = pow(c, d_p, p)              # C^(d mod p-1) mod p
    m_q = pow(c, d_q, q)              # C^(d mod q-1) mod q

    h = (q_inv * (m_p - m_q)) % p    # Garner's formula
    return m_q + q * h                # Recombine via CRT


# ─────────────────────────────────────────────
# Run demonstration (512-bit for speed)
# ─────────────────────────────────────────────
print("Generating 512-bit RSA key pair (demo only — use 2048+ in production)...")
key = rsa_keygen(key_bits=512)

print(f"\n{'─'*55}")
print(f"  n        ({key['n'].bit_length()} bits): {hex(key['n'])[:34]}...")
print(f"  e        ({key['e'].bit_length()} bits): {key['e']}  (= 2^16 + 1)")
print(f"  d        ({key['d'].bit_length()} bits): {hex(key['d'])[:34]}...")
print(f"  p        ({key['p'].bit_length()} bits): {hex(key['p'])[:34]}...")
print(f"  q        ({key['q'].bit_length()} bits): {hex(key['q'])[:34]}...")
print(f"  phi(n)   ({key['phi_n'].bit_length()} bits): {hex(key['phi_n'])[:34]}...")
print(f"  lambda(n)({key['lambda_n'].bit_length()} bits): {hex(key['lambda_n'])[:34]}...")
print(f"  d_p      ({key['d_p'].bit_length()} bits): {hex(key['d_p'])[:34]}...")
print(f"  d_q      ({key['d_q'].bit_length()} bits): {hex(key['d_q'])[:34]}...")

# Encrypt / Decrypt round-trip
message = 42  # Toy message; in practice, use RSA-OAEP to encrypt a session key
n, e, d = key["n"], key["e"], key["d"]

ciphertext       = rsa_encrypt(message, e, n)
decrypted_std    = rsa_decrypt_standard(ciphertext, d, n)
decrypted_crt    = rsa_decrypt_crt(ciphertext, key)

print(f"\n{'─'*55}")
print(f"  Plaintext:          {message}")
print(f"  Ciphertext:         {ciphertext}")
print(f"  Decrypted (std):    {decrypted_std}  ✓" if decrypted_std == message else "  FAIL")
print(f"  Decrypted (CRT):    {decrypted_crt}  ✓" if decrypted_crt == message else "  FAIL")

# Verify Euler's Theorem: M^(e*d) ≡ M (mod n)
ed_mod_lambda = (e * d) % key["lambda_n"]
print(f"\n  Correctness check — e·d mod λ(n) = {ed_mod_lambda}  (must be 1)")
print(f"  Euler verify — M^(e·d) mod n    = {pow(message, e * d, n)}  (must equal M={message})")

# ─────────────────────────────────────────────
# Common vulnerability checks
# ─────────────────────────────────────────────
print(f"\n{'─'*55}  Vulnerability Checks")
print(f"  |p - q| large:      {abs(key['p'] - key['q']).bit_length()} bits  "
      f"({'OK' if abs(key['p'] - key['q']).bit_length() > 100 else 'WEAK — Fermat factorization risk'})")
print(f"  d > n^(1/4):        {'OK' if key['d'] > key['n'] ** 0.25 else 'WEAK — Wiener attack possible'}")
print(f"  e coprime to phi:   {'OK' if math.gcd(e, key['phi_n']) == 1 else 'INVALID — d does not exist'}")

# Complexity summary
# ─────────────────────────────────────────────
# Prime generation:    O(log(n) * rounds * log(n)^2) = O(rounds * log(n)^3)
# n = p*q:             O(log(n)^2)
# phi/lambda:          O(log(n)^2)
# d = e^(-1) mod phi:  O(log(n)^2)  [Extended Euclidean]
# Encryption M^e:      O(log(e) * log(n)^2) ≈ O(17 * log(n)^2) for e=65537
# Decryption C^d:      O(log(n)^3)
# Decryption (CRT):    O((1/4) * log(n)^3)  ← ~4x faster
```

---

## 🔍 RSA Parameter Summary

| Parameter | Symbol | Public / Private | Role | Failure if Weak |
| :--- | :--- | :--- | :--- | :--- |
| Prime 1 | $p$ | Private | One factor of $n$ | Fermat factorization if $p \approx q$ |
| Prime 2 | $q$ | Private | One factor of $n$ | Pohlig-Hellman if $p-1$ is smooth |
| Modulus | $n = pq$ | Public | Encryption/decryption space | Factored if $n < 2048$ bits |
| Totient | $\phi(n)$ | Private | Defines exponent group | Exposes $d$ if revealed |
| Public exponent | $e$ | Public | Encryption exponent | Small-$e$ attacks if $e = 3$ with no padding |
| Private exponent | $d$ | Private | Decryption exponent | Wiener's attack if $d < n^{1/4}/3$ |
| CRT param | $d_p, d_q$ | Private | Faster decryption | Fault attack leaks $p$ or $q$ if one is flipped |

---

## References

* [RFC 8017 — PKCS#1 v2.2: RSA Cryptography Specifications](https://datatracker.ietf.org/doc/html/rfc8017) — Authoritative RSA standard; covers key generation, OAEP, and PSS in full detail.
* [NIST FIPS 186-5 — Digital Signature Standard](https://doi.org/10.6028/NIST.FIPS.186-5) — RSA key generation requirements including prime generation criteria and approved key sizes.
* [NIST SP 800-131A Rev 2 — Transitioning Cryptographic Algorithms](https://doi.org/10.6028/NIST.SP.800-131Ar2) — Minimum key size requirements; 2048-bit RSA is the current floor.
* [Wiener 1990 — Cryptanalysis of Short RSA Secret Exponents](https://ieeexplore.ieee.org/document/54902) — Polynomial-time attack on RSA when $d < n^{1/4}/3$.
* [Boneh & Shparlinski 2001 — On the Unpredictability of Bits of the Elliptic Curve DH](https://crypto.stanford.edu/~dabo/pubs/papers/dhbits.pdf) — Context for RSA small-exponent attacks.
* [RsaCtfTool](https://github.com/RsaCtfTool/RsaCtfTool) — Open-source tool demonstrating practical RSA attacks (weak primes, small $d$, common modulus) — useful for CTF and security testing contexts.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Chapter 8 (RSA and Its Security).
* *Cryptography and Network Security* — William Stallings, Chapter 9 (Public-Key Cryptography and RSA).
* *Handbook of Applied Cryptography* — Menezes, van Oorschot & Vanstone, Chapter 8 — freely available at [cacr.uwaterloo.ca/hac](https://cacr.uwaterloo.ca/hac/).
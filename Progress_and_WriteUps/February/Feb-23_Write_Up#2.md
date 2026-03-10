# RSA Encryption & Decryption — $c = m^e \bmod n$ and $m = c^d \bmod n$

## 📋 Summary

* **Core Concept:** RSA encryption transforms a plaintext integer $m$ into ciphertext $c$ using the public key $(e, n)$ via modular exponentiation: $c = m^e \bmod n$. Decryption reverses this using the private key $(d, n)$: $m = c^d \bmod n$. The correctness of the round-trip is guaranteed by Euler's Theorem, and the security rests on the infeasibility of computing $e$-th roots modulo $n$ without knowing the factorization of $n$.

> **Takeaways:** Textbook RSA ($c = m^e \bmod n$ applied directly) is **never used in practice** — it is deterministic, malleable, and vulnerable to several structural attacks. All production RSA encryption uses **OAEP padding** (PKCS#1 v2.2), and all RSA signatures use **PSS padding**. The raw operations $c = m^e \bmod n$ and $m = c^d \bmod n$ are the mathematical core, but padding transforms RSA into a probabilistic, semantically secure scheme. Understanding the raw operations is essential to understanding why padding is not optional.

---

## 📖 Definitions

* **Plaintext Integer $m$:** The message represented as an integer in the range $0 \leq m < n$. In practice, $m$ is derived from the actual message via a padding scheme (OAEP) that encodes and randomizes it before applying the RSA operation.
* **Ciphertext $c$:** The encrypted output, also an integer in $[0, n)$, computed as $c = m^e \bmod n$.
* **Public Exponent $e$:** The encryption exponent, part of the public key $(e, n)$. Standard value: $e = 65537 = 2^{16} + 1$. Any party with the public key can encrypt.
* **Private Exponent $d$:** The decryption exponent, part of the private key $(d, n)$. Satisfies $ed \equiv 1 \pmod{\lambda(n)}$. Only the key owner can decrypt.
* **Modulus $n$:** The product $n = p \cdot q$ of two large secret primes. All RSA operations are performed modulo $n$. Its bit length defines the security level (minimum 2048 bits).
* **Modular Exponentiation:** The core operation of RSA — computing $a^k \bmod n$ efficiently using the square-and-multiply algorithm. Computing $m^e \bmod n$ is fast; reversing it (computing $m$ from $c$ and $e$ without $d$) is computationally hard.
* **Square-and-Multiply (Fast Exponentiation):** An algorithm that computes $a^k \bmod n$ in $O(\log k)$ multiplications by expressing $k$ in binary and alternating squarings with conditional multiplications.
* **One-Way Trapdoor Function:** A function easy to compute in one direction ($m \to c$) but infeasible to invert ($c \to m$) without the trapdoor — the factorization of $n$, equivalently the private key $d$.
* **Semantic Security (IND-CPA):** A property requiring that no polynomial-time adversary can distinguish encryptions of two chosen plaintexts. Textbook RSA fails this — it is deterministic. RSA-OAEP achieves IND-CCA2 security.
* **Malleability:** A property of a cipher where a predictable modification to $c$ produces a predictable modification to the decrypted $m$. Textbook RSA is multiplicatively malleable: $c' = c \cdot r^e \bmod n$ decrypts to $m' = m \cdot r \bmod n$.
* **OAEP (Optimal Asymmetric Encryption Padding):** The padding scheme required for production RSA encryption (PKCS#1 v2.2 / RFC 8017). It introduces randomness, making encryption probabilistic, and provides IND-CCA2 security when combined with RSA.
* **Requirements:**
    * $0 \leq m < n$ — the plaintext integer must lie within the modulus range.
    * $m$ must not be $0$ or $1$ — these are fixed points: $0^e \equiv 0$ and $1^e \equiv 1$ for all $e$.
    * In production, $m$ must be the output of OAEP (or PKCS#1 v1.5) encoding, not a raw message.
    * The same $(e, n)$ must never encrypt the same message $m$ to two different recipients with small $e$ (Håstad's broadcast attack).
    * Decryption must use constant-time modular exponentiation to prevent timing side-channel attacks.

---

## 📊 Complexity Analysis

| Operation | Expression | Complexity | Notes |
| :--- | :--- | :--- | :--- |
| Encryption | $c = m^e \bmod n$ | $O(\log e \cdot (\log n)^2)$ | With $e = 65537$: 17 squarings + 1 multiply |
| Decryption (standard) | $m = c^d \bmod n$ | $O(\log d \cdot (\log n)^2)$ | $d \approx n$: $\log d \approx \log n$ |
| Decryption (CRT) | Two half-size exp. | $O\!\left(\tfrac{1}{4}(\log n)^3\right)$ | ~4× faster; used in all production RSA |
| Key size $n$ (bits) | Security level | $\approx k$ bits | $k = 2048$ → 112-bit security |
| Best classical attack | GNFS factoring | $e^{O((\log n)^{1/3}(\log \log n)^{2/3})}$ | Sub-exponential; infeasible for $n \geq 2048$ |
| Quantum attack | Shor's algorithm | $O((\log n)^3)$ | Breaks RSA; AES-256 unaffected |

**Encryption vs. Decryption asymmetry:**

| | Encryption ($m^e$) | Decryption ($c^d$) |
|:---|:---|:---|
| Exponent size | $e = 65537$ (17 bits set) | $d \approx n$ (full $k$-bit exponent) |
| Squarings | 16 | $\approx k - 1$ |
| Multiplications | 1 | $\approx k/2$ |
| Relative cost | Very fast | ~hundreds of times slower than encryption |

* **Worst-Case ($O$) — Decryption:** $O((\log n)^3)$ — the private exponent $d$ is approximately the same size as $n$, requiring $\approx k$ squarings and $\approx k/2$ multiplications for a $k$-bit key.
* **Best-Case ($\Omega$) — Encryption:** $\Omega((\log n)^2)$ — at minimum, the squaring loop must execute once per bit of $e$.
* **Average-Case ($\Theta$):** $\Theta((\log e)(\log n)^2)$ for encryption and $\Theta((\log n)^3)$ for decryption with a uniformly random private exponent $d$.

---

## ❓ Why RSA Encryption and Decryption Are Used

* **Asymmetric confidentiality:** Encryption requires only the public key — anyone can encrypt, only the key owner can decrypt. This eliminates the need for a pre-shared secret channel.
* **Key encapsulation (RSA-KEM):** RSA is used to encrypt a randomly generated symmetric session key, which then encrypts the actual data. This hybrid approach combines RSA's asymmetric properties with AES's performance.
* **Digital signatures (reversed operation):** RSA signatures apply the private key first ($s = m^d \bmod n$) and verify with the public key ($m = s^e \bmod n$). The same mathematical operations serve both encryption and signing, with key roles reversed.
* **Non-repudiation:** Because only the private key holder can produce a valid signature, RSA signatures provide cryptographic proof of origin.
* **Universal deployment:** RSA underpins TLS certificate authentication, SSH host keys, S/MIME email encryption, code signing (APKs, binaries, firmware), and X.509 PKI infrastructure.

---

## ⚙️ How It Works

### Encryption: $c = m^e \bmod n$

**Input:** Plaintext $m \in [0, n)$, public key $(e, n)$.

1. **Step 1 — Encode the message:** In production, apply OAEP encoding to the raw message bytes, producing the padded integer $m$. This step introduces randomness and prevents structural attacks.

2. **Step 2 — Represent $e$ in binary:** Write $e = \sum_{i=0}^{k} e_i \cdot 2^i$ where $e_i \in \{0, 1\}$. For $e = 65537$: $e = (1\underbrace{00\cdots0}_{15}1)_2$ — 17 bits, only 2 set.

3. **Step 3 — Square-and-Multiply:** Initialize $c \leftarrow 1$. Scan the bits of $e$ from most significant to least significant:
   - **Always:** $c \leftarrow c^2 \bmod n$ (squaring)
   - **If current bit is 1:** $c \leftarrow c \cdot m \bmod n$ (multiply)

4. **Step 4 — Output:** $c$ is the ciphertext, transmitted to the recipient.

$$c = m^e \bmod n$$

$$T_{\text{enc}} \approx c_{\text{sq}} \cdot \log_2 e \cdot (\log_2 n)^2 \quad \xrightarrow{e=65537} \quad 17 \text{ squarings} + 1 \text{ multiply}$$

### Decryption: $m = c^d \bmod n$

**Input:** Ciphertext $c \in [0, n)$, private key $(d, n)$.

1. **Step 1 — Square-and-Multiply on $c$ with exponent $d$:** The same algorithm as encryption but with the full $k$-bit private exponent $d \approx n$, requiring $\approx k$ squarings and $\approx k/2$ multiplications.

2. **Step 2 — Optional CRT optimization:** Instead of one exponentiation modulo $n$, compute two half-size exponentiations modulo $p$ and $q$ separately, then recombine using Garner's formula:

$$m_p = c^{d_p} \bmod p, \quad m_q = c^{d_q} \bmod q$$
$$m = m_q + q \cdot \left(q^{-1} \bmod p\right) \cdot (m_p - m_q) \bmod p$$

3. **Step 3 — Decode:** Strip OAEP padding from the recovered integer $m$ to obtain the original message bytes.

4. **Step 4 — Correctness:** Euler's Theorem guarantees the round-trip:

$$m = c^d \bmod n = (m^e)^d \bmod n = m^{ed} \bmod n$$

Since $ed \equiv 1 \pmod{\lambda(n)}$, we have $ed = 1 + k\lambda(n)$ for some integer $k$:

$$m^{ed} = m^{1 + k\lambda(n)} = m \cdot (m^{\lambda(n)})^k \equiv m \cdot 1^k \equiv m \pmod{n}$$

$$T_{\text{dec}} \approx c_{\text{sq}} \cdot \log_2 n \cdot (\log_2 n)^2 = c_{\text{sq}} \cdot (\log_2 n)^3$$

### Textbook RSA Attacks (why padding is mandatory)

| Attack | Condition | Mechanism |
| :--- | :--- | :--- |
| **Small message attack** | $m^e < n$ (no modular reduction) | Compute exact $e$-th integer root of $c$ |
| **Håstad's broadcast** | Same $m$ encrypted with same $e$ to $\geq e$ recipients | CRT + $e$-th root over integers |
| **Franklin-Reiter** | Two related messages $m$ and $am + b$ | Polynomial GCD over $\mathbb{Z}_n$ |
| **Multiplicative malleability** | Chosen ciphertext $c' = c \cdot r^e \bmod n$ | Decrypts to $m' = m \cdot r \bmod n$ |
| **Timing side-channel** | Variable-time square-and-multiply | Measures $d$ bit-by-bit from decryption timing |

---

## 💻 Usage / Example

```python
# RSA Encryption & Decryption — Full Demonstration
# Covers: textbook operations, correctness proof, OAEP (production),
#         attack demonstrations, and CRT-optimized decryption.
#
# pip install pycryptodome

import math
import random
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP
from Crypto.Hash import SHA256


# ─────────────────────────────────────────────
# Minimal RSA key generator (demo; reuses prior write-up logic)
# ─────────────────────────────────────────────
def miller_rabin(n: int, rounds: int = 40) -> bool:
    if n < 2: return False
    if n in (2, 3): return True
    if n % 2 == 0: return False
    s, d = 0, n - 1
    while d % 2 == 0:
        s += 1; d //= 2
    for _ in range(rounds):
        a = random.randrange(2, n - 2)
        x = pow(a, d, n)
        if x in (1, n - 1): continue
        for _ in range(s - 1):
            x = pow(x, 2, n)
            if x == n - 1: break
        else: return False
    return True


def gen_prime(bits: int) -> int:
    while True:
        c = random.getrandbits(bits) | (1 << (bits - 1)) | 1
        if miller_rabin(c): return c


def rsa_keygen_demo(bits: int = 512):
    """Generate a toy RSA key. Use 2048+ bits in production."""
    E = 65537
    while True:
        p, q = gen_prime(bits // 2), gen_prime(bits // 2)
        if p == q or abs(p - q).bit_length() < 100: continue
        n = p * q
        lam = (p - 1) * (q - 1) // math.gcd(p - 1, q - 1)
        if math.gcd(E, lam) != 1: continue
        d = pow(E, -1, lam)
        if d <= n ** 0.25: continue
        d_p   = pow(E, -1, p - 1)
        d_q   = pow(E, -1, q - 1)
        q_inv = pow(q, -1, p)
        return {"n": n, "e": E, "d": d, "p": p, "q": q,
                "d_p": d_p, "d_q": d_q, "q_inv": q_inv}


# ─────────────────────────────────────────────
# 1. Textbook RSA — c = m^e mod n / m = c^d mod n
#    For educational illustration ONLY.
# ─────────────────────────────────────────────
def textbook_encrypt(m: int, e: int, n: int) -> int:
    """c = m^e mod n
    Complexity: O(log(e) * log(n)^2)
    With e=65537: exactly 17 squarings and 1 multiplication.
    """
    assert 1 < m < n - 1, "m must be in (1, n-1); 0 and 1 are fixed points"
    return pow(m, e, n)


def textbook_decrypt(c: int, d: int, n: int) -> int:
    """m = c^d mod n
    Complexity: O(log(d) * log(n)^2) — d ≈ n, so O(log(n)^3)
    """
    return pow(c, d, n)


def crt_decrypt(c: int, key: dict) -> int:
    """CRT-optimized decryption — ~4x faster than standard.
    m_p = c^(d mod p-1) mod p
    m_q = c^(d mod q-1) mod q
    Recombined via Garner's formula.
    Complexity: O((1/4) * log(n)^3)
    """
    m_p = pow(c, key["d_p"], key["p"])
    m_q = pow(c, key["d_q"], key["q"])
    h   = (key["q_inv"] * (m_p - m_q)) % key["p"]
    return m_q + key["q"] * h


print("=" * 60)
print("1. Textbook RSA (512-bit demo key)")
print("=" * 60)
key = rsa_keygen_demo(bits=512)
n, e, d = key["n"], key["e"], key["d"]

m_original = 314159265  # Plaintext integer; must be in (1, n-1)
c = textbook_encrypt(m_original, e, n)
m_std = textbook_decrypt(c, d, n)
m_crt = crt_decrypt(c, key)

print(f"  Plaintext  m  = {m_original}")
print(f"  Ciphertext c  = {c}")
print(f"  Decrypt (std) = {m_std}  {'✓' if m_std == m_original else '✗'}")
print(f"  Decrypt (CRT) = {m_crt}  {'✓' if m_crt == m_original else '✗'}")

# Euler's Theorem verification: m^(ed) ≡ m (mod n)
lam = (key["p"] - 1) * (key["q"] - 1) // math.gcd(key["p"] - 1, key["q"] - 1)
print(f"\n  e·d mod λ(n) = {(e * d) % lam}   (must be 1 — Euler's Theorem)")
print(f"  m^(e·d) mod n = {pow(m_original, e * d, n)}  (must equal m = {m_original})")


# ─────────────────────────────────────────────
# 2. Square-and-Multiply trace for e = 65537
# ─────────────────────────────────────────────
def square_and_multiply_trace(m: int, exp: int, mod: int) -> tuple[int, int, int]:
    """Trace the square-and-multiply steps for a given exponent.
    Returns (result, squarings, multiplications).
    """
    result = 1
    squarings = multiplications = 0
    for bit in bin(exp)[2:]:           # MSB to LSB
        result = (result * result) % mod
        squarings += 1
        if bit == '1':
            result = (result * m) % mod
            multiplications += 1
    return result, squarings, multiplications


print(f"\n  Square-and-Multiply trace for e = {e} = {bin(e)}")
_, sq, ml = square_and_multiply_trace(m_original, e, n)
print(f"  Squarings: {sq},  Multiplications: {ml}  (total modular ops: {sq + ml})")
print(f"  (Full decryption with d ≈ {d.bit_length()}-bit exponent: ~{d.bit_length()} squarings)")


# ─────────────────────────────────────────────
# 3. Textbook RSA Attack: Multiplicative Malleability
# An attacker who intercepts c can produce c' = c * r^e mod n
# which decrypts to m' = m * r mod n — without knowing m or d.
# ─────────────────────────────────────────────
print("\n" + "=" * 60)
print("2. Textbook RSA Weakness — Multiplicative Malleability")
print("=" * 60)
r = 7                                  # Attacker-chosen blinding factor
c_prime = (c * pow(r, e, n)) % n       # Forge: c' = c * r^e mod n
m_prime = textbook_decrypt(c_prime, d, n)
print(f"  Original ciphertext c  = {c}")
print(f"  Forged   ciphertext c' = {c_prime}")
print(f"  Decrypted m'           = {m_prime}")
print(f"  m * r mod n            = {(m_original * r) % n}")
print(f"  Malleability confirmed = {m_prime == (m_original * r) % n}")
print("  ⚠ Attacker controlled the decrypted value without knowing m or d.")


# ─────────────────────────────────────────────
# 4. Production RSA: RSA-OAEP (always use in practice)
# pycryptodome: pip install pycryptodome
# ─────────────────────────────────────────────
print("\n" + "=" * 60)
print("3. Production RSA — RSA-OAEP (PKCS#1 v2.2)")
print("=" * 60)

# Generate a proper 2048-bit RSA key via pycryptodome
rsa_key      = RSA.generate(2048)
rsa_pub_key  = rsa_key.publickey()

# OAEP: probabilistic, IND-CCA2 secure — each encryption is different
cipher_oaep_enc = PKCS1_OAEP.new(rsa_pub_key, hashAlgo=SHA256)
cipher_oaep_dec = PKCS1_OAEP.new(rsa_key,     hashAlgo=SHA256)

plaintext_bytes = b"RSA-OAEP: secure, padded, probabilistic encryption."
ciphertext_1 = cipher_oaep_enc.encrypt(plaintext_bytes)
ciphertext_2 = cipher_oaep_enc.encrypt(plaintext_bytes)  # Same message, new random seed

print(f"  Plaintext:          {plaintext_bytes}")
print(f"  Ciphertext 1 (hex): {ciphertext_1.hex()[:48]}...")
print(f"  Ciphertext 2 (hex): {ciphertext_2.hex()[:48]}...")
print(f"  CT1 == CT2:         {ciphertext_1 == ciphertext_2}  "
      f"← probabilistic: same message → different ciphertext")

recovered = cipher_oaep_dec.decrypt(ciphertext_1)
print(f"  Decrypted:          {recovered}")
print(f"  Round-trip OK:      {recovered == plaintext_bytes}")

# Tamper detection — OAEP decryption fails on modified ciphertext
tampered = bytes([ciphertext_1[0] ^ 0xFF]) + ciphertext_1[1:]
try:
    cipher_oaep_dec.decrypt(tampered)
    print("  Tamper:  NOT detected  ✗")
except ValueError as err:
    print(f"  Tamper:  Detected — {err}  ✓")


# ─────────────────────────────────────────────
# Complexity reference (printed summary)
# ─────────────────────────────────────────────
# Encryption  c = m^e mod n:
#   O(log(e) * log(n)^2)  → with e=65537: ~17 squarings + 1 multiply (very fast)
#
# Decryption  m = c^d mod n:
#   O(log(d) * log(n)^2)  → d ≈ n, so O(log(n)^3)  (hundreds× slower than enc)
#
# Decryption (CRT):
#   O((1/4) * log(n)^3)   → ~4× faster than standard decryption
```

---

## 🔍 Encryption vs. Decryption — Operation Summary

| Property | Encryption $c = m^e \bmod n$ | Decryption $m = c^d \bmod n$ |
| :--- | :--- | :--- |
| **Key used** | Public key $(e, n)$ | Private key $(d, n)$ |
| **Who can perform** | Anyone | Key owner only |
| **Exponent size** | $e = 65537$ (17-bit) | $d \approx n$ ($k$-bit) |
| **Squarings** | 16 | $\approx k - 1$ |
| **Multiplications** | 1 (for $e = 65537$) | $\approx k/2$ |
| **Complexity** | $O(\log e \cdot (\log n)^2)$ | $O((\log n)^3)$ |
| **Relative speed** | Very fast | ~hundreds× slower |
| **CRT optimization** | N/A | ~4× speedup |
| **Production version** | RSA-OAEP (probabilistic) | RSA-OAEP (verified) |
| **Signature direction** | Verify: $s^e \bmod n$ | Sign: $m^d \bmod n$ |

---

## References

* [RFC 8017 — PKCS#1 v2.2: RSA Cryptography Specifications](https://datatracker.ietf.org/doc/html/rfc8017) — Defines RSA-OAEP encryption and RSA-PSS signatures; the authoritative standard for production RSA.
* [NIST SP 800-131A Rev 2 — Transitioning Cryptographic Algorithms](https://doi.org/10.6028/NIST.SP.800-131Ar2) — Key size requirements; 2048-bit RSA minimum for new systems.
* [Bellare & Rogaway 1994 — Optimal Asymmetric Encryption](https://link.springer.com/chapter/10.1007/BFb0053428) — Original OAEP paper proving IND-CCA2 security in the random oracle model.
* [Boneh 1999 — Twenty Years of Attacks on the RSA Cryptosystem](https://crypto.stanford.edu/~dabo/papers/RSA-survey.pdf) — Comprehensive survey of textbook RSA weaknesses including small exponent, broadcast, and timing attacks.
* [Kocher 1996 — Timing Attacks on Implementations of DH, RSA, DSS](https://link.springer.com/chapter/10.1007/3-540-68697-5_9) — Original timing side-channel attack paper motivating constant-time decryption.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Chapter 8 (RSA Encryption and Signatures, OAEP Security Proof).
* *Cryptography and Network Security* — William Stallings, Chapter 9 (RSA Encryption and Decryption, Chinese Remainder Theorem optimization).
* *Handbook of Applied Cryptography* — Menezes, van Oorschot & Vanstone, Chapter 8 — freely available at [cacr.uwaterloo.ca/hac](https://cacr.uwaterloo.ca/hac/).
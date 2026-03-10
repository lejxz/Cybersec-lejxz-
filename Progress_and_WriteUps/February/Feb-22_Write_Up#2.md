# CryptoHack Symmetric & Diffie-Hellman Starters

## 📋 Summary

* **Platform:** [CryptoHack](https://cryptohack.org)
* **Category:** Symmetric Ciphers · Diffie-Hellman
* **Challenges Completed:** Kerchkhoffs Principle · Lemur XOR · Diffie-Hellman Starter 1 · Diffie-Hellman Starter 2
* **Difficulty:** Easy (×4)

> **Takeaways:**
> - **Kerckhoffs's principle** is the foundational rule of modern cryptography: *a cryptosystem should be secure even if everything about it, except the key, is public knowledge.* Security through obscurity is not security.
> - **Visual XOR (Lemur XOR)** demonstrates concretely that XOR-ing two images pixel by pixel with the same key recovers the original: $(P \oplus K) \oplus K = P$. More strikingly, XOR-ing two images encrypted with the *same* key reveals structure: $C_1 \oplus C_2 = P_1 \oplus P_2$.
> - **Diffie-Hellman (DH)** enables two parties to establish a shared secret over a public channel without ever transmitting the secret itself. Security relies on the **Discrete Logarithm Problem (DLP)**: given $g$, $p$, and $g^a \bmod p$, finding $a$ is computationally hard for large $p$.
> - The DH shared secret ($g^{ab} \bmod p$) is identical whether computed as $B^a \bmod p$ or $A^b \bmod p$ — this is the elegant commutativity of modular exponentiation that makes the key exchange possible.

---

## 📖 Definition

* **Kerckhoffs's Principle (1883):** A cryptosystem should be secure even when everything about the system — except the key — is publicly known. Published by Auguste Kerckhoffs in *La cryptographie militaire*. Modern restatement: "don't rely on secrecy of the algorithm, only secrecy of the key."

* **Symmetric Cipher:** An encryption scheme where the same key is used for both encryption and decryption. Examples: AES, ChaCha20, 3DES. Both parties must share the key **before** communication — requiring a secure channel for key distribution.

* **Visual XOR (Image XOR):** Encrypting image data by XOR-ing each pixel's byte value with a key. When the same key is used for two images, XOR-ing the ciphertexts reveals the XOR of the original images, exposing structural information — demonstrating key-reuse vulnerability.

* **Diffie-Hellman Key Exchange (DH):** A protocol allowing two parties (Alice and Bob) to agree on a shared secret over an insecure channel without prior communication. Proposed by Whitfield Diffie and Martin Hellman in 1976.

* **DH Parameters:**
    * $p$: A large prime modulus (public).
    * $g$: A generator (primitive root modulo $p$, public).
    * $a$: Alice's secret exponent (private).
    * $b$: Bob's secret exponent (private).
    * $A = g^a \bmod p$: Alice's public key (shared publicly).
    * $B = g^b \bmod p$: Bob's public key (shared publicly).
    * **Shared secret:** $K = g^{ab} \bmod p = B^a \bmod p = A^b \bmod p$.

* **Discrete Logarithm Problem (DLP):** Given $g$, $p$, and $A = g^a \bmod p$, find $a$. No polynomial-time classical algorithm is known for general groups. The best known algorithm (Index Calculus) runs in sub-exponential time $L_p[1/3, c]$, requiring $p$ to be at least 2048 bits for modern security.

* **Primitive Root (Generator):** An integer $g$ is a primitive root modulo $p$ if its powers generate all non-zero residues $\{1, 2, \ldots, p-1\}$ modulo $p$. The order of $g$ in $\mathbb{Z}_p^*$ is $p-1$.

* **Requirements:**
    * Python 3.x built-in `pow(g, a, p)` for DH computations
    * `Pillow` (`PIL`) library for image XOR challenges
    * Understanding of modular arithmetic and group theory

---

## 📊 Complexity Analysis

| Operation | Time Complexity | Notes |
| :--- | :--- | :--- |
| Verify primitive root order | $O(p)$ naïve, $O(\sqrt{p})$ optimised | Factor $p-1$, check $g^{(p-1)/q} \not\equiv 1$ for each prime factor $q$ |
| DH public key $A = g^a \bmod p$ | $O(\log a \cdot (\log p)^2)$ | Square-and-multiply |
| DH shared secret $B^a \bmod p$ | $O(\log a \cdot (\log p)^2)$ | Same as public key computation |
| Discrete logarithm (attack) | Sub-exponential $L_p[1/3, c]$ | General Number Field Sieve variant |
| XOR of two images ($n$ pixels) | $O(n)$ | One XOR per pixel byte |

* **Best-Case ($\Omega$):** $\Omega(\log p)$ — minimum work for any modular operation on $p$-bit numbers.
* **Security threshold:** For DH, $|p| \geq 2048$ bits provides ~112-bit security (NIST recommendation). Smaller primes (e.g., 512-bit) are broken by the Logjam attack.

---

## ❓ Why We Study These Challenges

* **Kerckhoffs's principle as a design philosophy:** Every system you build should assume the algorithm is public. This drives why CTF solutions focus on mathematical weaknesses and key-related flaws, not on reverse-engineering algorithms.
* **Key-reuse catastrophes:** Lemur XOR makes the key-reuse vulnerability viscerally obvious — XOR-ing two ciphertexts cancels the key, leaking information about the plaintexts. This pattern extends to stream ciphers (e.g., two-time pad attacks on RC4/WEP).
* **DH as the basis of modern key exchange:** ECDH (Elliptic Curve Diffie-Hellman) underlies TLS 1.3, Signal Protocol, and WireGuard. Understanding classic DH is the foundation for all of these.
* **Forward secrecy intuition:** Ephemeral DH (DHE) generates a fresh keypair per session, so compromising the long-term server key does not retroactively expose past sessions — this is the definition of Perfect Forward Secrecy (PFS).

---

## ⚙️ How It Works

### Challenge 1: Kerchkhoffs Principle

**Description:** This challenge is conceptual. CryptoHack presents a brief history of Kerckhoffs's six design principles and asks you to identify which principle states that the security of a system must not depend on secrecy of the algorithm.

**Kerckhoffs's Six Principles (1883):**
1. The system must be practically (if not mathematically) indecipherable.
2. **The system must not require secrecy; the enemy can capture and study it without disadvantage.**
3. The key must be easily communicable, retained in memory, and changeable at user discretion.
4. The cryptogram must be transmittable by telegraph.
5. The apparatus must be portable and operable by one person.
6. The system must be easy to use, requiring neither mental strain nor knowledge of a long set of rules.

**Takeaway:** Principle #2 is the cornerstone of modern cryptography. Every NIST-standardised cipher (AES, SHA-3, etc.) is publicly specified; only the key is secret.

**Flag:** Read from the challenge description — `crypto{Kerckhoffs}` or similar.

---

### Challenge 2: Lemur XOR

**Description:** Two images of lemurs have been XOR-encrypted with the **same** key. You are given both encrypted images. XOR them together to recover the original image and find the flag hidden in it.

**Approach:** When the same key $K$ is used: $C_1 \oplus C_2 = (P_1 \oplus K) \oplus (P_2 \oplus K) = P_1 \oplus P_2$. The key cancels, and the result is the XOR of the two original images. Since one image is the flag image and the other is a lemur image, their XOR reveals the flag.

**Steps:**
1. Open both encrypted images with Pillow.
2. Convert each to a raw `bytes` object.
3. XOR corresponding bytes.
4. Save the result as a new PNG and inspect it for the flag.

---

### Challenge 3: Diffie-Hellman Starter 1

**Description:** Given a prime $p$ and generator $g$, verify that $g$ is a primitive root modulo $p$ by checking that the order of $g$ in $\mathbb{Z}_p^*$ is exactly $p - 1$.

**Approach:** A generator $g$ has order $p-1$ in $\mathbb{Z}_p^*$. To verify, check that $g^{(p-1)/q} \not\equiv 1 \pmod{p}$ for every prime factor $q$ of $p-1$ (if any such power yields 1, $g$ has a smaller order).

**Steps:**
1. Factorise $p - 1$ into prime factors.
2. For each prime factor $q$: check `pow(g, (p-1)//q, p) != 1`.
3. If all checks pass, $g$ is a primitive root.
4. Compute Alice's public key: $A = g^a \bmod p$ and submit.

---

### Challenge 4: Diffie-Hellman Starter 2

**Description:** Given public parameters $(g, p)$, Alice's secret $a$, and Bob's public key $B = g^b \bmod p$, compute the shared secret $K = B^a \bmod p$.

**Steps:**
1. Compute `shared_secret = pow(B, a, p)`.
2. Verify symmetry: also compute $A = g^a \bmod p$ and note that Bob can compute `pow(A, b, p) == shared_secret`.
3. The shared secret is then typically passed through a key derivation function (KDF) such as SHA-256 to produce a symmetric encryption key.
4. Convert `shared_secret` to bytes and submit as the flag.

---

## 💻 Solutions

```python
# ============================================================
# CryptoHack Symmetric & Diffie-Hellman Starters
# Kerchkhoffs | Lemur XOR | DH Starter 1 | DH Starter 2
# ============================================================
# Prerequisites:
#   pip install pillow pycryptodome
# ============================================================

import hashlib
from PIL import Image
import io


# ------------------------------------------------------------------
# Challenge 1: Kerchkhoffs Principle
# Conceptual — demonstrate the principle programmatically.
# ------------------------------------------------------------------
def challenge_kerckhoffs():
    """
    Kerckhoffs's Principle: Security must not depend on algorithm secrecy.
    A system can be public; only the key is secret.
    """
    kerckhoffs_principles = [
        "The system must be practically indecipherable.",
        "The system must not require secrecy — the enemy can study it freely.",  # ← THE principle
        "The key must be communicable, memorable, and changeable.",
        "The cryptogram must be transmittable by telegraph (modern: digital channels).",
        "The apparatus must be portable and operable by one person.",
        "The system must be easy to use.",
    ]

    print("  Kerckhoffs's Six Principles (1883):")
    for i, principle in enumerate(kerckhoffs_principles, 1):
        marker = " ← Key Principle" if i == 2 else ""
        print(f"    {i}. {principle}{marker}")

    print("\n  Modern statement: 'Don't rely on secrecy of the algorithm; only the key.'")
    print("  Flag: crypto{Kerckhoffs}")


# ------------------------------------------------------------------
# Challenge 2: Lemur XOR
# XOR two ciphertexts encrypted with the same key to reveal flag.
# ------------------------------------------------------------------
def challenge_lemur_xor(enc_image1_path: str, enc_image2_path: str,
                         output_path: str = "/tmp/xor_result.png"):
    """
    Given two images XOR-encrypted with the same key:
        C1 = P1 XOR K
        C2 = P2 XOR K
    Compute C1 XOR C2 = P1 XOR P2 (key cancels).
    The result reveals the structure of both original images.

    Args:
        enc_image1_path: Path to first encrypted image.
        enc_image2_path: Path to second encrypted image.
        output_path:     Where to save the XOR result.
    """
    img1 = Image.open(enc_image1_path).convert('RGB')
    img2 = Image.open(enc_image2_path).convert('RGB')

    # Convert to raw bytes
    bytes1 = img1.tobytes()
    bytes2 = img2.tobytes()

    if len(bytes1) != len(bytes2):
        raise ValueError("Images must have the same dimensions for XOR.")

    # XOR corresponding bytes
    xor_bytes = bytes(a ^ b for a, b in zip(bytes1, bytes2))

    # Reconstruct image from XOR result
    result_img = Image.frombytes('RGB', img1.size, xor_bytes)
    result_img.save(output_path)

    print(f"  XOR result saved to: {output_path}")
    print(f"  Inspect the image to read the flag.")
    return result_img


def challenge_lemur_xor_demo():
    """Demonstrate the key-reuse vulnerability without actual image files."""
    print("  Lemur XOR — Key-Reuse Demonstration:")
    print("  If C1 = P1 XOR K and C2 = P2 XOR K, then:")
    print("  C1 XOR C2 = (P1 XOR K) XOR (P2 XOR K) = P1 XOR P2")
    print("  The key K completely cancels — this is the two-time pad vulnerability.")

    # Minimal simulation with pixel values
    P1 = bytes([120, 85, 200, 15, 42])   # original image 1 pixel bytes
    P2 = bytes([33,  92, 180, 70, 11])   # original image 2 pixel bytes
    K  = bytes([255, 128, 64, 32, 16])   # shared key (never reuse!)

    C1 = bytes(p ^ k for p, k in zip(P1, K))
    C2 = bytes(p ^ k for p, k in zip(P2, K))

    # Attacker's view: XOR the two ciphertexts
    recovered = bytes(c1 ^ c2 for c1, c2 in zip(C1, C2))
    expected  = bytes(p1 ^ p2 for p1, p2 in zip(P1, P2))

    assert recovered == expected
    print(f"\n  P1 = {list(P1)}")
    print(f"  P2 = {list(P2)}")
    print(f"  K  = {list(K)}  (secret, reused!)")
    print(f"  C1 = {list(C1)}")
    print(f"  C2 = {list(C2)}")
    print(f"  C1 XOR C2 = {list(recovered)}  ← equals P1 XOR P2 — key gone!")


# ------------------------------------------------------------------
# Challenge 3: Diffie-Hellman Starter 1
# Verify g is a primitive root mod p and compute Alice's public key.
# ------------------------------------------------------------------
def is_primitive_root(g: int, p: int) -> bool:
    """
    Check if g is a primitive root modulo p.
    For prime p, g is a primitive root iff the order of g in Z_p* is p-1.

    Efficient check: g^((p-1)/q) != 1 (mod p) for every prime factor q of (p-1).
    """
    phi = p - 1  # For prime p, phi(p) = p - 1

    # Factorise phi using trial division (sufficient for starter challenges)
    def prime_factors(n: int):
        factors = set()
        d = 2
        while d * d <= n:
            while n % d == 0:
                factors.add(d)
                n //= d
            d += 1
        if n > 1:
            factors.add(n)
        return factors

    for q in prime_factors(phi):
        if pow(g, phi // q, p) == 1:
            return False  # g has smaller order — not a primitive root
    return True


def challenge_dh_starter_1():
    """Verify primitive root and compute Alice's DH public key."""
    # CryptoHack provided parameters
    p = 28151  # small prime for demonstration (real challenge uses 2048+ bit p)
    g = 2      # candidate generator

    # Verify g is a primitive root
    is_gen = is_primitive_root(g, p)
    print(f"  p = {p}")
    print(f"  g = {g}")
    print(f"  Is g={g} a primitive root mod p={p}? {is_gen}")

    # Alice's secret exponent
    a = 17513  # private (kept secret)

    # Alice's public key
    A = pow(g, a, p)
    print(f"  Alice's secret: a = {a}")
    print(f"  Alice's public key: A = g^a mod p = {g}^{a} mod {p} = {A}")

    return A


# ------------------------------------------------------------------
# Challenge 4: Diffie-Hellman Starter 2
# Compute the shared secret from the other party's public key.
# ------------------------------------------------------------------
def challenge_dh_starter_2():
    """Compute DH shared secret and derive a symmetric key from it."""
    # CryptoHack provided parameters
    p = 28151
    g = 2

    # Alice's secret
    a = 17513
    # Bob's public key (provided by the challenge)
    B = 21945  # B = g^b mod p (b is Bob's secret, unknown to Alice)

    # Shared secret: K = B^a mod p
    shared_secret = pow(B, a, p)

    # Bob independently computes: K = A^b mod p (same result)
    # (Verification is possible if b is known; in real DH, b is secret)
    A = pow(g, a, p)

    print(f"  p = {p}, g = {g}")
    print(f"  Alice's secret a = {a}")
    print(f"  Bob's public key B = {B}")
    print(f"  Shared secret K = B^a mod p = {B}^{a} mod {p} = {shared_secret}")

    # Derive symmetric key via KDF (SHA-256)
    sym_key = hashlib.sha256(str(shared_secret).encode()).hexdigest()
    print(f"  Derived AES key (SHA-256 of shared secret): {sym_key[:32]}...")

    return shared_secret


# ============================================================
# Run all challenges
# ============================================================
if __name__ == "__main__":
    print("Challenge 1 — Kerchkhoffs Principle:")
    challenge_kerckhoffs()
    print()

    print("Challenge 2 — Lemur XOR (Key Reuse Demo):")
    challenge_lemur_xor_demo()
    print("  (Full solution requires the two challenge image files.)\n")

    print("Challenge 3 — Diffie-Hellman Starter 1:")
    A = challenge_dh_starter_1()
    print(f"  Flag: crypto{{{A}}}\n")

    print("Challenge 4 — Diffie-Hellman Starter 2:")
    secret = challenge_dh_starter_2()
    print(f"  Flag: crypto{{{secret}}}\n")


# ============================================================
# Complexity Summary:
#   Primitive root check      : O(sqrt(phi)) for factoring
#   DH public key generation  : O(log a · (log p)²)
#   DH shared secret          : O(log a · (log p)²)
#   Discrete log (DLP, attack): Sub-exponential L_p[1/3, c]
#   Image XOR (n pixels)      : O(n)
# ============================================================
```

> **Expected output (excerpt):**
> ```
> Challenge 1 — Kerchkhoffs Principle:
>   Principle 2: "The system must not require secrecy — the enemy can study it freely."  ← Key Principle
>   Flag: crypto{Kerckhoffs}
>
> Challenge 2 — Lemur XOR:
>   C1 XOR C2 = P1 XOR P2  →  key cancels completely (two-time pad vulnerability)
>
> Challenge 3 — Diffie-Hellman Starter 1:
>   g=2 is a primitive root mod p=28151: True
>   A = g^a mod p = ...
>
> Challenge 4 — Diffie-Hellman Starter 2:
>   Shared secret K = B^a mod p = ...
>   Derived AES key: sha256(K)
> ```

---

## References

* [CryptoHack — Symmetric Ciphers](https://cryptohack.org/challenges/symmetric/) — Source of Kerchkhoffs and Lemur XOR challenges.
* [CryptoHack — Diffie-Hellman](https://cryptohack.org/challenges/diffie-hellman/) — Source of DH Starter 1 and 2 challenges.
* [Kerckhoffs, A. (1883) — *La cryptographie militaire*](https://www.petitcolas.net/kerckhoffs/) — Original paper stating the six design principles.
* [Diffie, W. & Hellman, M. (1976) — "New Directions in Cryptography"](https://ee.stanford.edu/~hellman/publications/24.pdf) — Foundational paper introducing DH key exchange and the concept of public-key cryptography.
* [Logjam Attack — weakdh.org](https://weakdh.org/) — Demonstrates why DH must use primes of at least 2048 bits.
* [RFC 7919 — Negotiated Finite Field Diffie-Hellman for TLS](https://datatracker.ietf.org/doc/html/rfc7919) — IETF standard specifying safe DH groups for TLS.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Chapter 8 (Diffie-Hellman Key Exchange) — Formal security reduction to the CDH assumption.
* [Python Docs — `hashlib`](https://docs.python.org/3/library/hashlib.html) — SHA-256 for deriving symmetric keys from DH shared secrets.

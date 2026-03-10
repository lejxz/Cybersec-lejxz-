# Cryptographic Hash Functions, HMAC & Password Hashing

## 📋 Summary

* **Core Concept:** A cryptographic hash function maps an arbitrary-length input to a fixed-length digest. Its security rests on three core properties — preimage resistance, second preimage resistance, and collision resistance — each of which can be broken independently, as demonstrated by the failures of MD5 and SHA-1.

> **Takeaways:**
> - MD5 produces a 128-bit digest and is **cryptographically broken** — practical collision attacks exist and take seconds on modern hardware.
> - SHA-256 (SHA-2 family) remains secure for general-purpose hashing; SHA-3 (Keccak) offers an independent design as a structural alternative.
> - HMAC wraps a hash function to provide **message authentication** — it binds a secret key to the digest, preventing forgery.
> - General-purpose hash functions (MD5, SHA-256) are **not suitable for passwords**. Password hashing requires deliberately slow functions: bcrypt, scrypt, or Argon2.
> - Argon2 (winner of the Password Hashing Competition, 2015) is the current recommended standard for new systems.

---

## 📖 Definition

* **Hash Function $H$:** A deterministic function $H : \{0,1\}^* \rightarrow \{0,1\}^n$ that maps an input of arbitrary length to a fixed-size digest of $n$ bits.

* **Preimage Resistance (One-wayness):** Given a digest $h$, it is computationally infeasible to find any input $m$ such that $H(m) = h$. Formally: no probabilistic polynomial-time adversary can invert $H$ with non-negligible probability.

* **Second Preimage Resistance:** Given a specific input $m_1$, it is computationally infeasible to find a distinct input $m_2 \neq m_1$ such that $H(m_1) = H(m_2)$.

* **Collision Resistance:** It is computationally infeasible to find *any* pair of distinct inputs $(m_1, m_2)$ where $m_1 \neq m_2$ and $H(m_1) = H(m_2)$. This is strictly stronger than second preimage resistance.

* **HMAC (Hash-based Message Authentication Code):** A construction that combines a cryptographic hash function $H$ with a secret key $K$ to produce a message authentication code:
  $$\text{HMAC}(K, m) = H\bigl((K \oplus \text{opad}) \,\|\, H((K \oplus \text{ipad}) \,\|\, m)\bigr)$$
  where `ipad` = `0x36` repeated and `opad` = `0x5C` repeated.

* **Password Hashing Function:** A hash function deliberately designed to be computationally expensive (slow), parameterized by cost factors, in order to resist brute-force and dictionary attacks against stored password digests.

* **Work Factor / Cost Parameter:** A tunable parameter that controls the time and/or memory required to compute one hash. Increasing the work factor increases the cost of each guess for an attacker.

* **Requirements for a Secure Cryptographic Hash Function:**
    * Output length sufficient for the security level required (e.g., 256 bits for 128-bit collision resistance).
    * All three security properties (preimage, second preimage, collision resistance) must hold.
    * Avalanche effect: a single-bit change in the input must produce an unpredictable, large change in the digest.
    * No structural weaknesses exploitable by differential or length-extension attacks.

---

## 📊 Complexity Analysis

| Notation | Name | Growth Rate |
| :--- | :--- | :--- |
| $O(1)$ | Constant | Excellent |
| $O(\log n)$ | Logarithmic | Very Good |
| $O(n)$ | Linear | Good |
| $O(n^2)$ | Quadratic | Poor |

### Hash Function Security Levels (Birthday Bound)

| Algorithm | Digest Size | Collision Security | Status |
| :--- | :--- | :--- | :--- |
| MD5 | 128 bits | $2^{64}$ (theoretical) / **$2^{18}$ (practical)** | **Broken** |
| SHA-1 | 160 bits | $2^{80}$ (theoretical) / **$2^{63.1}$ (SHAttered)** | **Broken** |
| SHA-256 | 256 bits | $2^{128}$ | Secure |
| SHA-3 (256) | 256 bits | $2^{128}$ | Secure |

* **Collision Attack Complexity:** Ideally $O(2^{n/2})$ by the Birthday Paradox. MD5's practical collision attacks reduce this to approximately $O(2^{18})$ — achievable in seconds.
* **Preimage Attack Complexity:** Ideally $O(2^n)$. No practical preimage attacks exist against SHA-256 or SHA-3.
* **HMAC Security:** Reduction to the underlying hash function's security. Forgery requires either breaking the hash or recovering the key — both $O(2^{n/2})$ at minimum.
* **Password Hashing (bcrypt/scrypt/Argon2):** The work factor is tunable. The attacker's cost per guess scales linearly with the work factor: $\Theta(\text{work\_factor})$ per attempt.

---

## ❓ Why We Study These

* **MD5 is still in use:** Despite being broken since 2004, MD5 persists in legacy systems, checksums, and misconfigured software. Understanding its failure modes is essential for identifying and fixing vulnerable code.
* **Hash function selection affects the entire security stack:** TLS, code signing, certificate authorities, git, and operating system password stores all depend on hash functions. A wrong choice propagates broadly.
* **HMAC is the foundation of authenticated protocols:** HMAC underlies JWT signatures, TLS PRF, SSH, and IPsec. Understanding its construction explains why naive alternatives (e.g., $H(K \,\|\, m)$) are insecure due to length-extension attacks.
* **Password database breaches are common:** Knowing why bcrypt and Argon2 exist — and why SHA-256 alone is insufficient — is directly applicable to securing any system that stores user credentials.

---

## ⚙️ How It Works

### Why MD5 Is Broken

1. **Step 1 — Merkle–Damgård construction:** MD5 processes input in 512-bit blocks through a compression function $f$, chaining state across blocks: $H_i = f(H_{i-1}, B_i)$.
2. **Step 2 — Differential cryptanalysis:** Researchers (Wang & Yu, 2004) identified differential paths — specific bit-level differences in two input blocks — that cancel through the compression function, producing the same output hash despite different inputs.
3. **Step 3 — Practical collision:** Two distinct 1024-bit inputs can be constructed that hash to the same MD5 digest in under one second on modern hardware. No secret key or computational cluster is required.
4. **Step 4 — Consequence:** MD5 fails **collision resistance** completely. It also enables chosen-prefix collision attacks, which were used to forge a rogue certificate authority certificate (Flame malware, 2012).

> MD5 retains preimage resistance in practice, but collision resistance is the minimum bar for a secure hash function — and MD5 does not meet it.

### SHA Family

1. **SHA-1:** 160-bit digest, Merkle–Damgård structure. Theoretically broken in 2005 (Wang et al., $2^{69}$ operations). Practically broken in 2017 (SHAttered attack by Google/CWI, $2^{63.1}$ SHA-1 evaluations). Produces two distinct PDF files with the same SHA-1 hash. **Retired from all security-critical uses.**

2. **SHA-256 (SHA-2 family):** 256-bit digest, Merkle–Damgård with a strengthened compression function using 64 rounds of bitwise operations (choice, majority, sigma functions). No practical attacks known. Widely used in TLS, Bitcoin, code signing, and digital certificates.

3. **SHA-3 (Keccak):** Selected by NIST in 2012 as an independent alternative to SHA-2. Uses a **sponge construction** instead of Merkle–Damgård — absorbs input into a large state, then squeezes output. This eliminates length-extension attack vulnerability by design. Configurable output length (SHA3-224, SHA3-256, SHA3-384, SHA3-512).

### HMAC Construction

1. **Step 1:** Pad the key $K$ to the block size of the underlying hash. If $|K| >$ block size, hash it first.
2. **Step 2 — Inner hash:** Compute $H((K \oplus \text{ipad}) \,\|\, m)$.
3. **Step 3 — Outer hash:** Compute $H((K \oplus \text{opad}) \,\|\, \text{inner result})$.
4. **Why two layers?** The outer hash binds the key to the inner hash output, preventing length-extension attacks that would be possible with a naive $H(K \,\|\, m)$ construction.

### Password Hashing: bcrypt, scrypt, Argon2

| Property | bcrypt | scrypt | Argon2 |
| :--- | :--- | :--- | :--- |
| Year | 1999 | 2009 | 2015 (PHC winner) |
| Cost Parameters | Work factor (time) | N (time), r (block), p (parallelism) | Time, Memory, Parallelism |
| Memory-Hard | No | Yes | Yes |
| Recommended | Legacy systems | Acceptable | **Preferred (new systems)** |

* **bcrypt:** Applies the Blowfish cipher's key schedule (64 iterations per round, repeated $2^{\text{cost}}$ times). Resistant to GPU acceleration due to data-dependent memory lookups. Output is always 60 characters including the salt.
* **scrypt:** Adds memory-hardness on top of PBKDF2. Allocates a large pseudo-random array in memory during computation — attackers need both time *and* memory, making custom ASIC attacks significantly more expensive.
* **Argon2:** Three variants — Argon2d (GPU resistance), Argon2i (side-channel resistance), Argon2id (hybrid, recommended). Independently tunable time cost ($t$), memory cost ($m$, in KiB), and parallelism ($p$).

---

## 💻 Usage / Example

```python
# ============================================================
# Hash Functions, HMAC & Password Hashing — Demonstrations
# ============================================================
# Prerequisites: pip install bcrypt argon2-cffi
# hashlib, hmac: Python standard library
# ============================================================

import hashlib
import hmac
import os
import bcrypt
from argon2 import PasswordHasher
from argon2.exceptions import VerifyMismatchError


# ------------------------------------------------------------------
# SECTION 1: General-Purpose Hash Functions
# ------------------------------------------------------------------

def demonstrate_hashing(message: str) -> None:
    """Compute MD5, SHA-1, SHA-256, and SHA-3 digests of a message."""
    data = message.encode("utf-8")

    digests = {
        "MD5    (BROKEN)": hashlib.md5(data).hexdigest(),
        "SHA-1  (BROKEN)": hashlib.sha1(data).hexdigest(),
        "SHA-256 (Secure)": hashlib.sha256(data).hexdigest(),
        "SHA-3-256 (Secure)": hashlib.sha3_256(data).hexdigest(),
    }

    print("=" * 60)
    print(f"Message: '{message}'")
    print("=" * 60)
    for name, digest in digests.items():
        print(f"  {name}: {digest}")
    print()


def demonstrate_avalanche(message: str) -> None:
    """
    Demonstrate the avalanche effect: a 1-character change in the
    input produces a completely different digest in SHA-256.
    """
    msg1 = message.encode("utf-8")
    msg2 = (message + ".").encode("utf-8")  # One character added

    h1 = hashlib.sha256(msg1).hexdigest()
    h2 = hashlib.sha256(msg2).hexdigest()

    # Count differing bits between the two 256-bit digests
    bits1 = bin(int(h1, 16))[2:].zfill(256)
    bits2 = bin(int(h2, 16))[2:].zfill(256)
    diff_bits = sum(b1 != b2 for b1, b2 in zip(bits1, bits2))

    print(f"Avalanche Effect (SHA-256):")
    print(f"  Original : {h1}")
    print(f"  Modified : {h2}")
    print(f"  Bits differing: {diff_bits}/256 ({diff_bits/256*100:.1f}%)")
    print()


# ------------------------------------------------------------------
# SECTION 2: HMAC — Message Authentication
# ------------------------------------------------------------------

def compute_hmac(key: bytes, message: str) -> str:
    """
    Compute HMAC-SHA256 for a message under a given key.
    Used to verify both integrity and authenticity.
    """
    return hmac.new(key, message.encode("utf-8"), hashlib.sha256).hexdigest()


def verify_hmac(key: bytes, message: str, expected_mac: str) -> bool:
    """
    Verify an HMAC using a constant-time comparison to prevent
    timing side-channel attacks.
    """
    computed = hmac.new(key, message.encode("utf-8"), hashlib.sha256).hexdigest()
    # hmac.compare_digest is constant-time — never use == for MAC comparison
    return hmac.compare_digest(computed, expected_mac)


def demonstrate_hmac() -> None:
    key = os.urandom(32)   # 256-bit secret key
    message = "Transfer $500 to account 9981"

    mac = compute_hmac(key, message)
    print(f"HMAC-SHA256 Demo:")
    print(f"  Message : {message}")
    print(f"  MAC     : {mac}")

    # Tampered message — MAC will not verify
    tampered = "Transfer $9000 to account 9981"
    is_valid_original = verify_hmac(key, message, mac)
    is_valid_tampered = verify_hmac(key, tampered, mac)

    print(f"  Verify original  : {is_valid_original}")   # True
    print(f"  Verify tampered  : {is_valid_tampered}")   # False
    print()


# ------------------------------------------------------------------
# SECTION 3: Password Hashing
# ------------------------------------------------------------------

def demonstrate_bcrypt(password: str) -> None:
    """
    Hash a password with bcrypt (cost factor 12).
    bcrypt automatically generates and stores a random salt.
    """
    pwd_bytes = password.encode("utf-8")

    # cost=12 means 2^12 = 4096 iterations of Blowfish key schedule
    hashed = bcrypt.hashpw(pwd_bytes, bcrypt.gensalt(rounds=12))

    is_correct = bcrypt.checkpw(pwd_bytes, hashed)
    is_wrong   = bcrypt.checkpw(b"wrongpassword", hashed)

    print(f"bcrypt (cost=12):")
    print(f"  Hash           : {hashed.decode()}")
    print(f"  Correct verify : {is_correct}")   # True
    print(f"  Wrong verify   : {is_wrong}")     # False
    print()


def demonstrate_argon2(password: str) -> None:
    """
    Hash a password with Argon2id (recommended variant).
    Parameters: time_cost=3, memory_cost=65536 KiB (64 MiB), parallelism=2
    """
    ph = PasswordHasher(
        time_cost=3,          # Number of iterations
        memory_cost=65536,    # Memory usage in KiB (64 MiB)
        parallelism=2,        # Number of parallel threads
        hash_len=32,          # Output length in bytes
        salt_len=16           # Salt length in bytes
    )

    hashed = ph.hash(password)

    try:
        ph.verify(hashed, password)
        correct = True
    except VerifyMismatchError:
        correct = False

    try:
        ph.verify(hashed, "wrongpassword")
        wrong = True
    except VerifyMismatchError:
        wrong = False

    print(f"Argon2id (t=3, m=64MiB, p=2):")
    print(f"  Hash           : {hashed}")
    print(f"  Correct verify : {correct}")   # True
    print(f"  Wrong verify   : {wrong}")     # False
    print()


# ------------------------------------------------------------------
# SECTION 4: Why SHA-256 Alone Is Insufficient for Passwords
# ------------------------------------------------------------------

def naive_sha256_password(password: str) -> str:
    """
    ❌ INSECURE — Do NOT use this for real passwords.
    SHA-256 is fast: ~500 million hashes/second on a GPU.
    An attacker can brute-force common passwords in milliseconds.
    """
    return hashlib.sha256(password.encode("utf-8")).hexdigest()


# ============================================================
# MAIN DEMONSTRATION
# ============================================================

if __name__ == "__main__":
    # Section 1: General hashing
    demonstrate_hashing("Hello, Cryptography!")
    demonstrate_avalanche("Hello, Cryptography!")

    # Section 2: HMAC
    demonstrate_hmac()

    # Section 3: Password hashing
    password = "MySecurePassword123!"
    demonstrate_bcrypt(password)
    demonstrate_argon2(password)

    # Section 4: Naive comparison
    print("SHA-256 (naive, INSECURE for passwords):")
    print(f"  {naive_sha256_password(password)}")
    print("  ⚠ A GPU can compute ~500M of these per second.")

# ============================================================
# Summary of Complexities:
#   SHA-256 hash       : O(n) in message length — fast by design
#   HMAC-SHA256        : O(n) — two SHA-256 calls
#   bcrypt (cost=12)   : O(2^12) per guess — ~200ms on CPU
#   Argon2id (t=3)     : O(t * m) per guess — tunable to target hardware
#   MD5 collision      : O(2^18) — seconds on commodity hardware (BROKEN)
# ============================================================
```

> **Key Insight:** The goal of password hashing is to be *intentionally slow*. SHA-256 computes in nanoseconds — bcrypt and Argon2 compute in hundreds of milliseconds by design. For an attacker iterating billions of guesses, this difference is decisive.

---

## References

* [Wang, X. & Yu, H. (2005) — "How to Break MD5 and Other Hash Functions"](https://link.springer.com/chapter/10.1007/11426639_2) — Original paper demonstrating practical MD5 collision attacks using differential cryptanalysis.
* [Stevens et al. (2017) — "The First Collision for Full SHA-1 (SHAttered)"](https://shattered.io) — Google/CWI practical SHA-1 collision; includes two distinct PDFs with identical SHA-1 digests.
* [RFC 2104 — HMAC: Keyed-Hashing for Message Authentication](https://datatracker.ietf.org/doc/html/rfc2104) — Original HMAC specification by Bellare, Canetti & Krawczyk.
* [RFC 9106 — Argon2 Memory-Hard Function for Password Hashing](https://datatracker.ietf.org/doc/html/rfc9106) — Official RFC for Argon2 (PHC winner); covers Argon2d, Argon2i, and Argon2id variants.
* [NIST SP 800-107 Rev. 1 — Recommendation for Applications Using Approved Hash Algorithms](https://csrc.nist.gov/publications/detail/sp/800-107/rev-1/final) — NIST guidance on hash function selection for security applications.
* [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) — Practical guidance on bcrypt, scrypt, and Argon2 parameter selection.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Chapter 6 (Hash Functions and Applications).
* *Cryptography and Network Security* — William Stallings, Chapter 11 (Cryptographic Hash Functions).
# Cryptographic Hash Attacks

## 📋 Summary

* **Core Concept:** Hash attack techniques attempt to recover plaintext from a digest, forge authentication tokens, or exploit implementation timing. They range from exhaustive search (brute force) to mathematically elegant exploits (birthday attack), and vary significantly in their computational and storage trade-offs.

> **Takeaways:**
> - Brute force and dictionary attacks are the most direct — their effectiveness depends entirely on the cost of computing one hash and the quality of the password.
> - Rainbow tables trade storage for time, eliminating the need to recompute hashes at query time; salting defeats them completely.
> - The birthday attack is a mathematical bound, not a flaw — it sets the minimum collision resistance of any $n$-bit hash at $O(2^{n/2})$.
> - Timing attacks target the **implementation**, not the algorithm — a function can be mathematically secure yet leak secrets through execution time differences.
> - Defenses are well understood: slow password hashing, unique salts, memory-hard functions, and constant-time comparison.

---

## 📖 Definition

* **Brute Force Attack:** A systematic, exhaustive search over all possible inputs in the keyspace until a hash match is found. Makes no assumptions about the target — it is guaranteed to succeed given sufficient time.

* **Dictionary Attack:** A targeted search that tests a precomputed list of likely plaintexts (a "dictionary") against a target hash. More efficient than brute force because it exploits the predictability of human-chosen passwords.

* **Rainbow Table:** A precomputed data structure that stores chains of alternating hash and reduction operations, enabling time–space trade-off lookups. Allows hash reversal in $O(t)$ table lookups rather than $O(2^n)$ recomputation, at the cost of $O(t \cdot m)$ storage.

* **Reduction Function $R$:** A function $R : \{0,1\}^n \rightarrow \mathcal{P}$ that maps a hash digest back to the plaintext space $\mathcal{P}$. It is *not* the inverse of $H$ — it produces a different plaintext in the same search space.

* **Rainbow Table Chain:** A sequence $p_0 \xrightarrow{H} h_0 \xrightarrow{R_0} p_1 \xrightarrow{H} h_1 \xrightarrow{R_1} \cdots \xrightarrow{H} h_{t-1}$ where different reduction functions $R_i$ are used at each step to reduce chain merges.

* **Birthday Attack:** An attack exploiting the birthday paradox — the probability that two randomly chosen values share the same hash reaches 50% after approximately $2^{n/2}$ samples, not $2^n$. Used to find collisions in hash functions.

* **Birthday Paradox:** In a set of $k$ uniformly random values drawn from a space of size $N$, the probability of a collision exceeds 50% when $k \approx \sqrt{N}$. For an $n$-bit hash: $k \approx 2^{n/2}$.

* **Timing Attack:** A side-channel attack that recovers secret information by measuring the time taken by cryptographic operations. Exploits data-dependent branching or early-exit logic in implementations rather than the mathematical structure of the algorithm.

* **Salt:** A random value, unique per password, concatenated to the plaintext before hashing: $H(\text{salt} \,\|\, \text{password})$. Salts defeat precomputed attacks (rainbow tables, dictionary tables) by ensuring each hash is computed independently.

* **Requirements for a Successful Brute Force Attack:**
    * The hash function must be fast to compute (unfavorable for bcrypt/Argon2).
    * No rate limiting, account lockout, or slow hash function in the target system.
    * Sufficient computational resources (GPU cluster, ASICs for specific algorithms).

* **Requirements for a Successful Rainbow Table Attack:**
    * The target password hash must be unsalted.
    * A precomputed table for the specific hash algorithm and character set must exist or be buildable.
    * The plaintext must fall within the covered keyspace.

---

## 📊 Complexity Analysis

| Notation | Name | Growth Rate |
| :--- | :--- | :--- |
| $O(1)$ | Constant | Excellent |
| $O(\log n)$ | Logarithmic | Very Good |
| $O(n)$ | Linear | Good |
| $O(n^2)$ | Quadratic | Poor |

### Attack Complexity Comparison

| Attack | Time Complexity | Space Complexity | Defeated By |
| :--- | :--- | :--- | :--- |
| Brute Force | $O(2^n)$ | $O(1)$ | Slow hash (bcrypt/Argon2) |
| Dictionary | $O(\|D\|)$ | $O(\|D\|)$ | Salt + slow hash |
| Rainbow Table Lookup | $O(t)$ — table lookup | $O(t \cdot m)$ — precomputed | Salt (per-password unique) |
| Rainbow Table Build | $O(t \cdot m)$ | $O(t \cdot m)$ | N/A (offline) |
| Birthday Attack | $O(2^{n/2})$ | $O(2^{n/2})$ | Longer digest ($n \geq 256$) |
| Timing Attack | $O(k)$ — measurements | $O(1)$ | Constant-time comparison |

* **Worst-Case ($O$) — Brute Force:** $O(2^n)$ where $n$ is the bit length of the password space; for an 8-character lowercase password, $n = \log_2(26^8) \approx 38$ bits.
* **Best-Case ($\Omega$) — Dictionary:** $\Omega(1)$ if the first entry matches.
* **Average-Case ($\Theta$) — Rainbow Table Lookup:** $\Theta(t)$ chain traversals of length $t$, each requiring one hash and one reduction.

---

## ❓ Why We Study These Attacks

* **Password database breaches are routine:** Knowing the mechanics of each attack explains why salted, slow hashing is the minimum requirement for any credential store.
* **Birthday bound sets algorithm requirements:** Understanding why $n/2$ bits of collision resistance is the practical ceiling directly informs hash function selection (SHA-256 gives 128-bit collision resistance, not 256-bit).
* **Timing attacks are implementation bugs, not algorithm bugs:** A developer using a textbook-secure MAC can still be exploited if they compare tokens with `==`. This attack class is critical for anyone writing authentication code.
* **Rainbow table history shaped modern standards:** The NTLM password hashes in leaked Windows domain databases were trivially reversed via rainbow tables — this directly motivated the adoption of bcrypt in Linux PAM and PBKDF2 in PKCS#5.

---

## ⚙️ How It Works

### Brute Force Attack

1. **Step 1 — Define the keyspace:** Enumerate all strings over the target alphabet up to length $\ell$. For alphanumeric + symbols (95 printable ASCII), an 8-character password yields $95^8 \approx 6.6 \times 10^{15}$ candidates.
2. **Step 2 — Hash each candidate:** Compute $H(\text{candidate})$ for each entry. A GPU running MD5 achieves roughly $10^{10}$ hashes/second; Argon2id at default parameters reduces this to $\approx 10^3$/second.
3. **Step 3 — Compare digest:** If $H(\text{candidate}) = h_\text{target}$, the plaintext is recovered.
4. **Step 4 — Outcome:** Guaranteed eventual success, but time scales exponentially with password length and hash cost.

### Dictionary Attack

1. **Step 1 — Obtain a wordlist:** Use a corpus such as RockYou (14 million entries), combined with rule-based mutations (e.g., `password` → `P@ssw0rd`, `password123`).
2. **Step 2 — Apply rules:** Tools such as Hashcat apply mangling rules (capitalize, substitute `a→@`, append digits) to multiply effective dictionary size.
3. **Step 3 — Hash and compare:** For each mutated candidate, compute $H(\text{candidate})$ and compare to the target digest.
4. **Step 4 — Salt check:** If the hash is salted, concatenate the known salt before hashing: $H(\text{salt} \,\|\, \text{candidate})$.

### Rainbow Table Attack

1. **Step 1 — Build chains (offline):** For each starting plaintext $p_0$, compute a chain of length $t$ by alternating $H$ and $R_i$. Store only the start and end of each chain: $(p_0, p_t)$.
2. **Step 2 — Sort the table:** Sort chains by their endpoint $p_t$ for fast lookup.
3. **Step 3 — Query (online):** Given target hash $h$, apply successive reduction-hash pairs: $h \xrightarrow{R_{t-1}} p \xrightarrow{H} h' \xrightarrow{R_{t-2}} \cdots$ until an endpoint match is found in the table.
4. **Step 4 — Reconstruct:** When a match is found, regenerate the chain from its stored start point to locate the exact plaintext that produced $h$.
5. **Step 5 — Salt defeats this:** A unique salt per password means a separate table would need to be built per salt value — computationally infeasible at scale.

### Birthday Attack on Hash Functions

1. **Step 1 — Sample randomly:** Generate a large set of random messages $\{m_1, m_2, \ldots, m_k\}$ and compute their digests.
2. **Step 2 — Apply birthday bound:** By the birthday paradox, a collision $H(m_i) = H(m_j)$ is expected when $k \approx 2^{n/2}$ for an $n$-bit digest. For MD5 ($n=128$): $2^{64}$ samples needed in theory — reduced to $\approx 2^{18}$ in practice via differential cryptanalysis.
3. **Step 3 — Exploit the collision:** In a chosen-prefix collision, an attacker constructs two semantically different documents (e.g., two X.509 certificates) that produce the same digest, enabling signature forgery.
4. **Step 4 — Implication for digest length:** A 128-bit hash provides only 64-bit collision resistance. SHA-256 provides 128-bit collision resistance — considered the practical minimum for security today.

### Timing Attack

1. **Step 1 — Identify a comparison:** Locate a MAC or hash comparison in the target code (e.g., `if token == expected_token`).
2. **Step 2 — Observe early exit:** A naive string comparison returns `False` on the first mismatched byte. The comparison takes longer for inputs that share a longer common prefix with the secret.
3. **Step 3 — Measure execution time:** Send requests with varying first bytes and measure response latency. The byte value that consistently takes longest has the highest probability of being correct.
4. **Step 4 — Iterate byte by byte:** Repeat for each position, recovering the secret one byte at a time in $O(256 \times n)$ measurements — far fewer than $O(256^n)$ brute force.
5. **Step 5 — Defense:** Use constant-time comparison functions (`hmac.compare_digest` in Python, `crypto.timingSafeEqual` in Node.js) that always compare all bytes regardless of early mismatch.

---

## 💻 Usage / Example

```python
# ============================================================
# Hash Attacks — Demonstrations & Defenses
# ============================================================
# hashlib, hmac, time, os: Python standard library
# ============================================================

import hashlib
import hmac
import os
import time
import itertools
import string


# ------------------------------------------------------------------
# ATTACK 1: Brute Force Attack (small keyspace demo)
# ------------------------------------------------------------------

def brute_force_md5(target_hash: str, max_length: int = 4) -> str | None:
    """
    Attempt to reverse an MD5 hash by exhaustively trying all
    combinations of lowercase letters up to max_length characters.

    ⚠ Only feasible for very short passwords and fast hash functions.
    Against bcrypt/Argon2, this is computationally impractical.
    """
    charset = string.ascii_lowercase  # 26 characters

    for length in range(1, max_length + 1):
        for candidate_tuple in itertools.product(charset, repeat=length):
            candidate = "".join(candidate_tuple)
            digest = hashlib.md5(candidate.encode()).hexdigest()
            if digest == target_hash:
                return candidate  # Plaintext recovered

    return None  # Not found within keyspace


# ------------------------------------------------------------------
# ATTACK 2: Dictionary Attack
# ------------------------------------------------------------------

def dictionary_attack(target_hash: str, wordlist: list[str],
                       salt: bytes = b"") -> str | None:
    """
    Test each entry in a wordlist against a target hash.
    Supports optional salt: H(salt || password).

    Args:
        target_hash: Hex-encoded target digest (SHA-256).
        wordlist:    List of candidate plaintext passwords.
        salt:        Known salt bytes (empty if unsalted).

    Returns:
        Matched plaintext, or None if not found.
    """
    for word in wordlist:
        data = salt + word.encode("utf-8")
        digest = hashlib.sha256(data).hexdigest()
        if digest == target_hash:
            return word

    return None


# ------------------------------------------------------------------
# ATTACK 3: Birthday Attack Demonstration (Toy Hash)
# ------------------------------------------------------------------

def truncated_hash(message: str, bits: int = 16) -> int:
    """
    Compute a truncated SHA-256 digest — simulates a weak hash
    function with only `bits` of output for birthday demo purposes.
    """
    full = int(hashlib.sha256(message.encode()).hexdigest(), 16)
    mask = (1 << bits) - 1
    return full & mask


def birthday_attack_demo(bits: int = 16) -> tuple[str, str] | None:
    """
    Find a collision in a truncated hash by random sampling.
    Expected collision at ~2^(bits/2) = 2^8 = 256 samples for bits=16.

    Returns a colliding pair (m1, m2) where m1 != m2 and H(m1) == H(m2).
    """
    seen: dict[int, str] = {}
    attempt = 0

    while True:
        msg = f"message_{attempt}_{os.urandom(4).hex()}"
        h = truncated_hash(msg, bits)

        if h in seen and seen[h] != msg:
            return seen[h], msg  # Collision found

        seen[h] = msg
        attempt += 1


# ------------------------------------------------------------------
# ATTACK 4: Timing Attack Simulation & Defense
# ------------------------------------------------------------------

def insecure_token_compare(user_token: str, secret_token: str) -> bool:
    """
    ❌ VULNERABLE — early-exit string comparison.
    Leaks how many leading bytes the user token shares with the secret.
    """
    return user_token == secret_token


def secure_token_compare(user_token: str, secret_token: str) -> bool:
    """
    ✅ SECURE — constant-time comparison.
    Always compares all bytes regardless of where the first mismatch occurs.
    """
    return hmac.compare_digest(
        user_token.encode("utf-8"),
        secret_token.encode("utf-8")
    )


def simulate_timing_attack(secret: str, candidates: list[str],
                            trials: int = 500) -> dict[str, float]:
    """
    Measure average comparison time for each candidate against the secret.
    Demonstrates that insecure comparison leaks prefix information via latency.

    Returns a dict mapping each candidate to its average comparison time (ns).
    """
    timings: dict[str, float] = {}

    for candidate in candidates:
        total = 0.0
        for _ in range(trials):
            start = time.perf_counter_ns()
            insecure_token_compare(candidate, secret)
            total += time.perf_counter_ns() - start
        timings[candidate] = total / trials

    return timings


# ------------------------------------------------------------------
# DEFENSE: Proper Salted Hashing
# ------------------------------------------------------------------

def hash_password_with_salt(password: str) -> tuple[bytes, str]:
    """
    Hash a password with a unique random salt using SHA-256.
    (In production, use bcrypt or Argon2 — not raw SHA-256.)

    Returns: (salt, hex_digest)
    """
    salt = os.urandom(16)   # 128-bit cryptographically random salt
    digest = hashlib.sha256(salt + password.encode("utf-8")).hexdigest()
    return salt, digest


def verify_salted_password(password: str, salt: bytes, stored_hash: str) -> bool:
    computed = hashlib.sha256(salt + password.encode("utf-8")).hexdigest()
    return hmac.compare_digest(computed, stored_hash)  # Constant-time


# ============================================================
# MAIN DEMONSTRATION
# ============================================================

if __name__ == "__main__":

    # --- Brute Force ---
    target = hashlib.md5("cat".encode()).hexdigest()
    print(f"Brute Force MD5 target: {target}")
    result = brute_force_md5(target)
    print(f"Recovered plaintext   : {result}\n")

    # --- Dictionary Attack (unsalted) ---
    wordlist = ["password", "123456", "dragon", "letmein", "sunshine", "secret"]
    secret_plain = "sunshine"
    target_hash = hashlib.sha256(secret_plain.encode()).hexdigest()

    found = dictionary_attack(target_hash, wordlist)
    print(f"Dictionary Attack (unsalted):")
    print(f"  Target hash : {target_hash}")
    print(f"  Found       : {found}\n")

    # --- Dictionary Attack (salted — fails without the salt) ---
    salt = os.urandom(16)
    salted_hash = hashlib.sha256(salt + secret_plain.encode()).hexdigest()
    found_salted = dictionary_attack(salted_hash, wordlist)  # No salt passed
    found_with_salt = dictionary_attack(salted_hash, wordlist, salt)
    print(f"Dictionary Attack (salted):")
    print(f"  Without salt (attacker has no salt) : {found_salted}")
    print(f"  With known salt                     : {found_with_salt}\n")

    # --- Birthday Attack ---
    print("Birthday Attack (16-bit truncated hash):")
    m1, m2 = birthday_attack_demo(bits=16)
    h1 = truncated_hash(m1, 16)
    h2 = truncated_hash(m2, 16)
    print(f"  Collision found: H('{m1}') = H('{m2}') = {h1}")
    print(f"  Verify: {h1 == h2}\n")

    # --- Timing Attack ---
    secret_token = "auth_token_secret_xyz"
    test_candidates = [
        "aaaaaaaaaaaaaaaaaaaaaa",   # No prefix match
        "auth_token_secret_xy0",   # Almost correct (last char wrong)
        "auth_token_secret_xyz",   # Correct
    ]

    print("Timing Attack Simulation (500 trials each):")
    timings = simulate_timing_attack(secret_token, test_candidates)
    for candidate, avg_ns in timings.items():
        print(f"  '{candidate[:25]}...' avg: {avg_ns:.1f} ns")
    print("  (Higher time = longer common prefix — leaks secret structure)\n")

    # --- Secure Comparison ---
    print("Secure constant-time comparison:")
    print(f"  Correct : {secure_token_compare(secret_token, secret_token)}")
    print(f"  Wrong   : {secure_token_compare('wrong_token', secret_token)}")

# ============================================================
# Complexities:
#   Brute Force       : O(|alphabet|^length) per hash call
#   Dictionary        : O(|wordlist|) with rule mutations
#   Rainbow Lookup    : O(t) chain steps — O(t*m) precompute
#   Birthday Attack   : O(2^(n/2)) random samples
#   Timing Attack     : O(256 * n) measurements (vs O(256^n) BF)
# ============================================================
```

> **Key Insight:** Every attack above has a well-defined, low-cost defense. Salting defeats rainbow tables and dictionary precomputation. Slow hashing defeats brute force. Longer digests push the birthday bound out of reach. Constant-time comparison closes the timing side-channel. None of these defenses require a new algorithm — they require correct implementation.

---

## References

* [Oechslin, P. (2003) — "Making a Faster Cryptanalytic Time–Memory Trade-Off"](https://link.springer.com/chapter/10.1007/978-3-540-45146-4_36) — Original rainbow table paper; introduces per-step reduction functions to eliminate chain merges.
* [Hellman, M.E. (1980) — "A Cryptanalytic Time–Memory Trade-Off"](https://ieeexplore.ieee.org/document/1056220) — Foundational time–memory trade-off attack predating rainbow tables.
* [Kocher, P. (1996) — "Timing Attacks on Implementations of Diffie–Hellman, RSA, DSS, and Other Systems"](https://link.springer.com/chapter/10.1007/3-540-68697-5_9) — Seminal paper establishing timing attacks as a practical threat class.
* [RFC 6151 — Updated Security Considerations for MD5](https://datatracker.ietf.org/doc/html/rfc6151) — Formally documents MD5's broken status for security-critical use.
* [Python `hmac` Documentation — `compare_digest`](https://docs.python.org/3/library/hmac.html#hmac.compare_digest) — Reference for constant-time comparison in Python.
* [Hashcat Rules Documentation](https://hashcat.net/wiki/doku.php?id=rule_based_attack) — Comprehensive reference for dictionary mutation rules used in real-world attacks.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Chapter 6 (Hash Functions).
* *The Web Application Hacker's Handbook* — Stuttard & Pinto, Chapter 7 (Attacking Authentication).
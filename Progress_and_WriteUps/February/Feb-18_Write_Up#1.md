# Mathematical Foundations of Cryptography

## 📋 Summary

* **Core Concept:** Modern cryptographic systems — including RSA, Diffie-Hellman, and elliptic curve cryptography — are built upon a set of mathematical structures: modular arithmetic, prime numbers, the greatest common divisor, Euler's totient function, and the discrete logarithm problem. Their security does not rely on secrecy of the algorithm, but on the computational hardness of reversing specific mathematical operations.

> **Takeaways:** Each of these five concepts plays a direct role in at least one major cryptographic primitive. Modular arithmetic defines the number space in which all operations occur. Prime numbers and factorization underpin RSA's security assumption. The GCD drives key generation via the Extended Euclidean Algorithm. Euler's totient function determines the structure of modular inverses. The discrete logarithm problem is the hardness assumption behind Diffie-Hellman and elliptic curve schemes. A weakness in any one of these — or a polynomial-time algorithm solving them (e.g., via quantum computing) — would break the cryptosystems that depend on them.


---

## 1. Modular Arithmetic

### 📖 Definition

* **Modulus ($n$):** A positive integer $n$ that defines the size of the finite number space $\mathbb{Z}_n = \{0, 1, 2, \ldots, n-1\}$.
* **Congruence:** Two integers $a$ and $b$ are congruent modulo $n$, written $a \equiv b \pmod{n}$, if and only if $n \mid (a - b)$ — that is, $n$ divides their difference evenly.
* **Modular Inverse:** An integer $a^{-1}$ such that $a \cdot a^{-1} \equiv 1 \pmod{n}$. This exists if and only if $\gcd(a, n) = 1$ (i.e., $a$ and $n$ are coprime).
* **Modular Exponentiation:** Computing $a^e \pmod{n}$ efficiently using repeated squaring, which is the core operation in RSA and Diffie-Hellman.
* **Requirements:**
    * $n \geq 2$ for the modulus to define a meaningful number space.
    * A modular inverse exists for $a$ only when $\gcd(a, n) = 1$.
    * All arithmetic operations (addition, subtraction, multiplication) are closed within $\mathbb{Z}_n$.

### 📊 Complexity Analysis

| Notation | Name | Growth Rate |
| :--- | :--- | :--- |
| $O(1)$ | Constant | Excellent |
| $O(\log n)$ | Logarithmic | Very Good |
| $O((\log n)^2)$ | Log-Squared | Good |
| $O((\log n)^3)$ | Log-Cubed | Acceptable |

* **Modular Reduction ($a \bmod n$) — $O(\log n)$:** Division of two $k$-bit numbers where $k = \log_2 n$.
* **Modular Multiplication — $O((\log n)^2)$:** Multiply two $k$-bit numbers, then reduce.
* **Modular Exponentiation ($a^e \bmod n$) — Worst-Case ($O$):** $O((\log e) \cdot (\log n)^2)$ using fast exponentiation (square-and-multiply). This is efficient even for 2048-bit exponents.
* **Best-Case ($\Omega$):** $\Omega(\log e)$ squarings when no multiplications are required (e.g., $e$ is a power of 2).
* **Average-Case ($\Theta$):** $\Theta(\log e)$ squarings and $\approx \frac{\log e}{2}$ multiplications.

### ❓ Why We Use It

* **Finite arithmetic:** Modular arithmetic keeps numbers bounded within a fixed range, which is essential for digital computation.
* **Trapdoor functions:** Operations like modular exponentiation are easy to compute forward but computationally hard to reverse without the key.
* **Foundation for all public-key systems:** RSA, Diffie-Hellman, DSA, and ECC all perform their core operations within $\mathbb{Z}_n$ or a related finite group.

### ⚙️ How It Works

1. **Step 1:** Select a modulus $n$.
2. **Step 2:** Reduce any integer $a$ to its representative in $[0, n-1]$: $a \bmod n = a - n \lfloor a/n \rfloor$.
3. **Step 3 — Modular Exponentiation (Square-and-Multiply):** To compute $a^e \bmod n$, express $e$ in binary. For each bit from most significant to least significant: square the running result, and multiply by $a$ if the current bit is 1, then reduce modulo $n$:
   $$\text{result} = a^e \bmod n, \quad T(k) \approx c \cdot \log e \cdot (\log n)^2$$
4. **Step 4:** The result is always in $\{0, 1, \ldots, n-1\}$.

---

## 2. Prime Numbers and Factorization

### 📖 Definition

* **Prime Number:** An integer $p > 1$ whose only positive divisors are $1$ and $p$ itself.
* **Composite Number:** An integer $n > 1$ that has at least one divisor other than $1$ and $n$; equivalently, $n = a \cdot b$ for some $1 < a, b < n$.
* **Integer Factorization:** The problem of expressing a composite integer $n$ as a product of prime factors: $n = p_1^{e_1} \cdot p_2^{e_2} \cdots p_k^{e_k}$.
* **Fundamental Theorem of Arithmetic:** Every integer $n > 1$ has a unique prime factorization (up to ordering of factors).
* **RSA Security Assumption:** Given a product $n = p \cdot q$ of two large primes, it is computationally infeasible to recover $p$ and $q$ efficiently. No polynomial-time classical algorithm for this problem is known.
* **Requirements:**
    * For RSA, primes $p$ and $q$ must each be at least 1024 bits; 2048 bits or more is the current standard.
    * Primes must be generated using a cryptographically secure primality test (e.g., Miller-Rabin).
    * $p$ and $q$ must be chosen independently and must not be close to each other in value.

### 📊 Complexity Analysis

| Algorithm | Complexity | Notes |
| :--- | :--- | :--- |
| Trial Division | $O(\sqrt{n})$ | Impractical for large $n$ |
| Miller-Rabin (primality test) | $O(k \cdot (\log n)^2)$ | $k$ = number of rounds; probabilistic |
| General Number Field Sieve (factoring) | $O\!\left(e^{(\log n)^{1/3}(\log \log n)^{2/3}}\right)$ | Sub-exponential; best classical algorithm |
| Shor's Algorithm (quantum) | $O((\log n)^3)$ | Polynomial-time; breaks RSA |

* **Worst-Case ($O$) — Factoring:** Sub-exponential via GNFS; effectively infeasible for $n \geq 2048$ bits on classical hardware.
* **Best-Case ($\Omega$) — Primality Test:** $\Omega((\log n)^2)$ — at minimum, one modular exponentiation must be performed.
* **Average-Case ($\Theta$) — Miller-Rabin with $k$ rounds:** $\Theta(k \cdot (\log n)^2)$; the probability of a false positive is at most $4^{-k}$.

### ❓ Why We Use It

* **RSA key generation:** Two large primes $p$ and $q$ are multiplied to form the public modulus $n = p \cdot q$. The security of RSA rests on the hardness of factoring $n$ back into $p$ and $q$.
* **Prime generation:** Primes are generated efficiently using probabilistic tests (Miller-Rabin), which are fast even for 4096-bit numbers.
* **Distribution of primes:** By the Prime Number Theorem, the number of primes up to $N$ is approximately $N / \ln N$, ensuring that large primes are dense enough to find quickly.

### ⚙️ How It Works — Miller-Rabin Primality Test

1. **Step 1:** Given an odd integer $n > 2$, write $n - 1 = 2^s \cdot d$ where $d$ is odd.
2. **Step 2:** Choose a random witness $a$ with $2 \leq a \leq n - 2$.
3. **Step 3:** Compute $x = a^d \bmod n$.
4. **Step 4:** If $x = 1$ or $x = n - 1$, $n$ passes this round (probably prime). Otherwise, square $x$ up to $s - 1$ times; if $x = n - 1$ at any point, $n$ passes. If $x$ never reaches $n - 1$, $n$ is **composite**.
5. **Step 5:** Repeat for $k$ independent witnesses. If all pass:
   $$P(\text{false positive}) \leq 4^{-k}$$

---

## 3. Greatest Common Divisor (GCD)

### 📖 Definition

* **Greatest Common Divisor:** For integers $a$ and $b$ (not both zero), $\gcd(a, b)$ is the largest positive integer that divides both $a$ and $b$.
* **Coprime (Relatively Prime):** Two integers $a$ and $b$ are coprime if $\gcd(a, b) = 1$. This is the condition required for a modular inverse of $a$ modulo $b$ to exist.
* **Euclidean Algorithm:** A recursive algorithm for computing $\gcd(a, b)$ based on the identity $\gcd(a, b) = \gcd(b, a \bmod b)$, with base case $\gcd(a, 0) = a$.
* **Extended Euclidean Algorithm:** An extension that, in addition to computing $\gcd(a, b)$, finds integers $x$ and $y$ (Bézout coefficients) satisfying:
  $$ax + by = \gcd(a, b)$$
  When $\gcd(a, b) = 1$, this directly yields $a^{-1} \bmod b = x \bmod b$.
* **Requirements:**
    * Inputs must be non-negative integers (or their absolute values are used).
    * The Extended Euclidean Algorithm is required for modular inverse computation in RSA key generation.

### 📊 Complexity Analysis

| Notation | Name | Growth Rate |
| :--- | :--- | :--- |
| $O(\log(\min(a,b)))$ | Logarithmic | Very Good |

* **Worst-Case ($O$):** $O(\log(\min(a, b)))$ — occurs when $a$ and $b$ are consecutive Fibonacci numbers, which maximize the number of recursive steps (Lamé's Theorem).
* **Best-Case ($\Omega$):** $\Omega(1)$ — when $b \mid a$, the algorithm terminates in one step.
* **Average-Case ($\Theta$):** $\Theta(\log n)$ where $n = \min(a, b)$.

### ❓ Why We Use It

* **RSA key generation:** The public exponent $e$ must satisfy $\gcd(e, \phi(n)) = 1$, verified using the Euclidean Algorithm.
* **Modular inverse:** The private exponent $d$ in RSA is computed as $d = e^{-1} \bmod \phi(n)$ using the Extended Euclidean Algorithm.
* **Key validation:** Ensures that selected parameters are coprime and that the cryptosystem is correctly constructed.

### ⚙️ How It Works

1. **Step 1:** Given inputs $a \geq b > 0$, apply the recurrence: $\gcd(a, b) = \gcd(b, a \bmod b)$.
2. **Step 2:** Repeat until $b = 0$; at that point, $\gcd = a$.
3. **Step 3 — Extended version:** Track back-substitution of Bézout coefficients to find $x, y$ such that $ax + by = \gcd(a, b)$:
   $$T(a, b) \approx c \cdot \log(\min(a, b))$$
4. **Step 4:** If $\gcd(a, b) = 1$, then $x \bmod b$ is the modular inverse of $a$ modulo $b$.

---

## 4. Euler's Totient Function

### 📖 Definition

* **Euler's Totient Function $\phi(n)$:** For a positive integer $n$, $\phi(n)$ counts the number of integers in $\{1, 2, \ldots, n\}$ that are coprime to $n$ — i.e., integers $k$ with $\gcd(k, n) = 1$.
* **Euler's Theorem:** For any integer $a$ with $\gcd(a, n) = 1$:
  $$a^{\phi(n)} \equiv 1 \pmod{n}$$
* **Fermat's Little Theorem (special case):** When $n = p$ is prime and $\gcd(a, p) = 1$:
  $$a^{p-1} \equiv 1 \pmod{p}$$
* **RSA Totient:** For $n = p \cdot q$ (two distinct primes):
  $$\phi(n) = (p-1)(q-1)$$
* **Requirements:**
    * $p$ and $q$ must be prime for the formula $\phi(pq) = (p-1)(q-1)$ to hold.
    * $\phi(n)$ must be kept **secret** in RSA; it is used to compute the private key $d$.
    * The public exponent $e$ must satisfy $1 < e < \phi(n)$ and $\gcd(e, \phi(n)) = 1$.

### 📊 Complexity Analysis

| Operation | Complexity | Notes |
| :--- | :--- | :--- |
| Compute $\phi(p)$ for prime $p$ | $O(1)$ | $\phi(p) = p - 1$ by definition |
| Compute $\phi(pq)$ given $p, q$ | $O(1)$ | $(p-1)(q-1)$ directly |
| Compute $\phi(n)$ by factoring $n$ | Sub-exponential | Requires factoring $n$ first |

* **Worst-Case ($O$) — Given factorization:** $O(k \cdot \log n)$ where $k$ is the number of distinct prime factors.
* **Best-Case ($\Omega$) — Prime input:** $\Omega(1)$ — $\phi(p) = p - 1$ is trivial.
* **Average-Case ($\Theta$) — RSA context:** $\Theta(1)$ since $p$ and $q$ are known during key generation.

### ❓ Why We Use It

* **RSA private key derivation:** $d = e^{-1} \bmod \phi(n)$ is computed using the Extended Euclidean Algorithm. Without $\phi(n)$, computing $d$ from $e$ and $n$ alone requires factoring $n$.
* **Correctness of RSA decryption:** Euler's Theorem guarantees that $(m^e)^d \equiv m^{ed} \equiv m \pmod{n}$ when $ed \equiv 1 \pmod{\phi(n)}$.
* **Security guarantee:** An attacker who cannot factor $n$ cannot compute $\phi(n)$ and therefore cannot derive $d$ from the public key $(e, n)$.

### ⚙️ How It Works

1. **Step 1:** Factor $n$ into its prime power components: $n = p_1^{e_1} \cdot p_2^{e_2} \cdots p_k^{e_k}$.
2. **Step 2:** Apply the multiplicative formula:
   $$\phi(n) = n \prod_{p \mid n} \left(1 - \frac{1}{p}\right)$$
3. **Step 3 — RSA shortcut:** Since $n = p \cdot q$:
   $$\phi(n) = (p-1)(q-1) = n - p - q + 1$$
4. **Step 4:** Use $\phi(n)$ to compute $d = e^{-1} \bmod \phi(n)$ via the Extended Euclidean Algorithm.

---

## 5. Discrete Logarithm Problem

### 📖 Definition

* **Discrete Logarithm:** Given a cyclic group $G$ of order $q$, a generator $g$, and an element $h \in G$, the discrete logarithm of $h$ base $g$ is the unique integer $x \in \{0, 1, \ldots, q-1\}$ such that:
  $$g^x \equiv h \pmod{p}$$
* **Discrete Logarithm Problem (DLP):** The computational problem of finding $x$ given $g$, $h$, and $p$. No polynomial-time classical algorithm is known for large groups.
* **Generator (Primitive Root):** An element $g$ of $\mathbb{Z}_p^*$ such that every element of the group can be expressed as a power of $g$. The group $\mathbb{Z}_p^*$ has order $p - 1$.
* **Diffie-Hellman Problem (DHP):** Given $g^a \bmod p$ and $g^b \bmod p$, compute $g^{ab} \bmod p$ without knowing $a$ or $b$. The DHP is believed to be as hard as the DLP.
* **Requirements:**
    * The prime $p$ must be large (at least 2048 bits for classical DLP; 256-bit elliptic curve groups offer equivalent security).
    * The group order $p - 1$ must have a large prime factor to resist Pohlig-Hellman attacks.
    * The generator $g$ must be a primitive root of the group.

### 📊 Complexity Analysis

| Algorithm | Complexity | Notes |
| :--- | :--- | :--- |
| Baby-step Giant-step | $O(\sqrt{q})$ time & space | Generic group algorithm |
| Pohlig-Hellman | $O(\sqrt{p_{\max}})$ | $p_{\max}$ = largest prime factor of $q$ |
| Index Calculus (mod p) | $O\!\left(e^{(\log p)^{1/2}(\log \log p)^{1/2}}\right)$ | Sub-exponential; best for $\mathbb{Z}_p^*$ |
| Elliptic Curve DLP | $O(\sqrt{q})$ | No sub-exponential algorithm known |
| Shor's Algorithm (quantum) | $O((\log p)^3)$ | Breaks DLP and ECDLP |

* **Worst-Case ($O$) — Classical:** Sub-exponential via index calculus for $\mathbb{Z}_p^*$; fully exponential for elliptic curve groups.
* **Best-Case ($\Omega$):** $\Omega((\log p)^2)$ — at minimum, computing $g^x \bmod p$ requires modular exponentiation.
* **Average-Case ($\Theta$):** $\Theta(\sqrt{q})$ for generic group algorithms without group-specific structure to exploit.

### ❓ Why We Use It

* **Diffie-Hellman Key Exchange (DHKE):** Two parties agree on a shared secret $g^{ab} \bmod p$ by exchanging $g^a$ and $g^b$ publicly. An eavesdropper seeing both values cannot compute $g^{ab}$ without solving the DHP.
* **Digital Signature Algorithm (DSA):** Signature validity is verified using modular exponentiation; forgery requires solving the DLP.
* **Elliptic Curve Cryptography (ECC):** The DLP is transported to an elliptic curve group, where no sub-exponential algorithm is known, enabling shorter keys with equivalent security (e.g., 256-bit ECC ≈ 3072-bit RSA).
* **Forward Secrecy:** Ephemeral Diffie-Hellman (DHE/ECDHE) generates a fresh key pair per session, so compromise of the long-term key does not expose past sessions.

### ⚙️ How It Works — Diffie-Hellman Key Exchange

1. **Step 1 — Public parameters:** Alice and Bob agree on a large prime $p$ and a generator $g$ of $\mathbb{Z}_p^*$.
2. **Step 2 — Private keys:** Alice chooses a secret integer $a$; Bob chooses a secret integer $b$. Both are in $\{2, \ldots, p-2\}$.
3. **Step 3 — Public keys:** Alice sends $A = g^a \bmod p$; Bob sends $B = g^b \bmod p$.
4. **Step 4 — Shared secret:** Alice computes $B^a \bmod p$; Bob computes $A^b \bmod p$. Both equal:
   $$K = g^{ab} \bmod p$$
5. **Step 5 — Security:** An adversary observing $g$, $p$, $A$, and $B$ must solve the DLP to recover $a$ or $b$, which is computationally infeasible for large $p$.

---

## 💻 Usage / Example

```python
# Mathematical Foundations of Cryptography — Python Demonstration
# Covers: modular arithmetic, Miller-Rabin, GCD, totient, DLP / Diffie-Hellman

import math
import random


# ─────────────────────────────────────────────
# 1. Modular Arithmetic
# ─────────────────────────────────────────────
def mod_exp(base: int, exp: int, mod: int) -> int:
    """Fast modular exponentiation using Python's built-in pow (square-and-multiply).
    Complexity: O(log(exp) * log(mod)^2)
    """
    return pow(base, exp, mod)  # Python's built-in uses fast exponentiation


a, e, n = 7, 256, 1000000007
print(f"[Modular Exp]  {a}^{e} mod {n} = {mod_exp(a, e, n)}")


# ─────────────────────────────────────────────
# 2. Miller-Rabin Primality Test
# ─────────────────────────────────────────────
def miller_rabin(n: int, k: int = 40) -> bool:
    """Probabilistic primality test. False positive probability <= 4^(-k).
    Complexity: O(k * log(n)^2)
    """
    if n < 2:
        return False
    if n == 2 or n == 3:
        return True
    if n % 2 == 0:
        return False

    # Write n-1 as 2^s * d
    s, d = 0, n - 1
    while d % 2 == 0:
        s += 1
        d //= 2

    for _ in range(k):
        a = random.randrange(2, n - 1)
        x = pow(a, d, n)
        if x == 1 or x == n - 1:
            continue
        for _ in range(s - 1):
            x = pow(x, 2, n)
            if x == n - 1:
                break
        else:
            return False  # Composite
    return True  # Probably prime


p = 2**31 - 1  # Mersenne prime (known prime for verification)
print(f"[Miller-Rabin] {p} is prime: {miller_rabin(p)}")


# ─────────────────────────────────────────────
# 3. GCD and Extended Euclidean Algorithm
# ─────────────────────────────────────────────
def extended_gcd(a: int, b: int) -> tuple[int, int, int]:
    """Returns (gcd, x, y) such that a*x + b*y = gcd(a, b).
    Complexity: O(log(min(a, b)))
    """
    if b == 0:
        return a, 1, 0
    gcd, x1, y1 = extended_gcd(b, a % b)
    return gcd, y1, x1 - (a // b) * y1


def mod_inverse(a: int, m: int) -> int:
    """Compute a^(-1) mod m using the Extended Euclidean Algorithm."""
    gcd, x, _ = extended_gcd(a, m)
    if gcd != 1:
        raise ValueError(f"Modular inverse does not exist: gcd({a}, {m}) = {gcd}")
    return x % m


e_val, phi_n = 65537, (61 - 1) * (53 - 1)  # Toy RSA example: p=61, q=53
gcd_result, x, y = extended_gcd(e_val, phi_n)
print(f"[Ext. GCD]     gcd({e_val}, {phi_n}) = {gcd_result}  →  Bézout: {e_val}*({x}) + {phi_n}*({y}) = {gcd_result}")

d = mod_inverse(e_val, phi_n)
print(f"[Mod Inverse]  d = {e_val}^(-1) mod {phi_n} = {d}  →  Verify: {e_val}*{d} mod {phi_n} = {(e_val * d) % phi_n}")


# ─────────────────────────────────────────────
# 4. Euler's Totient Function
# ─────────────────────────────────────────────
def euler_totient(p: int, q: int) -> int:
    """Compute phi(p*q) = (p-1)*(q-1) for two distinct primes p, q.
    Complexity: O(1) given p and q.
    """
    return (p - 1) * (q - 1)


p_rsa, q_rsa = 61, 53        # Toy primes (use 2048-bit primes in production)
n_rsa = p_rsa * q_rsa
phi = euler_totient(p_rsa, q_rsa)
print(f"[Totient]      n = {p_rsa} * {q_rsa} = {n_rsa},  phi(n) = {phi}")

# RSA keypair demonstration
e_rsa = 65537 % phi  # Reduce to valid range for this toy example
if math.gcd(e_rsa, phi) != 1:
    e_rsa = 17       # Fallback exponent for toy example
d_rsa = mod_inverse(e_rsa, phi)
print(f"[RSA Keys]     Public: (e={e_rsa}, n={n_rsa}),  Private: (d={d_rsa}, n={n_rsa})")

m = 42  # Message (must be < n)
c = pow(m, e_rsa, n_rsa)    # Encrypt: C = M^e mod n
m_dec = pow(c, d_rsa, n_rsa) # Decrypt: M = C^d mod n
print(f"[RSA]          Encrypt {m} → {c},  Decrypt {c} → {m_dec}")


# ─────────────────────────────────────────────
# 5. Discrete Logarithm — Diffie-Hellman Key Exchange
# ─────────────────────────────────────────────
def diffie_hellman(p: int, g: int) -> tuple[int, int, int]:
    """Simulate one party's DH step: choose private key, compute public key.
    Returns (private_key, public_key, p).
    Complexity of key generation: O(log(p)^2) for modular exponentiation.
    """
    private = random.randrange(2, p - 1)
    public = pow(g, private, p)
    return private, public, p


# RFC 3526 — 2048-bit MODP Group (truncated to toy 64-bit prime for demonstration)
# In production, use a 2048-bit or 3072-bit safe prime.
p_dh = 0xFFFFFFFFFFFFFFC5  # 64-bit pseudo-safe prime (demonstration only)
g_dh = 2                    # Standard generator

alice_priv, alice_pub, _ = diffie_hellman(p_dh, g_dh)
bob_priv,   bob_pub,   _ = diffie_hellman(p_dh, g_dh)

alice_shared = pow(bob_pub,   alice_priv, p_dh)  # K = B^a mod p
bob_shared   = pow(alice_pub, bob_priv,   p_dh)  # K = A^b mod p

print(f"[DH]           Alice public: {hex(alice_pub)}")
print(f"[DH]           Bob   public: {hex(bob_pub)}")
print(f"[DH]           Shared secret match: {alice_shared == bob_shared}")

# Security note: An eavesdropper seeing alice_pub and bob_pub must solve
# g^x ≡ alice_pub (mod p) to find alice_priv — this is the Discrete Log Problem.
# Complexity of best classical attack: O(exp((log p)^(1/2) * (log log p)^(1/2)))
```


## 🔍 Concepts at a Glance

| Concept | Core Formula | Cryptographic Role | Hard Problem |
| :--- | :--- | :--- | :--- |
| Modular Arithmetic | $a \equiv b \pmod{n}$ | Defines finite computation space | — |
| Prime Numbers | $n = p \cdot q$ | RSA public modulus | Integer Factorization |
| GCD / Ext. Euclidean | $ax + by = \gcd(a,b)$ | Computes modular inverse $d = e^{-1} \bmod \phi(n)$ | — |
| Euler's Totient | $\phi(pq) = (p-1)(q-1)$ | RSA key derivation; Euler's Theorem | Requires factoring $n$ |
| Discrete Logarithm | $g^x \equiv h \pmod{p}$ | Diffie-Hellman, DSA, ECC | DLP / ECDLP |


## References

* [NIST FIPS 186-5 — Digital Signature Standard](https://doi.org/10.6028/NIST.FIPS.186-5) — Covers DSA and ECDSA, both grounded in the discrete logarithm problem.
* [RFC 3526 — MODP Diffie-Hellman Groups](https://datatracker.ietf.org/doc/html/rfc3526) — Standard safe primes and generators used in Diffie-Hellman key exchange.
* [RFC 8017 — PKCS #1: RSA Cryptography Specifications](https://datatracker.ietf.org/doc/html/rfc8017) — Formal RSA specification covering key generation, totient, and modular exponentiation.
* [Khan Academy — Modular Arithmetic](https://www.khanacademy.org/computing/computer-science/cryptography) — Accessible introduction to modular arithmetic in a cryptographic context.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Chapters 7–8 (Number Theory Background, RSA and DLP-based Cryptography).
* *Cryptography and Network Security* — William Stallings, Chapters 4–5 (Basic Concepts in Number Theory and Finite Fields).
* *The Art of Problem Solving — Number Theory* — Covers GCD, Euler's Theorem, and modular arithmetic with proofs.
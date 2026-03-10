# 📝 February 2026 — Notes & Reflections

Use this space for ongoing notes, insights, and reflections throughout your training journey.

## Key Insights

**Encoding ≠ Encryption:** Encoding (Base64, hex, URL encoding) transforms data for compatibility or transmission — it has no secret key and provides zero security. Encryption uses a secret key and is designed to protect confidentiality. Confusing the two is a common beginner mistake that leads to false assumptions about data protection.

---

**ECB Mode is Fundamentally Broken:** Electronic Codebook mode encrypts identical plaintext blocks to identical ciphertext blocks, revealing patterns in the data. The "penguin problem" illustrates this visually — encrypting a bitmap image with ECB leaves the shape perfectly recognizable in the ciphertext. Always use CBC, CTR, or GCM in practice.

---

**Nonce Reuse in GCM is Catastrophic:** GCM (Galois/Counter Mode) provides both encryption and authentication, but reusing the same nonce with the same key completely destroys confidentiality and authentication simultaneously. This is a documented real-world vulnerability that has affected production TLS implementations.

---

**RSA Security is Parameter-Dependent:** RSA is mathematically sound, but implementation errors destroy its security. Small exponents (e=3 with unpadded messages), common moduli shared between users, small primes, and the use of textbook RSA instead of OAEP padding are all exploitable. The algorithm is safe; careless key generation is not.

---

**Password Hashing ≠ General-Purpose Hashing:** SHA-256 is fast — which is exactly what makes it bad for passwords. Modern GPUs can compute billions of SHA-256 hashes per second, making brute-force trivial. Password hashing algorithms (bcrypt, scrypt, Argon2) are intentionally slow and memory-hard to resist brute-force and GPU-based attacks.

---

**Homomorphic Encryption is Theoretically Powerful, Practically Slow:** Fully Homomorphic Encryption (FHE) allows arbitrary computation on encrypted data without decrypting it — a remarkable property for privacy-preserving computation. However, current FHE schemes are 10³–10⁶× slower than plaintext operations, making real-world deployment extremely limited outside of research.

---

**Adversarial ML Attacks Exploit Decision Boundaries:** Small, imperceptible perturbations added to input data (adversarial examples) can cause ML models to misclassify with high confidence. These are not random errors — they are crafted by computing the gradient of the model's loss with respect to the input, then nudging pixels toward misclassification.

---

## Favorite Resources Discovered

**CryptoHack (cryptohack.org)** — Gamified cryptography learning platform with interactive challenges organized by topic (classical ciphers, hash functions, RSA, elliptic curves, AES). Best used for active practice immediately after learning theory. The challenge format provides instant feedback on understanding.

---

**Cryptopals Challenges (cryptopals.com)** — 48 cryptography challenges that teach real-world attack techniques by having you implement them. Focuses on practical vulnerabilities (CBC padding oracles, nonce reuse, fixed-key CBC decryption) rather than pure theory. The gold standard for hands-on cryptography learning.

---

**RsaCtfTool** — Automated tool for attacking weak RSA implementations from the command line. Supports small exponents, Wiener's attack, common modulus attacks, Fermat factorization, and many more. Essential for CTF cryptography challenges when key parameters are suspicious.

---

**pycryptodome (Python library)** — Comprehensive cryptography implementation library. Used throughout the month for AES (all modes), RSA (PKCS1_OAEP), random key generation, and padding utilities. Documentation is thorough and the API is intuitive for learning purposes.

---

**TryHackMe Cryptography Path** — Structured, guided progression through cryptography fundamentals, hashing, and practical cracking tools (John the Ripper, Hashcat). Ideal for building structured foundational knowledge before tackling unguided CTF challenges.

---

## Techniques That Clicked

**Frequency Analysis on Classical Ciphers** — Understanding that letter frequency in English (E=12.7%, T=9.1%, A=8.2%...) allows statistical attacks against any monoalphabetic substitution cipher without knowing the key. Once you internalize that language has statistical structure, classical cryptography becomes intuitive to break.

---

**AES Round Structure as Confusion + Diffusion:** After working through the AES round operations manually (SubBytes → ShiftRows → MixColumns → AddRoundKey), the roles became clear: SubBytes provides confusion (non-linear substitution), while ShiftRows and MixColumns provide diffusion (spreading bit influence across the block). Each layer has a distinct, necessary security purpose.

---

**CBC vs CTR Mode Trade-offs:** CBC requires sequential encryption (IV chaining blocks together), has PKCS7 padding requirements, and flip errors propagate to the next block. CTR generates a keystream and XORs it with plaintext, enabling parallelization, random access to any position, and no padding requirements. Choosing the right mode depends on the access pattern and performance requirements.

---

**The RSA Key Generation Pipeline:** Starting with two large random primes p and q, computing n = p×q, then φ(n) = (p-1)(q-1), choosing a public exponent e coprime to φ(n), and finally computing private exponent d = e⁻¹ mod φ(n) using the Extended Euclidean Algorithm. Walking through this step-by-step makes RSA demystified — it's modular arithmetic applied carefully.

---

**HMAC vs Hash-then-Append:** Simply computing hash(secret || message) for message authentication is vulnerable to length extension attacks — an attacker who knows H(secret || message) can compute H(secret || message || extension) without knowing the secret. HMAC uses a two-layer construction: H(key XOR opad || H(key XOR ipad || message)), which is provably secure against this attack.

---

**CRT-Optimized RSA Decryption:** Using the Chinese Remainder Theorem with precomputed values (dP = d mod p-1, dQ = d mod q-1, qInv = q⁻¹ mod p) speeds up RSA private key operations by roughly 4× by working modulo the smaller factors p and q separately, then combining via Garner's formula.

---

## Future Topics to Explore

**Elliptic Curve Cryptography (ECC)** — Provides equivalent security to RSA with much smaller key sizes (256-bit ECC ≈ 3072-bit RSA). Foundation for modern TLS 1.3, Bitcoin (secp256k1), Signal protocol (Curve25519), and ECDSA signatures. Required for understanding modern key exchange.

---

**Flask Session Security & Web Exploitation** — During the Love at First Breach CTF, Flask session cookie vulnerabilities were identified (Base64-encoded, weakly signed cookies). Deeper study needed on session token attacks, IDOR vulnerabilities, and API endpoint enumeration for the next CTF.

---

**Padding Oracle Attacks** — A side-channel attack against CBC-mode ciphers with PKCS7 padding that allows byte-by-byte decryption of ciphertext without knowing the key. One of the most elegant attacks in cryptography. Understanding this is key for CryptoHack and HackTheBox challenges.

---

**Practical Hashcat Mastery** — Rule-based attacks, mask attacks, combinator attacks, and hashcat performance tuning for different hash types (MD5, SHA-1, bcrypt, NTLM). Required for real-world password audit work and CTF hash cracking challenges in March.

---

**Post-Quantum Cryptography** — NIST finalized standards in 2024: CRYSTALS-Kyber (ML-KEM) for key encapsulation and CRYSTALS-Dilithium (ML-DSA) for digital signatures. Lattice-based algorithms will replace RSA and ECC as quantum computers scale. Important for understanding the future direction of applied cryptography.

---

**Side-Channel Attacks** — Timing attacks, power analysis (SPA/DPA), and cache-timing attacks bypass cryptographic correctness by exploiting physical implementation details rather than mathematical weaknesses. Increasingly relevant for embedded systems, smart cards, and hardware security modules.

---

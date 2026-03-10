# 🎯 Month Goal

Understand:

* Mathematical foundations of cryptography
* How encryption algorithms work internally
* Cryptographic vulnerabilities and attacks
* Intersection of cryptography and AI/ML

---

# 📅 Week 1 — Mathematical Foundations (Very Important)

## 1️⃣ Number Theory Basics

You must be comfortable with:

* Modular arithmetic
* Prime numbers and factorization
* Greatest Common Divisor (GCD)
* Euler's totient function
* Discrete logarithm problem

👉 Install:

* Python 3.x
* `pycryptodome` library
* `cryptography` library
* SageMath (optional, for advanced math)

```bash
pip install pycryptodome cryptography
```

---

## 2️⃣ Classical Cryptography (Critical)

Understand deeply:

* Caesar cipher
* Substitution cipher
* Vigenère cipher
* Frequency analysis
* Cryptanalysis techniques

### Practice:

```python
def caesar_cipher(text, shift):
    result = ""
    for char in text:
        if char.isalpha():
            base = ord('A') if char.isupper() else ord('a')
            result += chr((ord(char) - base + shift) % 26 + base)
        else:
            result += char
    return result

# Encrypt
plaintext = "HELLO"
ciphertext = caesar_cipher(plaintext, 3)
print(f"Encrypted: {ciphertext}")

# Decrypt
decrypted = caesar_cipher(ciphertext, -3)
print(f"Decrypted: {decrypted}")
```

Learn:

* Why simple substitution is insecure
* How frequency analysis breaks ciphers
* Key space and brute force complexity
* Difference between encoding and encryption

---

# 📅 Week 2 — Symmetric Cryptography

## 1️⃣ Block Cipher Fundamentals

Learn:

* Block vs Stream ciphers
* DES (understand weaknesses)
* AES (Rijndael) structure
* Modes of operation: ECB, CBC, CTR, GCM
* Padding schemes (PKCS7)

Practice:

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
from Crypto.Util.Padding import pad, unpad

# Generate key
key = get_random_bytes(32)  # 256-bit key

# Create cipher
cipher = AES.new(key, AES.MODE_CBC)

# Encrypt
plaintext = b"Secret message for encryption"
ciphertext = cipher.encrypt(pad(plaintext, AES.block_size))

print(f"IV: {cipher.iv.hex()}")
print(f"Ciphertext: {ciphertext.hex()}")

# Decrypt
decipher = AES.new(key, AES.MODE_CBC, cipher.iv)
decrypted = unpad(decipher.decrypt(ciphertext), AES.block_size)
print(f"Decrypted: {decrypted.decode()}")
```

Goal:
Understand what happens at the byte level during encryption.

---

## 2️⃣ Common Vulnerabilities

Learn:

* ECB mode weakness (penguin problem)
* Padding oracle attacks
* IV reuse vulnerabilities
* Key reuse problems

Use TryHackMe:

* Encryption - Crypto 101 room
* Complete all AES challenges

---

# 📅 Week 3 — Asymmetric Cryptography & Hashing

Now we go deeper.

## 1️⃣ RSA Cryptosystem

Concepts:

* Key generation (p, q, n, e, d)
* Encryption: $c = m^e \mod n$
* Decryption: $m = c^d \mod n$
* Common attacks: small exponent, common modulus

Practice:

```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP

# Generate RSA key pair
key = RSA.generate(2048)
public_key = key.publickey()

# Encrypt with public key
cipher = PKCS1_OAEP.new(public_key)
message = b"Confidential data"
ciphertext = cipher.encrypt(message)

print(f"Ciphertext: {ciphertext.hex()}")

# Decrypt with private key
decipher = PKCS1_OAEP.new(key)
plaintext = decipher.decrypt(ciphertext)
print(f"Decrypted: {plaintext.decode()}")

# Display key components
print(f"\nn = {key.n}")
print(f"e = {key.e}")
print(f"d = {key.d}")
```

Understand:

* Why RSA is slow compared to AES
* Hybrid encryption concept
* Digital signatures vs encryption

---

## 2️⃣ Hash Functions

Learn:

* MD5 (broken, understand why)
* SHA family (SHA-1, SHA-256, SHA-3)
* Properties: preimage resistance, collision resistance
* HMAC for message authentication
* Password hashing: bcrypt, scrypt, Argon2

Practice:

```python
import hashlib
import hmac

# Hash function
data = b"Important document"
hash_value = hashlib.sha256(data).hexdigest()
print(f"SHA-256: {hash_value}")

# HMAC for integrity
key = b"secret_key"
mac = hmac.new(key, data, hashlib.sha256).hexdigest()
print(f"HMAC: {mac}")

# Verify integrity
is_valid = hmac.compare_digest(
    mac, 
    hmac.new(key, data, hashlib.sha256).hexdigest()
)
print(f"Valid: {is_valid}")
```

---

# 📅 Week 4 — Practical Cryptanalysis & ML Integration

## 1️⃣ Breaking Weak Cryptography

Concepts:

* Brute force attacks
* Dictionary attacks on hashes
* Rainbow tables
* Birthday attack on hash functions
* Timing attacks

Tools:

* John the Ripper
* Hashcat
* CyberChef
* RsaCtfTool

Practice:

```python
import itertools
import hashlib

def brute_force_hash(target_hash, charset, max_length):
    """
    Brute force hash cracking.
    Time Complexity: O(n^m) where n=charset size, m=length
    """
    for length in range(1, max_length + 1):
        for attempt in itertools.product(charset, repeat=length):
            password = ''.join(attempt)
            if hashlib.md5(password.encode()).hexdigest() == target_hash:
                return password
    return None

# Example: crack weak password
target = hashlib.md5(b"abc").hexdigest()
charset = "abcdefghijklmnopqrstuvwxyz"
result = brute_force_hash(target, charset, 4)
print(f"Cracked password: {result}")
```

---

## 2️⃣ Cryptography in AI/ML Context

Learn intersection topics:

* Homomorphic encryption (compute on encrypted data)
* Secure multi-party computation
* Differential privacy
* Federated learning security
* Adversarial attacks on ML models
* Model extraction attacks

Basic Example:

```python
# Simple example: Encrypted model predictions
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
import pickle

def encrypt_model_output(prediction, key):
    """Encrypt ML model predictions before transmission"""
    cipher = AES.new(key, AES.MODE_EAX)
    ciphertext, tag = cipher.encrypt_and_digest(str(prediction).encode())
    return cipher.nonce, ciphertext, tag

def decrypt_model_output(nonce, ciphertext, tag, key):
    """Decrypt received predictions"""
    cipher = AES.new(key, AES.MODE_EAX, nonce=nonce)
    plaintext = cipher.decrypt_and_verify(ciphertext, tag)
    return float(plaintext.decode())

# Simulate secure prediction
key = get_random_bytes(16)
prediction = 0.95  # ML model output
nonce, encrypted, tag = encrypt_model_output(prediction, key)
decrypted = decrypt_model_output(nonce, encrypted, tag, key)
print(f"Original: {prediction}, Decrypted: {decrypted}")
```

---

# 📚 Daily Study Structure (2–3 Hours)

1. 30m mathematical theory
2. 1h coding/implementation
3. 30m cryptanalysis practice
4. 30m TryHackMe/CTF challenges

---

# 🧠 Concepts You Must Understand Clearly

* Why XOR is used in cryptography
* Difference between confusion and diffusion
* Key derivation functions
* Perfect forward secrecy
* Certificate chains and PKI
* Side-channel attacks
* Timing attacks

If you don't understand these, modern cryptography won't click.

---

# 🛠 Tools You Should Learn

* `openssl` command line
* `hashcat`
* `john`
* `RsaCtfTool`
* `CyberChef`
* Python `pycryptodome`
* Python `cryptography` library

---

# 🎓 TryHackMe Learning Path

Week 1:
* Cryptography Intro
* Encryption - Crypto 101

Week 2:
* Hashing - Crypto 101
* John The Ripper

Week 3:
* Crack the Hash
* Crack the Hash Level 2

Week 4:
* Custom CTF challenges
* HackTheBox Crypto challenges

---

# 🚀 After This Month

Next topics:

* Elliptic Curve Cryptography (ECC)
* Zero-knowledge proofs
* Blockchain cryptography
* Post-quantum cryptography
* Secure ML model deployment
* Privacy-preserving machine learning
* Differential privacy in datasets

---

# 🔗 AI/ML + Cryptography Career Path

Positions you can target:

* Security Engineer (AI/ML Systems)
* Cryptography Engineer
* Privacy Engineer
* Blockchain Developer
* Research Scientist (Secure AI)

Skills intersection:

* Federated Learning + Encryption
* Model Privacy + Differential Privacy
* Secure Inference Systems
* Privacy-Preserving Data Mining

---

# 📖 Recommended Resources

Books:

* "Understanding Cryptography" - Christof Paar
* "Serious Cryptography" - Jean-Philippe Aumasson
* "Applied Cryptography" - Bruce Schneier

Online:

* Cryptohack.org (gamified learning)
* TryHackMe Cryptography Path
* Cryptopals Challenges
* Khan Academy - Cryptography

Research Papers:

* "Privacy-Preserving Deep Learning"
* "Secure Multi-Party Computation"
* "Homomorphic Encryption for Machine Learning"

---

# 💡 Final Tips

1. **Always implement algorithms yourself first** before using libraries
2. **Never roll your own crypto** in production
3. **Understand the math** — it's not optional in cryptography
4. **Connect concepts to AI** — think about privacy in ML from day one
5. **Practice CTFs regularly** — cryptography challenges build intuition

---

**Remember:** Cryptography is the foundation of secure AI systems. As AI becomes more prevalent, cryptographic skills will be increasingly valuable for protecting models, data, and predictions.

```
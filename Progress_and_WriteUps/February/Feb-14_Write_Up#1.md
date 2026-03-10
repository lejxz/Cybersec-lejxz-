# Cryptography in Cybersecurity

## 📋 Summary
* **Core Concept:** Cryptography is the practice of securing information by transforming it into an unreadable format that can only be accessed by authorized parties who possess the correct decryption key.

> **Takeaways:** Cryptography is a fundamental pillar of cybersecurity that protects data confidentiality, integrity, and authenticity. It enables secure communication over insecure channels, protects stored data from unauthorized access, and verifies the identity of communicating parties. Modern cryptography relies on mathematical algorithms that are computationally infeasible to break without the proper key.


## 📖 Definition

* **Cryptography:** The science of protecting information by encoding it into an unreadable format using mathematical algorithms and keys.
* **Encryption:** The process of converting plaintext into ciphertext using an algorithm and a key.
* **Decryption:** The process of converting ciphertext back into plaintext using an algorithm and a key.
* **Plaintext:** The original, readable data before encryption.
* **Ciphertext:** The encrypted, unreadable data after encryption.
* **Key:** A piece of information used by cryptographic algorithms to encrypt or decrypt data.
* **Cipher:** An algorithm used to perform encryption and decryption.
* **Symmetric Encryption:** Encryption method where the same key is used for both encryption and decryption.
* **Asymmetric Encryption:** Encryption method where a public key is used for encryption and a private key is used for decryption.
* **Hash Function:** A one-way function that converts data of any size into a fixed-size output.
* **Digital Signature:** A cryptographic technique that verifies the authenticity and integrity of a message or document.

* **Requirements:**
    * A cryptographic algorithm (cipher)
    * A key or key pair
    * Secure key management
    * Computationally secure implementation


## 📊 Complexity Analysis

| Notation | Name | Growth Rate | Cryptographic Context |
| :--- | :--- | :--- | :--- |
| $O(1)$ | Constant | Excellent | Hash table lookups |
| $O(\log n)$ | Logarithmic | Very Good | Binary exponentiation |
| $O(n)$ | Linear | Good | Stream cipher encryption |
| $O(n^2)$ | Quadratic | Poor | Some matrix operations |
| $O(2^n)$ | Exponential | Very Poor | Brute force key search |

* **Worst-Case ($O$):** The maximum time required for cryptographic operations under adversarial conditions.
* **Best-Case ($\Omega$):** The minimum time required for cryptographic operations with optimal input.
* **Average-Case ($\Theta$):** The expected performance for typical cryptographic operations.

**Key Security Principle:** A secure cryptographic algorithm should have an exponential time complexity ($O(2^n)$) for breaking it through brute force, where $n$ is the key size in bits.


## ❓ Why we use it

* **Data Confidentiality:** Cryptography prevents unauthorized parties from reading sensitive information during transmission or storage.
* **Data Integrity:** Cryptographic hash functions and message authentication codes ensure that data has not been tampered with.
* **Authentication:** Digital signatures and certificates verify the identity of users, systems, and organizations.
* **Non-repudiation:** Digital signatures provide proof that a specific party sent a message, preventing them from denying it later.
* **Secure Communication:** Protocols like TLS/SSL use cryptography to secure web traffic, email, and other network communications.
* **Password Protection:** Hash functions protect stored passwords from being exposed in database breaches.
* **Regulatory Compliance:** Many regulations require encryption for protecting personal and financial data.


## ⚙️ How it works

### Symmetric Encryption Process
1. **Key Generation:** A secret key is generated and securely shared between sender and receiver.
2. **Encryption:** The sender uses the secret key and an encryption algorithm to convert plaintext into ciphertext.
3. **Transmission:** The ciphertext is sent over an insecure channel.
4. **Decryption:** The receiver uses the same secret key and decryption algorithm to convert ciphertext back to plaintext.

### Asymmetric Encryption Process
1. **Key Pair Generation:** A public key and private key pair is generated.
2. **Public Key Distribution:** The public key is shared openly while the private key remains secret.
3. **Encryption:** The sender encrypts the message using the receiver's public key.
4. **Decryption:** The receiver decrypts the message using their private key.

### Hash Function Process
1. **Input:** Any size of data is provided as input.
2. **Processing:** The hash function processes the data through mathematical operations.
3. **Output:** A fixed-size hash value (digest) is produced.
4. **Verification:** The hash can be recalculated and compared to verify data integrity.


## 💻 Usage / Program Example

### Example 1: Symmetric Encryption (AES)
```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
from Crypto.Util.Padding import pad, unpad

def symmetric_encrypt(plaintext, key):
    # Create AES cipher in CBC mode
    cipher = AES.new(key, AES.MODE_CBC)
    
    # Encrypt the plaintext with padding
    ciphertext = cipher.encrypt(pad(plaintext.encode(), AES.block_size))
    
    return cipher.iv, ciphertext

def symmetric_decrypt(iv, ciphertext, key):
    # Create AES cipher with the same IV
    cipher = AES.new(key, AES.MODE_CBC, iv)
    
    # Decrypt and remove padding
    plaintext = unpad(cipher.decrypt(ciphertext), AES.block_size)
    
    return plaintext.decode()

# Generate a random 256-bit key
key = get_random_bytes(32)

# Encrypt message
message = "This is a secret message"
iv, encrypted = symmetric_encrypt(message, key)
print(f"Encrypted: {encrypted.hex()}")

# Decrypt message
decrypted = symmetric_decrypt(iv, encrypted, key)
print(f"Decrypted: {decrypted}")

# Time Complexity: O(n) where n is the message length
```

### Example 2: Asymmetric Encryption (RSA)
```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP

def generate_key_pair():
    # Generate RSA key pair (2048 bits)
    key = RSA.generate(2048)
    private_key = key.export_key()
    public_key = key.publickey().export_key()
    return private_key, public_key

def asymmetric_encrypt(message, public_key):
    # Import public key and create cipher
    rsa_key = RSA.import_key(public_key)
    cipher = PKCS1_OAEP.new(rsa_key)
    
    # Encrypt the message
    ciphertext = cipher.encrypt(message.encode())
    return ciphertext

def asymmetric_decrypt(ciphertext, private_key):
    # Import private key and create cipher
    rsa_key = RSA.import_key(private_key)
    cipher = PKCS1_OAEP.new(rsa_key)
    
    # Decrypt the message
    plaintext = cipher.decrypt(ciphertext)
    return plaintext.decode()

# Generate keys
private_key, public_key = generate_key_pair()

# Encrypt with public key
message = "Secret data"
encrypted = asymmetric_encrypt(message, public_key)
print(f"Encrypted: {encrypted.hex()}")

# Decrypt with private key
decrypted = asymmetric_decrypt(encrypted, private_key)
print(f"Decrypted: {decrypted}")

# Time Complexity: O(k^3) where k is the key size
```

### Example 3: Hash Function (SHA-256)
```python
import hashlib

def hash_data(data):
    # Create SHA-256 hash object
    sha256 = hashlib.sha256()
    
    # Update with data
    sha256.update(data.encode())
    
    # Return hexadecimal digest
    return sha256.hexdigest()

def verify_integrity(data, expected_hash):
    # Calculate hash of data
    calculated_hash = hash_data(data)
    
    # Compare with expected hash
    return calculated_hash == expected_hash

# Hash a message
message = "Important document content"
hash_value = hash_data(message)
print(f"Hash: {hash_value}")

# Verify integrity
is_valid = verify_integrity(message, hash_value)
print(f"Integrity verified: {is_valid}")

# Demonstrate tampering detection
tampered_message = "Important document content modified"
is_valid = verify_integrity(tampered_message, hash_value)
print(f"Tampered message verified: {is_valid}")

# Time Complexity: O(n) where n is the data length
```

### Example 4: Password Hashing (bcrypt)
```python
import bcrypt

def hash_password(password):
    # Generate salt and hash password
    salt = bcrypt.gensalt(rounds=12)
    hashed = bcrypt.hashpw(password.encode(), salt)
    return hashed

def verify_password(password, hashed):
    # Verify password against hash
    return bcrypt.checkpw(password.encode(), hashed)

# Hash a password
password = "MySecurePassword123"
hashed_password = hash_password(password)
print(f"Hashed password: {hashed_password.decode()}")

# Verify correct password
is_correct = verify_password(password, hashed_password)
print(f"Password correct: {is_correct}")

# Verify incorrect password
is_correct = verify_password("WrongPassword", hashed_password)
print(f"Wrong password: {is_correct}")

# Time Complexity: O(2^r) where r is the cost factor (intentionally slow)
```

### Example 5: Caesar Cipher (Educational Example)
```python
def caesar_encrypt(plaintext, shift):
    ciphertext = ""
    for char in plaintext:
        if char.isalpha():
            # Determine if uppercase or lowercase
            base = ord('A') if char.isupper() else ord('a')
            # Shift character and wrap around
            shifted = (ord(char) - base + shift) % 26
            ciphertext += chr(base + shifted)
        else:
            ciphertext += char
    return ciphertext

def caesar_decrypt(ciphertext, shift):
    # Decryption is encryption with negative shift
    return caesar_encrypt(ciphertext, -shift)

# Encrypt message
plaintext = "HELLO WORLD"
shift = 3
encrypted = caesar_encrypt(plaintext, shift)
print(f"Encrypted: {encrypted}")

# Decrypt message
decrypted = caesar_decrypt(encrypted, shift)
print(f"Decrypted: {decrypted}")

# Time Complexity: O(n) where n is the message length
# Security: Very weak - only 26 possible keys
```


## References

* [TryHackMe - Cryptography](https://tryhackme.com/room/cryptographyintro) — Introduction to cryptography fundamentals and common algorithms.
* [TryHackMe - Encryption - Crypto 101](https://tryhackme.com/room/encryptioncrypto101) — Basic encryption concepts and techniques.
* [TryHackMe - Hashing - Crypto 101](https://tryhackme.com/room/hashingcrypto101) — Hash functions and their applications in security.
* [NIST Cryptographic Standards](https://csrc.nist.gov/projects/cryptographic-standards-and-guidelines) — Official cryptographic standards and guidelines.
* [Applied Cryptography](https://www.schneier.com/books/applied-cryptography/) — Bruce Schneier, comprehensive reference on cryptographic protocols.
# Cryptography Basics

## 📋 Summary
* **Core Concept:** Cryptography basics covers the fundamental principles, terminology, and essential algorithms used to secure information, including classical ciphers, modern encryption standards, and the mathematical foundations that make secure communication possible.

> **Takeaways:** Understanding cryptography basics is essential for cybersecurity professionals to protect data confidentiality, ensure data integrity, and verify authenticity. The field encompasses classical ciphers that demonstrate core concepts, modern symmetric and asymmetric encryption that secures digital communications, and hash functions that verify data integrity. Mastering these fundamentals enables you to implement proper security measures, identify cryptographic vulnerabilities, and make informed decisions about which cryptographic methods to apply in different scenarios.


## 📖 Definition

* **Cryptanalysis:** The study of analyzing and breaking cryptographic systems to find weaknesses.
* **Brute Force Attack:** An attack method that tries all possible keys until the correct one is found.
* **Key Space:** The total number of possible keys that can be used in a cryptographic algorithm.
* **Substitution Cipher:** A cipher that replaces each character in the plaintext with another character.
* **Transposition Cipher:** A cipher that rearranges the characters in the plaintext without changing them.
* **Block Cipher:** A cipher that encrypts fixed-size blocks of data at a time.
* **Stream Cipher:** A cipher that encrypts data one bit or byte at a time.
* **Initialization Vector (IV):** A random value used with encryption algorithms to ensure identical plaintexts produce different ciphertexts.
* **Salt:** A random value added to data before hashing to prevent rainbow table attacks.
* **Rainbow Table:** A precomputed table of hash values used to reverse cryptographic hash functions.
* **Key Exchange:** The process of securely sharing cryptographic keys between parties.
* **Diffie-Hellman:** A key exchange protocol that allows two parties to establish a shared secret over an insecure channel.

* **Requirements:**
    * Understanding of basic mathematics and number theory
    * Knowledge of binary and hexadecimal representations
    * Awareness of computational complexity
    * Secure random number generation
    * Proper key management practices


## 📊 Complexity Analysis

| Algorithm Type | Time Complexity | Space Complexity | Security Level |
| :--- | :--- | :--- | :--- |
| Caesar Cipher | $O(n)$ | $O(1)$ | Very Weak |
| Substitution Cipher | $O(n)$ | $O(1)$ | Weak |
| Vigenère Cipher | $O(n)$ | $O(k)$ | Weak |
| AES Encryption | $O(n)$ | $O(1)$ | Strong |
| RSA Encryption | $O(k^3)$ | $O(k)$ | Strong |
| SHA-256 Hash | $O(n)$ | $O(1)$ | Strong |

**Attack Complexity:**
| Attack Method | Complexity | Description |
| :--- | :--- | :--- |
| Brute Force (Caesar) | $O(26)$ | Try all 26 shifts |
| Brute Force (AES-128) | $O(2^{128})$ | Try all possible keys |
| Dictionary Attack | $O(d)$ | Try common passwords |
| Frequency Analysis | $O(n \log n)$ | Analyze character frequency |

* **Worst-Case ($O$):** The maximum time an attacker needs to break the cipher through brute force.
* **Best-Case ($\Omega$):** The minimum time for legitimate encryption or decryption operations.
* **Average-Case ($\Theta$):** The expected performance for typical cryptographic operations.

**Security Principle:** A cryptographic algorithm is considered secure if breaking it requires computational resources that are not practically available, typically requiring $O(2^n)$ operations where $n \geq 128$ bits.


## ❓ Why we use it

* **Foundation of Security:** Cryptography basics provide the essential knowledge needed to understand modern security systems and protocols.
* **Threat Assessment:** Understanding classical ciphers and their weaknesses helps identify similar vulnerabilities in modern systems.
* **Algorithm Selection:** Knowledge of basic cryptographic principles enables proper selection of algorithms for specific security requirements.
* **Secure Implementation:** Understanding the fundamentals prevents common implementation mistakes that weaken security.
* **Penetration Testing:** Cryptography basics are essential for identifying and exploiting weak cryptographic implementations during security assessments.
* **Digital Privacy:** These principles protect personal communications, financial transactions, and sensitive data from unauthorized access.
* **Career Development:** Mastery of cryptography basics is a prerequisite for advanced cybersecurity certifications and roles.


## ⚙️ How it works

### Classical Cipher Process
1. **Key Selection:** Choose a key that determines the transformation (such as shift value or substitution table).
2. **Character Mapping:** Apply the transformation rule to each character in the plaintext.
3. **Output Generation:** Produce the ciphertext by applying the transformation consistently.
4. **Reversal:** Use the inverse transformation with the same key to decrypt.

### Modern Encryption Process
1. **Key Generation:** Generate cryptographically secure random keys of appropriate length.
2. **Algorithm Selection:** Choose an appropriate algorithm based on security requirements (AES for symmetric, RSA for asymmetric).
3. **Mode Selection:** Select an encryption mode (CBC, GCM, etc.) that provides required security properties.
4. **Encryption Execution:** Apply the algorithm with proper padding and initialization vectors.
5. **Secure Storage:** Store or transmit the ciphertext along with necessary metadata (IV, algorithm identifier).

### Cryptanalysis Process
1. **Cipher Identification:** Determine what type of cipher was used based on characteristics.
2. **Pattern Recognition:** Look for patterns, repeated sequences, or statistical anomalies.
3. **Frequency Analysis:** Analyze the frequency of characters or patterns in the ciphertext.
4. **Known-Plaintext Attack:** Use known plaintext-ciphertext pairs to deduce the key.
5. **Key Recovery:** Attempt to recover the key through mathematical analysis or brute force.


## 💻 Usage / Program Example

### Example 1: Caesar Cipher Implementation
```python
def caesar_cipher(text, shift, mode='encrypt'):
    """
    Implements Caesar cipher encryption and decryption.
    Time Complexity: O(n) where n is the text length
    Space Complexity: O(n) for storing result
    """
    if mode == 'decrypt':
        shift = -shift
    
    result = ""
    for char in text:
        if char.isalpha():
            # Determine ASCII base (uppercase or lowercase)
            base = ord('A') if char.isupper() else ord('a')
            # Shift and wrap around alphabet
            shifted = (ord(char) - base + shift) % 26
            result += chr(base + shifted)
        else:
            result += char
    
    return result

# Encryption example
plaintext = "HELLO WORLD"
shift = 3
ciphertext = caesar_cipher(plaintext, shift, 'encrypt')
print(f"Plaintext: {plaintext}")
print(f"Ciphertext: {ciphertext}")
print(f"Decrypted: {caesar_cipher(ciphertext, shift, 'decrypt')}")

# Brute force attack demonstration
print("\nBrute Force Attack:")
for key in range(26):
    decrypted = caesar_cipher(ciphertext, key, 'decrypt')
    print(f"Key {key}: {decrypted}")
```

### Example 2: Substitution Cipher
```python
import string
import random

def generate_substitution_key():
    """
    Generates a random substitution key.
    Time Complexity: O(1) - fixed alphabet size
    """
    alphabet = list(string.ascii_uppercase)
    shuffled = alphabet.copy()
    random.shuffle(shuffled)
    return dict(zip(alphabet, shuffled))

def substitution_cipher(text, key, mode='encrypt'):
    """
    Implements substitution cipher.
    Time Complexity: O(n) where n is the text length
    """
    if mode == 'decrypt':
        # Reverse the key for decryption
        key = {v: k for k, v in key.items()}
    
    result = ""
    for char in text:
        if char.upper() in key:
            transformed = key[char.upper()]
            result += transformed if char.isupper() else transformed.lower()
        else:
            result += char
    
    return result

# Generate random substitution key
sub_key = generate_substitution_key()
print("Substitution Key (first 10):", dict(list(sub_key.items())[:10]))

# Encrypt and decrypt
plaintext = "ATTACK AT DAWN"
ciphertext = substitution_cipher(plaintext, sub_key, 'encrypt')
decrypted = substitution_cipher(ciphertext, sub_key, 'decrypt')

print(f"\nPlaintext: {plaintext}")
print(f"Ciphertext: {ciphertext}")
print(f"Decrypted: {decrypted}")
```

### Example 3: Frequency Analysis Attack
```python
def frequency_analysis(ciphertext):
    """
    Performs frequency analysis on ciphertext.
    Time Complexity: O(n) where n is the ciphertext length
    """
    # Count character frequencies
    frequency = {}
    total_chars = 0
    
    for char in ciphertext.upper():
        if char.isalpha():
            frequency[char] = frequency.get(char, 0) + 1
            total_chars += 1
    
    # Calculate percentages and sort
    freq_percent = {char: (count / total_chars * 100) 
                    for char, count in frequency.items()}
    
    sorted_freq = sorted(freq_percent.items(), 
                        key=lambda x: x[1], reverse=True)
    
    return sorted_freq

def suggest_substitutions(ciphertext):
    """
    Suggests possible substitutions based on English letter frequency.
    """
    # English letter frequency (most common)
    english_freq = ['E', 'T', 'A', 'O', 'I', 'N', 'S', 'H', 'R', 'D']
    
    cipher_freq = frequency_analysis(ciphertext)
    
    print("Frequency Analysis Results:")
    print(f"{'Cipher':<8} {'Frequency':<12} {'Possible'}")
    print("-" * 35)
    
    for i, (char, freq) in enumerate(cipher_freq[:10]):
        possible = english_freq[i] if i < len(english_freq) else '?'
        print(f"{char:<8} {freq:>6.2f}%       {possible}")

# Example ciphertext (encrypted with substitution)
ciphertext = """
KJMMW BWUMP! KJU KJVU VUTBHUMP FHQRBWZHQRKX FQXRBWQTQMXVHV
HTUQ MWB BHTWMJPKWDB. BKHV HV Q VJWDB UOQFRMU WY VDRRMHAP
YHUSDUTAX QTQMXVHV BW PUVV RWVVHJMU VDJVBHBDBHWTV.
"""

suggest_substitutions(ciphertext)
```

### Example 4: Vigenère Cipher
```python
def vigenere_cipher(text, key, mode='encrypt'):
    """
    Implements Vigenère cipher (polyalphabetic substitution).
    Time Complexity: O(n) where n is the text length
    Space Complexity: O(k) where k is the key length
    """
    result = ""
    key = key.upper()
    key_index = 0
    
    for char in text:
        if char.isalpha():
            # Get shift value from key
            shift = ord(key[key_index % len(key)]) - ord('A')
            
            if mode == 'decrypt':
                shift = -shift
            
            # Apply shift
            base = ord('A') if char.isupper() else ord('a')
            shifted = (ord(char) - base + shift) % 26
            result += chr(base + shifted)
            
            key_index += 1
        else:
            result += char
    
    return result

# Encryption and decryption
plaintext = "ATTACKATDAWN"
key = "LEMON"

ciphertext = vigenere_cipher(plaintext, key, 'encrypt')
decrypted = vigenere_cipher(ciphertext, key, 'decrypt')

print(f"Plaintext: {plaintext}")
print(f"Key: {key}")
print(f"Ciphertext: {ciphertext}")
print(f"Decrypted: {decrypted}")

# Key space analysis
print(f"\nKey Space: 26^{len(key)} = {26**len(key):,} possible keys")
```

### Example 5: Base64 Encoding (Not Encryption)
```python
import base64

def demonstrate_base64():
    """
    Demonstrates Base64 encoding (encoding, not encryption).
    Time Complexity: O(n) where n is the data length
    Note: Base64 is NOT encryption, it is merely encoding.
    """
    # Original data
    plaintext = "Secret Message"
    
    # Encode to Base64
    encoded = base64.b64encode(plaintext.encode()).decode()
    
    # Decode from Base64
    decoded = base64.b64decode(encoded).decode()
    
    print("Base64 Encoding Demonstration:")
    print(f"Original: {plaintext}")
    print(f"Encoded: {encoded}")
    print(f"Decoded: {decoded}")
    print("\nWarning: Base64 is NOT encryption!")
    print("It provides no security and can be easily reversed.")
    
    # Demonstrate multiple encoding
    double_encoded = base64.b64encode(encoded.encode()).decode()
    print(f"\nDouble Encoded: {double_encoded}")

demonstrate_base64()
```

### Example 6: XOR Cipher
```python
def xor_cipher(data, key):
    """
    Implements XOR cipher (symmetric operation).
    Time Complexity: O(n) where n is the data length
    Note: Same function encrypts and decrypts.
    """
    result = bytearray()
    key_bytes = key.encode() if isinstance(key, str) else key
    
    for i, byte in enumerate(data if isinstance(data, bytes) else data.encode()):
        # XOR with repeating key
        result.append(byte ^ key_bytes[i % len(key_bytes)])
    
    return bytes(result)

# Encryption and decryption
plaintext = "SENSITIVE DATA"
key = "SECRET"

# Encrypt
ciphertext = xor_cipher(plaintext, key)
print(f"Plaintext: {plaintext}")
print(f"Key: {key}")
print(f"Ciphertext (hex): {ciphertext.hex()}")

# Decrypt (same operation)
decrypted = xor_cipher(ciphertext, key).decode()
print(f"Decrypted: {decrypted}")

# Demonstrate weakness: known plaintext attack
print("\nKnown Plaintext Attack:")
known_plain = "SENSITIVE"
recovered_key = xor_cipher(known_plain, ciphertext[:len(known_plain)])
print(f"Recovered key fragment: {recovered_key.decode()}")
```

### Example 7: Key Space Calculator
```python
import math

def calculate_key_space(key_length, charset_size=26):
    """
    Calculates the key space and brute force difficulty.
    Time Complexity: O(1)
    """
    key_space = charset_size ** key_length
    
    # Estimate time to brute force at different speeds
    attempts_per_second = {
        'Manual': 1,
        'Python Script': 1_000_000,
        'GPU Cluster': 1_000_000_000_000
    }
    
    print(f"Key Length: {key_length}")
    print(f"Character Set Size: {charset_size}")
    print(f"Total Key Space: {key_space:,}\n")
    
    print("Brute Force Time Estimates:")
    print(f"{'Method':<15} {'Attempts/sec':<15} {'Time to Break'}")
    print("-" * 55)
    
    for method, speed in attempts_per_second.items():
        seconds = key_space / speed
        
        if seconds < 60:
            time_str = f"{seconds:.2f} seconds"
        elif seconds < 3600:
            time_str = f"{seconds/60:.2f} minutes"
        elif seconds < 86400:
            time_str = f"{seconds/3600:.2f} hours"
        elif seconds < 31536000:
            time_str = f"{seconds/86400:.2f} days"
        else:
            years = seconds / 31536000
            if years > 1e10:
                time_str = f"{years:.2e} years"
            else:
                time_str = f"{years:,.0f} years"
        
        print(f"{method:<15} {speed:>14,} {time_str}")

# Examples
print("Caesar Cipher (26 possible keys):")
calculate_key_space(1, 26)

print("\n" + "="*55)
print("\nModern 128-bit Key:")
calculate_key_space(128, 2)
```


## References

* [TryHackMe - Cryptography Basics](https://tryhackme.com/room/cryptographyintro) — Comprehensive introduction to fundamental cryptographic concepts.
* [TryHackMe - Crack the Hash](https://tryhackme.com/room/crackthehash) — Practical hash cracking exercises and tools.
* [TryHackMe - John The Ripper](https://tryhackme.com/room/johntheripper0) — Password cracking using John the Ripper tool.
* [Practical Cryptography](http://practicalcryptography.com/) — Interactive demonstrations of classical and modern ciphers.
* [The Code Book](https://simonsingh.net/books/the-code-book/) — Simon Singh, historical perspective on cryptography evolution.
* [Cryptool](https://www.cryptool.org/) — Educational software for learning cryptographic concepts.
# Block Cipher Modes of Operation & Padding Schemes

## 📋 Summary

* **Core Concept:** A block cipher (e.g., AES) encrypts exactly one fixed-size block at a time. A **mode of operation** defines how to apply the cipher repeatedly and securely across multiple blocks of arbitrary-length data. A **padding scheme** defines how to extend plaintext to a multiple of the block size when the data does not align naturally.

> **Takeaways:** The choice of mode is a security-critical decision — it is not merely a performance consideration. ECB is structurally broken and must never be used. CBC is widely deployed but misuse-prone (padding oracle attacks, no parallelism). CTR converts a block cipher into a stream cipher and enables parallelism, but provides no authentication. GCM extends CTR with a GHASH authenticator, providing Authenticated Encryption with Associated Data (AEAD) — the modern standard. PKCS#7 padding is the canonical scheme for block-aligned padding and is the source of the CBC Padding Oracle attack class if decryption errors are exposed to an attacker.

---

## 📖 Definitions

* **Mode of Operation:** A standardized algorithm that specifies how a block cipher is applied iteratively to encrypt or decrypt a message longer than a single block, governing the relationship between successive blocks.
* **Initialization Vector (IV):** A non-secret, typically random or unique value used to initialize a mode's state. An IV ensures that encrypting the same plaintext twice under the same key produces different ciphertexts. Its required properties (random vs. unique vs. nonce) vary by mode.
* **Nonce (Number Used Once):** A value that must be unique per encryption operation under a given key, but need not be unpredictable. Nonces are used in CTR and GCM; reuse is catastrophic.
* **AEAD (Authenticated Encryption with Associated Data):** An encryption scheme that simultaneously provides confidentiality, integrity, and authenticity. The ciphertext cannot be decrypted if it has been tampered with. GCM is the most widely deployed AEAD construction.
* **Authentication Tag:** A fixed-size value (typically 128 bits in GCM) appended to the ciphertext that acts as a MAC (Message Authentication Code). Verification of the tag must occur before decryption is accepted.
* **Padding Oracle:** A side-channel vulnerability in which a system leaks whether decrypted ciphertext has valid padding (e.g., via different error messages or timing). An attacker can exploit this to decrypt arbitrary ciphertext without the key — one block at a time.
* **PKCS#7 Padding:** A deterministic padding scheme where $k$ bytes of padding are appended, each with value $k$, to extend a plaintext to the next multiple of the block size. If the plaintext is already block-aligned, a full block of padding is added.
* **Malleability:** A property of a ciphertext whereby an attacker can make predictable modifications to the ciphertext that produce predictable changes in the decrypted plaintext — without knowing the key. ECB and unauthenticated CBC are both malleable.
* **Requirements:**
    * All modes require a block cipher (e.g., AES) with a fixed block size $b$ (128 bits for AES).
    * IV/nonce must never be reused under the same key in CTR or GCM — nonce reuse in GCM directly exposes the authentication key.
    * CBC requires a random (unpredictable) IV; reuse leaks whether two plaintexts share a common prefix.
    * GCM authentication tags must be verified before any plaintext is released or acted upon.
    * PKCS#7 padding must be validated and removed upon decryption; padding errors must not be distinguishable from other decryption errors.

---

## 1. ECB — Electronic Codebook Mode

### ⚙️ How It Works

Each plaintext block is encrypted **independently** with the same key. No IV is used. No state is carried between blocks.

$$C_i = E_K(P_i), \quad D_i = D_K(C_i)$$

1. **Step 1:** Divide the padded plaintext into $n$ blocks of $b$ bits each.
2. **Step 2:** Encrypt each block independently: $C_i = E_K(P_i)$.
3. **Step 3:** Concatenate ciphertext blocks: $C = C_1 \| C_2 \| \cdots \| C_n$.
4. **Step 4 (Decryption):** Each block is decrypted independently: $P_i = D_K(C_i)$.

**Critical structural weakness — ECB Penguin Problem:**
Identical plaintext blocks always produce identical ciphertext blocks. If the plaintext has repeating structure (e.g., an image with large uniform regions, a database with repeating field patterns), the structure is preserved in the ciphertext and is directly visible to an attacker.

$$P_i = P_j \implies C_i = C_j \quad \text{(for any } i \neq j\text{)}$$

### ❓ Why ECB Must Never Be Used

* **No semantic security:** Identical plaintext blocks produce identical ciphertext blocks, revealing plaintext structure completely.
* **Block-level replay attacks:** An attacker can reorder, duplicate, or delete individual ciphertext blocks without detection.
* **No diffusion across blocks:** A change in block $i$ affects only $C_i$ — no other block is affected.
* **No authentication:** Ciphertexts are fully malleable.

**ECB has no legitimate use case in modern systems.** It is covered here solely because it appears in legacy code, CTF challenges, and as a baseline to understand why chaining was introduced.

---

## 2. CBC — Cipher Block Chaining Mode

### ⚙️ How It Works

Each plaintext block is XORed with the **previous ciphertext block** before encryption. The first block is XORed with the IV. This creates a dependency chain: each ciphertext block depends on all preceding plaintext blocks.

$$C_i = E_K(P_i \oplus C_{i-1}), \quad C_0 = \text{IV}$$
$$P_i = D_K(C_i) \oplus C_{i-1}, \quad C_0 = \text{IV}$$

1. **Step 1:** Generate a random 128-bit IV.
2. **Step 2:** XOR $P_1$ with the IV, then encrypt: $C_1 = E_K(P_1 \oplus \text{IV})$.
3. **Step 3:** For each subsequent block: $C_i = E_K(P_i \oplus C_{i-1})$.
4. **Step 4 (Decryption):** $P_i = D_K(C_i) \oplus C_{i-1}$. Decryption of block $i$ requires only $C_i$ and $C_{i-1}$ — blocks can be decrypted in parallel.

**Key asymmetry — encryption is sequential; decryption is parallelizable.**

### 📊 Complexity

| Property | Value |
| :--- | :--- |
| Encryption | Sequential only — $O(n)$ with no parallelism |
| Decryption | Parallelizable — $O(n/p)$ with $p$ processors |
| IV requirement | Random and unpredictable (not just unique) |
| Error propagation | 1-bit flip in $C_i$ corrupts $P_i$ entirely and flips the same bit in $P_{i+1}$ |

### ❓ Weaknesses of CBC

* **Padding Oracle (POODLE, BEAST, Lucky13):** If a system reveals whether the decrypted padding is valid (via different errors or timing), an attacker can decrypt the entire ciphertext byte-by-byte without the key. This is a **chosen-ciphertext attack** that requires only the ability to submit modified ciphertexts and observe the response.
* **IV reuse:** Reusing an IV under the same key leaks whether two messages share a common plaintext prefix.
* **Sequential encryption:** Cannot be parallelized during encryption, which is a throughput bottleneck.
* **No authentication:** CBC alone provides only confidentiality. Ciphertexts can be tampered with. Must be combined with a MAC (e.g., HMAC-SHA256) in Encrypt-then-MAC construction — never MAC-then-Encrypt.

---

## 3. CTR — Counter Mode

### ⚙️ How It Works

CTR converts a block cipher into a **stream cipher** by encrypting successive values of a counter and XORing the output with the plaintext. The counter is typically formed as $\text{nonce} \| \text{counter}$, where the nonce is unique per message and the counter increments per block.

$$C_i = P_i \oplus E_K(\text{nonce} \| i)$$
$$P_i = C_i \oplus E_K(\text{nonce} \| i)$$

Encryption and decryption are **identical operations** (both are XOR with the keystream).

1. **Step 1:** Choose a unique nonce for this encryption operation. Concatenate it with a counter starting at 0: $\text{CTR}_i = \text{nonce} \| i$.
2. **Step 2:** Encrypt the counter block: $\text{keystream}_i = E_K(\text{CTR}_i)$.
3. **Step 3:** XOR with plaintext: $C_i = P_i \oplus \text{keystream}_i$.
4. **Step 4:** Increment the counter for each block. **The nonce must never be reused under the same key.**
5. **Step 5 (Decryption):** Regenerate the same keystream and XOR with the ciphertext — identical to encryption.

**Keystream precomputation:** Since keystream generation does not depend on the plaintext, it can be precomputed and parallelized fully.

### 📊 Complexity

| Property | Value |
| :--- | :--- |
| Encryption | Fully parallelizable — $O(n/p)$ with $p$ processors |
| Decryption | Fully parallelizable — $O(n/p)$ |
| Random access | $O(1)$ — seek to any block by computing $E_K(\text{nonce} \| i)$ directly |
| Padding required | No — CTR produces a keystream of arbitrary length |
| Nonce reuse consequence | **Catastrophic** — XOR of two ciphertexts cancels the keystream: $C_1 \oplus C_2 = P_1 \oplus P_2$ |

### ❓ Why CTR Is Used

* **No padding required:** The keystream can be truncated to match the plaintext length exactly.
* **Full parallelism:** Both encryption and decryption are embarrassingly parallel — ideal for multi-core and hardware implementations.
* **Random access:** Individual blocks can be decrypted in $O(1)$ without decrypting all preceding blocks — useful for encrypted file systems and databases.
* **No authentication:** CTR alone provides only confidentiality. Ciphertexts are malleable (a flip in $C_i$ flips the same bit in $P_i$). Must be combined with a MAC or replaced by GCM.

---

## 4. GCM — Galois/Counter Mode

### ⚙️ How It Works

GCM = CTR mode encryption + **GHASH** authentication. It provides AEAD: the ciphertext is authenticated with a polynomial MAC computed over $GF(2^{128})$. Additional data (AAD) — such as headers, IP addresses, or metadata — can be authenticated without being encrypted.

$$C_i = P_i \oplus E_K(\text{nonce} \| i+1)$$
$$\text{Tag} = \text{GHASH}_H(AAD, C) \oplus E_K(\text{nonce} \| 0)$$

where $H = E_K(0^{128})$ is the GHASH key derived from the encryption key.

1. **Step 1:** Derive the GHASH key: $H = E_K(0^{128})$.
2. **Step 2:** Encrypt plaintext blocks using CTR mode starting at counter 2 (counter 1 is reserved for the tag).
3. **Step 3:** Compute GHASH over the AAD and ciphertext: a polynomial evaluation in $GF(2^{128})$ using $H$.
4. **Step 4:** XOR the GHASH output with $E_K(\text{nonce} \| 1)$ to produce the 128-bit authentication tag.
5. **Step 5 (Decryption):** Verify the authentication tag **before** decrypting. If the tag is invalid, discard the ciphertext and return an error — never return partially decrypted data.

$$T(n) \approx c \cdot \frac{n}{128} \quad \text{(linear in blocks; fully parallelizable)}$$

### 📊 Complexity

| Property | Value |
| :--- | :--- |
| Encryption + authentication | $O(n)$, fully parallelizable |
| Decryption + verification | $O(n)$, fully parallelizable |
| Authentication tag size | 128 bits (96-bit also supported, less secure) |
| Nonce size | 96 bits (recommended); other sizes require extra hashing |
| Nonce reuse consequence | **Catastrophic** — exposes GHASH key $H$, enabling full forgery of future messages |

### ❓ Why GCM Is the Modern Standard

* **AEAD in a single pass:** Confidentiality and integrity are achieved together without a separate MAC computation step.
* **AAD support:** Metadata (e.g., packet headers) can be integrity-protected without being encrypted — essential for network protocols.
* **No padding:** Inherited from CTR mode.
* **Hardware acceleration:** AES-NI + CLMUL (carry-less multiply) instructions implement GCM at line rate on modern CPUs.
* **Universal adoption:** GCM is mandatory in TLS 1.3, IPsec, SSH, and QUIC. It is the default AEAD in most modern TLS cipher suites.
* **Nonce misuse resistance:** Standard GCM is not nonce-misuse resistant. For contexts where nonce uniqueness cannot be guaranteed, AES-GCM-SIV (RFC 8452) should be used instead.

---

## 5. PKCS#7 Padding

### ⚙️ How It Works

PKCS#7 (defined in RFC 5652) pads a plaintext to the next multiple of the block size $b$ by appending $k$ bytes, each with value $k$, where:

$$k = b - (|P| \bmod b), \quad k \in \{1, 2, \ldots, b\}$$

If the plaintext length is already a multiple of $b$, a full block of $b$ bytes (each with value $b$) is appended. This ensures unambiguous removal of padding upon decryption.

**Examples for AES (b = 16 bytes):**

| Plaintext length | Bytes needed | Padding appended |
| :--- | :--- | :--- |
| 13 bytes | 3 | `\x03\x03\x03` |
| 15 bytes | 1 | `\x01` |
| 16 bytes | 16 | `\x10\x10...\x10` (16 bytes) |
| 20 bytes | 12 | `\x0c\x0c...\x0c` (12 bytes) |

**Padding removal (unpadding):**
1. Read the value of the last byte: $k = P[-1]$.
2. Verify that the last $k$ bytes all equal $k$. If not, the padding is invalid.
3. Remove the last $k$ bytes.

**Invalid padding examples (must be rejected):**
- `\x05\x05\x05\x05\x06` — last byte is 6 but only 4 bytes of value 6 are not present.
- `\x00` — a padding value of 0 is invalid (padding must be at least 1 byte).
- `\x11` — a padding value exceeding the block size is invalid.

### ❓ The Padding Oracle Attack (CBC-PKCS#7)

The Padding Oracle attack (Vaudenay, 2002) exploits a system that leaks padding validity through distinct error responses or timing differences during CBC decryption.

**Attack mechanism (single-byte recovery):**
Given ciphertext block $C_{i-1}$ and $C_i$, the attacker wants to recover $P_i$.

During decryption: $P_i = D_K(C_i) \oplus C_{i-1}$.

Let $I_i = D_K(C_i)$ (the intermediate value the attacker cannot see). The attacker constructs a modified block $C'_{i-1}$ and submits $C'_{i-1} \| C_i$ to the oracle. By flipping bytes in $C'_{i-1}$ and observing whether the oracle accepts the padding:

$$P_i[j] = I_i[j] \oplus C_{i-1}[j]$$

The attacker recovers $I_i[j]$ by finding the value of $C'_{i-1}[j]$ that makes the decrypted padding valid (e.g., `\x01` for the last byte). This requires at most 256 queries per byte, or $256 \times 16 = 4096$ queries per 16-byte block.

**Mitigations:**
- Use GCM (AEAD) instead of CBC+PKCS#7.
- If CBC is required: use Encrypt-then-MAC, verify the MAC before decrypting, and return a **single, identical** error for all failure modes (padding invalid, MAC invalid, decryption error).
- Use constant-time padding validation to eliminate timing oracles.

---

## 📊 Mode Comparison

| Property | ECB | CBC | CTR | GCM |
| :--- | :--- | :--- | :--- | :--- |
| **Encryption parallelism** | Yes | No | Yes | Yes |
| **Decryption parallelism** | Yes | Yes | Yes | Yes |
| **Random access** | Yes | No | Yes | Yes |
| **Requires IV/nonce** | No | Yes (random) | Yes (unique) | Yes (unique) |
| **Padding required** | Yes | Yes | No | No |
| **Provides authentication** | No | No | No | Yes |
| **Ciphertext malleable** | Yes | Yes | Yes | No |
| **Identical blocks leak info** | Yes | No | No | No |
| **Nonce reuse consequence** | N/A | IV reuse leaks prefix | Full keystream exposure | GHASH key exposed; forgery possible |
| **Security recommendation** | Never use | Legacy only | Use with MAC | Preferred |

---

## 💻 Usage / Example

```python
# Modes of Operation & PKCS#7 Padding — Full Demonstration
# pip install pycryptodome

from Crypto.Cipher import AES
from Crypto.Util.Padding import pad, unpad
from Crypto.Random import get_random_bytes
import os


BLOCK_SIZE = 16  # AES block size: 128 bits


# ─────────────────────────────────────────────
# PKCS#7 Padding — manual implementation
# ─────────────────────────────────────────────
def pkcs7_pad(data: bytes, block_size: int = BLOCK_SIZE) -> bytes:
    """Append k bytes of value k to reach the next block boundary.
    If already aligned, append a full block of padding.
    Complexity: O(1) — appends at most block_size bytes.
    """
    k = block_size - (len(data) % block_size)
    return data + bytes([k] * k)


def pkcs7_unpad(data: bytes, block_size: int = BLOCK_SIZE) -> bytes:
    """Remove and validate PKCS#7 padding.
    Raises ValueError for invalid padding — used by padding oracle mitigations.
    """
    if len(data) == 0 or len(data) % block_size != 0:
        raise ValueError("Invalid data length")
    k = data[-1]
    if k == 0 or k > block_size:
        raise ValueError(f"Invalid padding byte: {k}")
    if data[-k:] != bytes([k] * k):
        raise ValueError("Padding bytes are inconsistent")
    return data[:-k]


# Demonstrate padding
messages = [b"Hello, World!", b"AAAAAAAAAAAAAAAA", b"Hi"]
print("─── PKCS#7 Padding (block_size=16) ───")
for msg in messages:
    padded = pkcs7_pad(msg)
    k = padded[-1]
    print(f"  '{msg.decode()}' ({len(msg)}B)  →  padded ({len(padded)}B)  "
          f"padding: {k} × \\x{k:02x}")
    assert pkcs7_unpad(padded) == msg
print()


# ─────────────────────────────────────────────
# 1. ECB Mode — NEVER use in production
# ─────────────────────────────────────────────
def ecb_demo(plaintext: bytes, key: bytes) -> bytes:
    """ECB: each block encrypted independently.
    Identical plaintext blocks → identical ciphertext blocks.
    No IV required; fully parallelizable; structurally broken.
    """
    cipher = AES.new(key, AES.MODE_ECB)
    return cipher.encrypt(pad(plaintext, BLOCK_SIZE))


key = get_random_bytes(32)  # AES-256

# Demonstrate ECB pattern leak
repeated_blocks = b"AAAAAAAAAAAAAAAA" * 3  # 3 identical 16-byte blocks
ecb_ct = ecb_demo(repeated_blocks, key)
block_hex = [ecb_ct[i:i+16].hex() for i in range(0, 48, 16)]
print("─── ECB Weakness: Identical blocks → identical ciphertext ───")
for i, h in enumerate(block_hex):
    print(f"  Block {i+1}: {h}")
print(f"  Blocks identical: {block_hex[0] == block_hex[1] == block_hex[2]}")
print("  ⚠ Structure preserved — ECB must never be used.\n")


# ─────────────────────────────────────────────
# 2. CBC Mode — legacy, requires random IV
# ─────────────────────────────────────────────
def cbc_encrypt(plaintext: bytes, key: bytes) -> tuple[bytes, bytes]:
    """CBC: XOR each block with the previous ciphertext before encrypting.
    Requires random, unpredictable IV. Encryption is sequential.
    Returns (ciphertext, iv).
    """
    iv = get_random_bytes(BLOCK_SIZE)  # Must be random and unique
    cipher = AES.new(key, AES.MODE_CBC, iv)
    return cipher.encrypt(pad(plaintext, BLOCK_SIZE)), iv


def cbc_decrypt(ciphertext: bytes, key: bytes, iv: bytes) -> bytes:
    """CBC decryption is parallelizable (depends only on C_i and C_{i-1})."""
    cipher = AES.new(key, AES.MODE_CBC, iv)
    return unpad(cipher.decrypt(ciphertext), BLOCK_SIZE)


# Demonstrate IV randomness (same plaintext → different ciphertext)
msg = b"Sensitive payload"
ct1, iv1 = cbc_encrypt(msg, key)
ct2, iv2 = cbc_encrypt(msg, key)
print("─── CBC: Same plaintext, different IV → different ciphertext ───")
print(f"  CT1: {ct1.hex()}")
print(f"  CT2: {ct2.hex()}")
print(f"  Ciphertexts differ: {ct1 != ct2}")
recovered = cbc_decrypt(ct1, key, iv1)
print(f"  Decrypted: {recovered}\n")


# ─────────────────────────────────────────────
# 3. CTR Mode — stream cipher behaviour, no padding
# ─────────────────────────────────────────────
def ctr_encrypt(plaintext: bytes, key: bytes) -> tuple[bytes, bytes]:
    """CTR: encrypt a counter, XOR with plaintext.
    No padding needed. Fully parallelizable. Nonce must be unique per key.
    Returns (ciphertext, nonce).
    """
    nonce = get_random_bytes(BLOCK_SIZE)  # Unique nonce; must never repeat under same key
    cipher = AES.new(key, AES.MODE_CTR, nonce=nonce[:8], initial_value=nonce[8:])
    return cipher.encrypt(plaintext), nonce  # No padding — keystream matches plaintext length


def ctr_decrypt(ciphertext: bytes, key: bytes, nonce: bytes) -> bytes:
    cipher = AES.new(key, AES.MODE_CTR, nonce=nonce[:8], initial_value=nonce[8:])
    return cipher.decrypt(ciphertext)  # Identical to encryption


# Demonstrate CTR: no padding, arbitrary length, random access
arbitrary_len = b"No padding needed — 19 chars"
ct_ctr, nonce = ctr_encrypt(arbitrary_len, key)
print("─── CTR: No padding, arbitrary plaintext length ───")
print(f"  Plaintext length:   {len(arbitrary_len)} bytes")
print(f"  Ciphertext length:  {len(ct_ctr)} bytes  (equal — no padding added)")
print(f"  Decrypted: {ctr_decrypt(ct_ctr, key, nonce)}\n")


# ─────────────────────────────────────────────
# 4. GCM Mode — AEAD (recommended)
# ─────────────────────────────────────────────
def gcm_encrypt(plaintext: bytes, key: bytes,
                aad: bytes = b"") -> tuple[bytes, bytes, bytes]:
    """AES-GCM: CTR encryption + GHASH authentication.
    Provides confidentiality + integrity + authenticity in one pass.
    AAD is authenticated but NOT encrypted (e.g., packet headers).
    Returns (ciphertext, nonce, auth_tag).
    CRITICAL: Verify auth_tag before acting on any decrypted data.
    """
    nonce = get_random_bytes(12)  # 96-bit nonce; must be unique per key
    cipher = AES.new(key, AES.MODE_GCM, nonce=nonce)
    cipher.update(aad)
    ciphertext, tag = cipher.encrypt_and_digest(plaintext)
    return ciphertext, nonce, tag


def gcm_decrypt(ciphertext: bytes, key: bytes, nonce: bytes,
                tag: bytes, aad: bytes = b"") -> bytes:
    """GCM decryption: verifies auth tag BEFORE returning plaintext.
    Raises ValueError if the tag is invalid (tampered ciphertext/AAD).
    """
    cipher = AES.new(key, AES.MODE_GCM, nonce=nonce)
    cipher.update(aad)
    return cipher.decrypt_and_verify(ciphertext, tag)  # Tag verified first


secret   = b"Classified payload"
header   = b"msg-type:encrypted;version:1"  # AAD: authenticated, not encrypted
ct_gcm, nonce_gcm, tag_gcm = gcm_encrypt(secret, key, aad=header)

print("─── GCM: AEAD — authenticated encryption with associated data ───")
print(f"  Ciphertext:   {ct_gcm.hex()}")
print(f"  Auth tag:     {tag_gcm.hex()}")
print(f"  Decrypted:    {gcm_decrypt(ct_gcm, key, nonce_gcm, tag_gcm, header)}")

# Demonstrate tamper detection
tampered = bytes([ct_gcm[0] ^ 0xFF]) + ct_gcm[1:]  # Flip 1 bit
try:
    gcm_decrypt(tampered, key, nonce_gcm, tag_gcm, header)
except ValueError as e:
    print(f"  Tamper detected: {e}  ← GCM rejected modified ciphertext\n")


# ─────────────────────────────────────────────
# Summary: Complexity per mode for n bytes
# ─────────────────────────────────────────────
# ECB  — O(n): parallel encryption/decryption; no IV; broken (pattern leak)
# CBC  — O(n): sequential encryption, parallel decryption; random IV required
# CTR  — O(n): fully parallel; no padding; nonce reuse is catastrophic
# GCM  — O(n): fully parallel; AEAD; nonce reuse exposes GHASH key
```

---

## References

* [NIST SP 800-38A — Recommendation for Block Cipher Modes](https://doi.org/10.6028/NIST.SP.800-38A) — Formal specification of ECB, CBC, CFB, OFB, and CTR modes.
* [NIST SP 800-38D — Recommendation for GCM](https://doi.org/10.6028/NIST.SP.800-38D) — Full GCM specification including GHASH and tag generation.
* [RFC 5652 — Cryptographic Message Syntax (PKCS#7)](https://datatracker.ietf.org/doc/html/rfc5652) — Defines the PKCS#7 content type and padding scheme used in CMS.
* [RFC 8452 — AES-GCM-SIV](https://datatracker.ietf.org/doc/html/rfc8452) — Nonce-misuse resistant variant of GCM; recommended when nonce uniqueness cannot be guaranteed.
* [Vaudenay 2002 — Security Flaws Induced by CBC Padding](https://www.iacr.org/archive/eurocrypt2002/23320530/cbc02_e02d.pdf) — Original Padding Oracle attack paper.
* [SWEET32 — CVE-2016-2183](https://sweet32.info/) — Birthday-bound attack on 64-bit block ciphers in CBC and CTR modes.
* [Lucky Thirteen Attack](https://www.isg.rhul.ac.uk/tls/TLStiming.pdf) — Timing-based padding oracle in TLS CBC-HMAC-SHA1.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Chapter 3 (Private-Key Encryption and Pseudorandomness).
* *Cryptography and Network Security* — William Stallings, Chapter 6 (Block Cipher Operation — Modes of Operation).
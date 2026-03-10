# DES and AES — Symmetric Block Cipher Algorithms

## 📋 Summary

* **Core Concept:** DES (Data Encryption Standard) and AES (Advanced Encryption Standard) are both symmetric block ciphers — they use the same secret key to encrypt and decrypt fixed-size blocks of data. DES, designed in the 1970s, is now considered cryptographically broken due to its short 56-bit key. AES, standardized in 2001, replaced DES and remains the global standard for symmetric encryption today.

> **Takeaways:** DES should never be used in new systems. Its 56-bit effective key length makes it vulnerable to brute-force attacks; the EFF's "Deep Crack" machine broke it in 22 hours in 1999. Triple-DES (3DES) extended its life but has since been deprecated by NIST (2023). AES (Rijndael) was designed as a direct response to DES's weaknesses: it uses a 128-bit block size, supports 128/192/256-bit keys, and is built on a mathematically rigorous structure (Substitution-Permutation Network) with no known practical attacks against correctly implemented AES. Understanding DES is essential not because it should be used, but because its weaknesses define the design requirements that AES was built to satisfy.

---

## 1. DES — Data Encryption Standard

### 📖 Definition

* **DES:** A symmetric block cipher standardized by NIST (then NBS) in 1977, operating on 64-bit blocks with a 56-bit effective key (the remaining 8 bits are parity). Based on a Feistel Network structure with 16 rounds.
* **Feistel Network:** A symmetric structure that splits the block into two halves (L, R) and applies a round function $F$ iteratively: $L_{i+1} = R_i$, $R_{i+1} = L_i \oplus F(R_i, K_i)$. Decryption uses the same structure with the round keys reversed — no inverse of $F$ is required.
* **S-Box (Substitution Box):** A non-linear lookup table used inside the DES round function $F$. DES has 8 S-Boxes, each mapping 6 input bits to 4 output bits. The S-Boxes are the sole source of non-linearity and confusion in DES.
* **Key Schedule:** The process by which DES derives 16 subkeys $K_1$ through $K_{16}$, each 48 bits, from the 64-bit master key via permutation and rotation.
* **Triple-DES (3DES / TDEA):** An extension that applies DES three times: $C = E_{K_3}(D_{K_2}(E_{K_1}(P)))$. With three independent keys, the effective key length is 112 bits. Deprecated by NIST in 2023.
* **Requirements:**
    * 64-bit block size; plaintext must be padded to a multiple of 64 bits.
    * 56-bit effective key (64 bits with 8 parity bits, one per byte).
    * 16 Feistel rounds using the round function $F$, which involves expansion, XOR with subkey, S-Box substitution, and permutation.

### 📊 Complexity Analysis — DES

| Operation | Complexity | Notes |
| :--- | :--- | :--- |
| Single DES encrypt/decrypt | $O(1)$ | Fixed 16-round structure on a 64-bit block |
| DES over $n$ bytes of data | $O(n)$ | Each 64-bit block is processed independently |
| Brute-force key search | $O(2^{56})$ | $\approx 7.2 \times 10^{16}$ operations — feasible with dedicated hardware |
| Differential cryptanalysis | $O(2^{47})$ chosen plaintexts | Best theoretical attack; requires $2^{47}$ chosen pairs |
| Linear cryptanalysis | $O(2^{43})$ known plaintexts | Best known-plaintext attack against DES |

* **Worst-Case ($O$) — Brute Force:** $O(2^{56})$ key trials. A modern GPU cluster can exhaust this space in hours; dedicated ASICs can do it in seconds.
* **Best-Case ($\Omega$) — Encryption:** $\Omega(1)$ per block — each 64-bit block always requires exactly 16 rounds, regardless of the data.
* **Average-Case ($\Theta$) — Data encryption:** $\Theta(n / 64)$ block operations for $n$ bits of input, where each block operation is constant-time.

### ❓ Why DES Is No Longer Used (Weaknesses)

* **Short key length (56 bits):** The NSA reduced the originally proposed 64-bit key to 56 bits during standardization. This yields only $2^{56} \approx 7.2 \times 10^{16}$ possible keys — a search space that is trivially exhausted by modern hardware.
* **Small block size (64 bits):** A 64-bit block size leads to **birthday-bound collisions** after roughly $2^{32}$ blocks ($\approx$ 32 GB) of data encrypted under the same key, enabling the SWEET32 attack (CVE-2016-2183).
* **Differential cryptanalysis:** Biham and Shamir (1990) demonstrated that DES is susceptible to differential cryptanalysis, requiring $2^{47}$ chosen plaintext pairs — weaker than brute force but revealing that the S-Boxes were not designed to be maximally resistant.
* **Linear cryptanalysis:** Matsui (1993) showed DES can be broken with $2^{43}$ known plaintexts — the first experimental break of a full-round cipher.
* **Complementation property:** $E_{\bar{K}}(\bar{P}) = \overline{E_K(P)}$ — encrypting a bitwise complement of the plaintext with a complemented key yields a complemented ciphertext. This halves the effective brute-force search space to $2^{55}$.
* **Weak keys:** DES has 4 weak keys and 12 semi-weak keys for which the key schedule produces repeated subkeys, causing $E_K(E_K(P)) = P$ (double encryption is an identity). These must be explicitly excluded.
* **No authentication:** DES provides only confidentiality. Without a MAC or AEAD mode, ciphertexts are malleable.

### ⚙️ How DES Works

1. **Step 1 — Initial Permutation (IP):** The 64-bit plaintext block is rearranged by a fixed permutation table. This provides no security — it was included for ease of hardware loading.
2. **Step 2 — Key Schedule:** The 64-bit key (56 effective bits) is split and rotated to generate 16 × 48-bit subkeys $K_1, \ldots, K_{16}$.
3. **Step 3 — 16 Feistel Rounds:** The 64-bit block is split into $L_0$ (32 bits) and $R_0$ (32 bits). For each round $i$:
   - **Expansion (E):** $R_{i-1}$ is expanded from 32 to 48 bits by duplicating 16 bits.
   - **Key Mixing:** $E(R_{i-1}) \oplus K_i$ produces a 48-bit value.
   - **S-Box Substitution:** The 48 bits are split into 8 × 6-bit groups; each group is passed through one of 8 S-Boxes, producing 8 × 4-bit outputs (32 bits total). **This is the only non-linear step.**
   - **Permutation (P):** The 32-bit S-Box output is permuted by a fixed table.
   - **Round output:** $L_i = R_{i-1}$, $R_i = L_{i-1} \oplus F(R_{i-1}, K_i)$.
4. **Step 4 — Final Permutation (IP$^{-1}$):** The inverse of the initial permutation is applied to $R_{16} \| L_{16}$ to produce the ciphertext.

$$C = IP^{-1}(R_{16} \| L_{16}), \quad \text{where } R_i = L_{i-1} \oplus F(R_{i-1}, K_i)$$

---

## 2. AES — Advanced Encryption Standard (Rijndael)

### 📖 Definition

* **AES:** A symmetric block cipher selected by NIST in 2001 (FIPS 197) through an open international competition. Based on the Rijndael algorithm by Joan Daemen and Vincent Rijmen. Operates on 128-bit blocks with key sizes of 128, 192, or 256 bits.
* **Substitution-Permutation Network (SPN):** The architectural paradigm of AES. Unlike DES's Feistel structure, all bits of the block are transformed in each round using alternating layers of substitution and permutation applied to the entire block simultaneously.
* **State:** AES treats the 128-bit block as a 4×4 matrix of bytes (the "state array"). All round operations transform this matrix in-place.
* **AES S-Box:** A bijective (invertible) 8-bit to 8-bit substitution derived from the multiplicative inverse in $GF(2^8)$ followed by an affine transformation. Unlike DES S-Boxes, AES's S-Box has a rigorous algebraic definition and is designed to maximize resistance to differential and linear cryptanalysis.
* **$GF(2^8)$ (Galois Field):** The finite field over which AES arithmetic is performed. Addition is XOR; multiplication is polynomial multiplication modulo the irreducible polynomial $x^8 + x^4 + x^3 + x + 1$.
* **Round Key:** One of $N_r + 1$ derived 128-bit keys (where $N_r$ is the number of rounds), produced by the AES Key Expansion (key schedule).
* **Requirements:**
    * Fixed 128-bit block size.
    * Key size: 128 bits (AES-128, 10 rounds), 192 bits (AES-192, 12 rounds), or 256 bits (AES-256, 14 rounds).
    * Must be used with a secure mode of operation (ECB is insecure; prefer GCM or CBC with HMAC).

### 📊 Complexity Analysis — AES

| Operation | Complexity | Notes |
| :--- | :--- | :--- |
| AES-128 encrypt/decrypt (1 block) | $O(1)$ | Fixed 10-round SPN on 128-bit block |
| AES over $n$ bytes | $O(n)$ | Each 128-bit block is constant-time |
| Key expansion | $O(N_k)$ | $N_k$ = number of 32-bit words in key (4/6/8) |
| Best known attack (biclique) | $O(2^{126.2})$ | For AES-128; impractical, no operational threat |
| Quantum attack (Grover's) | $O(2^{64})$ for AES-128 | Reduces effective security by half; AES-256 still safe |

| AES Variant | Key Size | Rounds | Security Level | Quantum Security |
| :--- | :--- | :--- | :--- | :--- |
| AES-128 | 128 bits | 10 | 128-bit | 64-bit (Grover) |
| AES-192 | 192 bits | 12 | 192-bit | 96-bit (Grover) |
| AES-256 | 256 bits | 14 | 256-bit | 128-bit (Grover) |

* **Worst-Case ($O$) — Encryption:** $O(1)$ per block — AES-128 is always exactly 10 rounds; AES-256 is always 14 rounds. Input size does not affect per-block complexity.
* **Best-Case ($\Omega$):** $\Omega(1)$ — identical to worst-case; no early termination is possible or meaningful.
* **Average-Case ($\Theta$):** $\Theta(n)$ for $n$ bytes of data — linear in data size with a very small constant factor, especially with AES-NI hardware acceleration.

### ❓ Why We Use AES

* **Security margin:** No practical attack breaks AES below $O(2^{126})$ operations for AES-128. This is astronomically larger than what any classical computer can perform.
* **Hardware acceleration:** The AES-NI instruction set (Intel/AMD since ~2010; ARM since ARMv8) allows a single AES round to execute in 1–4 CPU cycles, making AES-GCM competitive with ChaCha20 on modern hardware.
* **Standardization and trust:** AES was selected through an open, peer-reviewed international competition. Unlike DES, the design criteria were made fully public.
* **Versatility:** AES supports three key sizes and all standard modes of operation (CBC, CTR, GCM, CCM, XTS). AES-GCM provides authenticated encryption (AEAD), combining encryption and integrity in a single operation.
* **Post-quantum relevance:** AES-256 retains 128-bit security even against Grover's quantum algorithm, making it suitable for long-term post-quantum use in symmetric encryption.

### ⚙️ How AES Works

AES operates on a 4×4 byte **state matrix** through $N_r$ rounds. Each full round (all rounds except the final) applies four transformations in sequence:

1. **Step 1 — Key Expansion:**
   The original key is expanded into $N_r + 1$ round keys using the Rijndael key schedule. Each round key is 128 bits (one full state matrix).

2. **Step 2 — Initial Round Key Addition (AddRoundKey):**
   Before the first round, the plaintext state is XORed with the first round key:
   $$\text{State} = \text{Plaintext} \oplus K_0$$

3. **Step 3 — Rounds 1 through $N_r - 1$ (Full Rounds):** Each round applies:

   **a) SubBytes** — Non-linear substitution. Each of the 16 bytes in the state is independently replaced using the AES S-Box (inverse in $GF(2^8)$ + affine transform). This provides **confusion**.

   **b) ShiftRows** — Linear row permutation. Row 0 is unchanged; Row 1 is rotated left by 1 byte; Row 2 by 2 bytes; Row 3 by 3 bytes. This ensures that each column of the output contains bytes from every column of the input, providing **inter-column diffusion**.

   **c) MixColumns** — Linear column mixing. Each column of 4 bytes is treated as a polynomial over $GF(2^8)$ and multiplied by a fixed matrix. This mixes bytes within each column and provides the primary **diffusion** within the cipher. The MDS (Maximum Distance Separable) property guarantees that any 1-byte difference in input produces at least 4 bytes changed in output.

   **d) AddRoundKey** — The current round key $K_i$ is XORed with the entire state:
   $$\text{State} = \text{State} \oplus K_i$$

4. **Step 4 — Final Round ($N_r$):** Same as above but **MixColumns is omitted** (its inclusion would not add security but would require extra inversion steps during decryption).

$$T(n) \approx c \cdot \frac{n}{128} \cdot N_r, \quad N_r \in \{10, 12, 14\}$$

**The two core security properties, achieved by the four transformations together:**

| Property | AES Layer Responsible |
| :--- | :--- |
| **Confusion** (key/ciphertext relationship is complex) | SubBytes (non-linear S-Box) |
| **Diffusion** (plaintext bits spread across ciphertext) | ShiftRows + MixColumns (together achieve full diffusion in 2 rounds) |

---

## 💻 Usage / Example

```python
# DES vs AES — Security and Performance Comparison
# pip install pycryptodome

from Crypto.Cipher import DES, AES
from Crypto.Util.Padding import pad, unpad
from Crypto.Random import get_random_bytes
import time
import os


# ─────────────────────────────────────────────
# WARNING: DES is cryptographically broken.
# This demonstration is for educational purposes only.
# NEVER use DES in production systems.
# ─────────────────────────────────────────────

def des_encrypt_decrypt(plaintext: bytes) -> dict:
    """DES CBC encryption/decryption (educational only).
    Key: 8 bytes (64 bits; only 56 bits are effective — 8 parity bits ignored).
    Block size: 8 bytes (64 bits).
    Rounds: 16 Feistel rounds.
    """
    key = get_random_bytes(8)   # 64-bit key (56-bit effective)
    iv  = get_random_bytes(8)   # 64-bit IV for CBC mode

    cipher_enc = DES.new(key, DES.MODE_CBC, iv)
    padded     = pad(plaintext, DES.block_size)  # PKCS#7 padding to 64-bit boundary
    ciphertext = cipher_enc.encrypt(padded)

    cipher_dec = DES.new(key, DES.MODE_CBC, iv)
    decrypted  = unpad(cipher_dec.decrypt(ciphertext), DES.block_size)

    return {
        "algorithm"  : "DES-CBC",
        "key_bits"   : 56,       # Effective key length (security-relevant)
        "block_bits" : 64,
        "rounds"     : 16,
        "ciphertext" : ciphertext.hex(),
        "decrypted"  : decrypted,
        "match"      : decrypted == plaintext,
        "vuln"       : "Brute-forceable in ~22 hours (EFF Deep Crack, 1999)"
    }


def aes_encrypt_decrypt(plaintext: bytes, key_bits: int = 256) -> dict:
    """AES-GCM authenticated encryption (recommended for production use).
    Key: 16/24/32 bytes → AES-128/192/256.
    Block size: 16 bytes (128 bits).
    Rounds: 10/12/14 depending on key size.
    GCM mode provides both confidentiality AND integrity (AEAD).
    """
    key_sizes = {128: 16, 192: 24, 256: 32}
    key_bytes = key_sizes[key_bits]
    rounds    = {128: 10, 192: 12, 256: 14}[key_bits]

    key   = get_random_bytes(key_bytes)
    nonce = get_random_bytes(12)   # 96-bit nonce for GCM
    aad   = b"authenticated_header"

    # Encrypt
    cipher_enc           = AES.new(key, AES.MODE_GCM, nonce=nonce)
    cipher_enc.update(aad)
    ciphertext, auth_tag = cipher_enc.encrypt_and_digest(plaintext)

    # Decrypt + verify authentication tag
    cipher_dec = AES.new(key, AES.MODE_GCM, nonce=nonce)
    cipher_dec.update(aad)
    decrypted  = cipher_dec.decrypt_and_verify(ciphertext, auth_tag)

    return {
        "algorithm"  : f"AES-{key_bits}-GCM",
        "key_bits"   : key_bits,
        "block_bits" : 128,
        "rounds"     : rounds,
        "ciphertext" : ciphertext.hex(),
        "auth_tag"   : auth_tag.hex(),
        "decrypted"  : decrypted,
        "match"      : decrypted == plaintext,
        "vuln"       : f"Best attack: O(2^{key_bits - 2}) — no practical threat"
    }


# ─────────────────────────────────────────────
# Run comparison
# ─────────────────────────────────────────────
message = b"Confidential message for cryptographic comparison."

print("=" * 60)
des_result = des_encrypt_decrypt(message)
for k, v in des_result.items():
    print(f"  {k:<12}: {v}")

print()
print("=" * 60)
for bits in [128, 192, 256]:
    aes_result = aes_encrypt_decrypt(message, key_bits=bits)
    for k, v in aes_result.items():
        print(f"  {k:<12}: {v}")
    print()


# ─────────────────────────────────────────────
# AES S-Box — inspect the non-linear substitution layer
# This is the core of AES's confusion property.
# ─────────────────────────────────────────────
AES_SBOX = [
    0x63,0x7c,0x77,0x7b,0xf2,0x6b,0x6f,0xc5,0x30,0x01,0x67,0x2b,0xfe,0xd7,0xab,0x76,
    0xca,0x82,0xc9,0x7d,0xfa,0x59,0x47,0xf0,0xad,0xd4,0xa2,0xaf,0x9c,0xa4,0x72,0xc0,
    0xb7,0xfd,0x93,0x26,0x36,0x3f,0xf7,0xcc,0x34,0xa5,0xe5,0xf1,0x71,0xd8,0x31,0x15,
    0x04,0xc7,0x23,0xc3,0x18,0x96,0x05,0x9a,0x07,0x12,0x80,0xe2,0xeb,0x27,0xb2,0x75,
    0x09,0x83,0x2c,0x1a,0x1b,0x6e,0x5a,0xa0,0x52,0x3b,0xd6,0xb3,0x29,0xe3,0x2f,0x84,
    0x53,0xd1,0x00,0xed,0x20,0xfc,0xb1,0x5b,0x6a,0xcb,0xbe,0x39,0x4a,0x4c,0x58,0xcf,
    0xd0,0xef,0xaa,0xfb,0x43,0x4d,0x33,0x85,0x45,0xf9,0x02,0x7f,0x50,0x3c,0x9f,0xa8,
    0x51,0xa3,0x40,0x8f,0x92,0x9d,0x38,0xf5,0xbc,0xb6,0xda,0x21,0x10,0xff,0xf3,0xd2,
    0xcd,0x0c,0x13,0xec,0x5f,0x97,0x44,0x17,0xc4,0xa7,0x7e,0x3d,0x64,0x5d,0x19,0x73,
    0x60,0x81,0x4f,0xdc,0x22,0x2a,0x90,0x88,0x46,0xee,0xb8,0x14,0xde,0x5e,0x0b,0xdb,
    0xe0,0x32,0x3a,0x0a,0x49,0x06,0x24,0x5c,0xc2,0xd3,0xac,0x62,0x91,0x95,0xe4,0x79,
    0xe7,0xc8,0x37,0x6d,0x8d,0xd5,0x4e,0xa9,0x6c,0x56,0xf4,0xea,0x65,0x7a,0xae,0x08,
    0xba,0x78,0x25,0x2e,0x1c,0xa6,0xb4,0xc6,0xe8,0xdd,0x74,0x1f,0x4b,0xbd,0x8b,0x8a,
    0x70,0x3e,0xb5,0x66,0x48,0x03,0xf6,0x0e,0x61,0x35,0x57,0xb9,0x86,0xc1,0x1d,0x9e,
    0xe1,0xf8,0x98,0x11,0x69,0xd9,0x8e,0x94,0x9b,0x1e,0x87,0xe9,0xce,0x55,0x28,0xdf,
    0x8c,0xa1,0x89,0x0d,0xbf,0xe6,0x42,0x68,0x41,0x99,0x2d,0x0f,0xb0,0x54,0xbb,0x16,
]

def subbytes_demo(state_byte: int) -> int:
    """Apply AES SubBytes to a single byte.
    The S-Box is derived from GF(2^8) multiplicative inverse + affine transform.
    Input: 0x00–0xFF  →  Output: non-linearly transformed byte.
    """
    return AES_SBOX[state_byte]

# Demonstrate the avalanche effect within SubBytes
print("SubBytes non-linearity (1-bit input difference → large output change):")
for pair in [(0x00, 0x01), (0x53, 0x52), (0xAB, 0xAA)]:
    a, b = pair
    sa, sb = subbytes_demo(a), subbytes_demo(b)
    diff = bin(sa ^ sb).count('1')
    print(f"  S({a:#04x})={sa:#04x}, S({b:#04x})={sb:#04x}  →  XOR diff: {sa^sb:#04x}  ({diff} bits flipped)")
```


## 🔍 DES vs AES — Design Comparison

| Property | DES | AES (Rijndael) |
| :--- | :--- | :--- |
| **Standardized** | 1977 (FIPS 46) | 2001 (FIPS 197) |
| **Block Size** | 64 bits | 128 bits |
| **Key Sizes** | 56 bits effective | 128 / 192 / 256 bits |
| **Rounds** | 16 | 10 / 12 / 14 |
| **Structure** | Feistel Network | Substitution-Permutation Network (SPN) |
| **Non-linearity source** | 8 × 6→4-bit S-Boxes | 16 × 8→8-bit S-Box (GF(2⁸) inverse) |
| **All bits transformed/round** | No (half-block only) | Yes (entire 128-bit state) |
| **Best known attack** | Brute force: $O(2^{56})$ | Biclique: $O(2^{126.2})$ |
| **SWEET32 vulnerability** | Yes (64-bit block) | No (128-bit block) |
| **Weak keys** | 4 weak, 12 semi-weak | None known |
| **Hardware acceleration** | Legacy only | AES-NI (Intel/AMD/ARM) |
| **Current NIST status** | Withdrawn (deprecated) | Current standard |
| **Post-quantum (AES-256)** | N/A | 128-bit security under Grover |


## References

* [NIST FIPS 197 — AES Standard](https://doi.org/10.6028/NIST.FIPS.197) — Full specification of the AES algorithm including SubBytes, ShiftRows, MixColumns, and key expansion.
* [NIST FIPS 46-3 — DES Standard (withdrawn)](https://csrc.nist.gov/publications/detail/fips/46/3/archive/1999-10-25) — The original DES specification, retained for historical reference.
* [NIST SP 800-131A Rev 2 — Transitioning Cryptographic Algorithms](https://doi.org/10.6028/NIST.SP.800-131Ar2) — Documents the deprecation of 3DES and the approved transition to AES.
* [EFF DES Cracker (Deep Crack)](https://w2.eff.org/Privacy/Cracking_DES/) — Documentation of the 1998 machine that broke DES in 56 hours; later improved to 22 hours.
* [SWEET32 Attack — CVE-2016-2183](https://sweet32.info/) — Birthday-bound attack on 64-bit block ciphers (DES, 3DES) in CBC mode.
* *Introduction to Modern Cryptography* — Jonathan Katz & Yehuda Lindell, Chapter 6 (Practical Constructions of Symmetric-Key Primitives).
* *Cryptography and Network Security* — William Stallings, Chapters 3–5 (DES, AES, and Block Cipher Operation).
* *The Design of Rijndael* — Joan Daemen & Vincent Rijmen (Springer, 2002) — The authoritative reference by AES's designers; covers GF(2⁸) arithmetic and the rationale behind each transformation.
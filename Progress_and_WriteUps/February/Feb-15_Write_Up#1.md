# Linux Basics

## 📋 Summary
* **Core Concept:** Linux is a Unix-like operating system kernel that serves as the foundation for various distributions, providing a command-line interface and multi-user environment for system management and software development.

> **Takeaways:** Linux is essential for system administration, cybersecurity operations, server management, and development environments. Understanding Linux fundamentals enables efficient navigation, file manipulation, process management, and system configuration through command-line tools.


## 📖 Definition

* **Linux:** An open-source operating system kernel created by Linus Torvalds, used as the foundation for various distributions (Ubuntu, Debian, Fedora, etc.).
* **Shell:** A command-line interface program that interprets user commands and executes them (common shells: bash, zsh, sh).
* **Distribution (Distro):** A complete operating system built on the Linux kernel, bundled with system utilities, package managers, and applications.
* **Root:** The superuser account with unrestricted access to all system files and commands.
* **File System Hierarchy:** The standardized directory structure in Linux, starting from the root directory (`/`).
* **Requirements:** 
    * Basic understanding of command-line interfaces
    * Familiarity with file system concepts
    * Knowledge of text editors (vim, nano, or VS Code via remote SSH)


## 📊 Common Commands Complexity

| Command Type | Time Complexity | Use Case |
| :--- | :--- | :--- |
| `ls`, `pwd`, `cd` | $O(1)$ | Directory navigation |
| `find` | $O(n)$ | File searching |
| `grep` | $O(n \cdot m)$ | Pattern matching in files |
| `sort` | $O(n \log n)$ | File sorting |

* **Command Execution:** Most basic commands execute in constant time relative to system calls.
* **File Operations:** Operations like search and pattern matching scale with file size and number of files.
* **System Monitoring:** Real-time monitoring tools (`top`, `htop`) run continuously with periodic updates.


## ❓ Why we use it

* **Development Environment:** Linux provides native support for C, C++, and Python development with powerful compilers (gcc, g++) and interpreters.
* **Cybersecurity Operations:** Most security tools (Wireshark, Metasploit, Nmap) are designed for Linux environments.
* **Server Infrastructure:** The majority of web servers, cloud platforms, and IoT devices run Linux.
* **System Control:** Command-line interface offers precise control over system resources, processes, and configurations.
* **Open Source:** Free to use, modify, and distribute with access to source code for learning and customization.


## ⚙️ How it works

1. **Boot Process:** System starts with BIOS/UEFI → Bootloader (GRUB) → Kernel initialization → Init system (systemd) → User space.
2. **Shell Interaction:** User enters commands → Shell interprets → Kernel executes → Output returns to shell.
3. **File System Structure:**
   * `/` — Root directory
   * `/home` — User directories
   * `/bin` — Essential binary executables
   * `/etc` — Configuration files
   * `/var` — Variable data (logs, temporary files)
   * `/usr` — User programs and libraries
4. **Permission System:** Each file has owner, group, and other permissions (read, write, execute).
5. **Process Management:** Kernel manages processes, memory allocation, and CPU scheduling.


## 💻 Usage / Program Example

```bash
# Basic Navigation
pwd                    # Print working directory
ls -la                 # List all files with details
cd /home/user/project  # Change directory

# File Operations
mkdir my_project       # Create directory
touch main.c          # Create empty file
cp source.py dest.py  # Copy file
mv old.txt new.txt    # Move/rename file
rm file.txt           # Remove file
rm -r directory/      # Remove directory recursively

# File Permissions
chmod 755 script.sh   # Set permissions (rwxr-xr-x)
chown user:group file # Change ownership

# Text Processing
cat file.txt          # Display file contents
grep "error" log.txt  # Search for pattern
head -n 10 file.txt   # Show first 10 lines
tail -f log.txt       # Monitor file in real-time

# Process Management
ps aux                # List all processes
top                   # Interactive process viewer
kill -9 PID           # Terminate process
bg                    # Send process to background
fg                    # Bring process to foreground

# System Information
uname -a              # System information
df -h                 # Disk usage
free -h               # Memory usage
whoami                # Current user

# Package Management (Ubuntu/Debian)
sudo apt update       # Update package list
sudo apt install gcc  # Install package
sudo apt remove pkg   # Remove package

# Networking
ping google.com       # Test connectivity
ifconfig              # Network interface info
netstat -tuln         # List open ports
ssh user@host         # Remote login
```

```python
# Example: Running Linux commands from Python
import os
import subprocess

# Execute shell command
result = subprocess.run(['ls', '-l'], capture_output=True, text=True)
print(result.stdout)

# Get current directory
current_dir = os.getcwd()
print(f"Current directory: {current_dir}")

# Create directory
os.makedirs('my_project', exist_ok=True)

# File operations
with open('config.txt', 'w') as f:
    f.write("Settings configuration\n")
```

## References

* [Linux Documentation Project](https://tldp.org/) — Comprehensive guides and how-tos for Linux systems.
* [GNU Coreutils Manual](https://www.gnu.org/software/coreutils/manual/) — Official documentation for core Linux utilities.
* [The Linux Command Line](https://linuxcommand.org/) — William Shotts, beginner-friendly introduction to command-line usage.
* [Linux System Programming](https://man7.org/tlpi/) — Michael Kerrisk, advanced system-level programming concepts.